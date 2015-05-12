---
layout : life
title : Rails Xinge Push
category : Ruby On Rails
duoshuo: true
date : 2015-05-10
---

###简介
>
- _**[信鸽（XG Push）][1]**_:_是一款专业的免费移动 App 推送平台，支持百亿级的通知/消息推送，秒级触达移动用户，现已全面支持 Android 和 iOS 两大主流平台。
信鸽推送平台是腾讯旗下的一个产品，相信在并发，可靠性和稳定性方面，经得住考验。。_

---------------------------------------

###使用
>
- _**首先使用QQ号登录/注册信鸽,然后创建iOS/Android应用,由于信鸽区分开发环境和测试环境所以需要创建两个应用。
应用创建完成之后,将开发相关同事的 QQ 号，添加进应用的管理员。为了实现消息的推送,需要 iOS 开发者,制作证书,并上传至信鸽平台,交由信鸽托管,以此来对接苹果的消息中心。**_

>
![页面结果](/res/img/blog/ruby_on_rails/create_app.jpg)

<!-- more -->

- _**配置: 将信鸽内创建的应用配置信息：Access ID 和 Secret Key，放入 Rails 的配置项内。**_
>
![页面结果](/res/img/blog/ruby_on_rails/key_info.jpg)
>
![页面结果](/res/img/blog/ruby_on_rails/rails_setting.png)


- _**代码**_


### 对接信鸽接口，给设备 Push 消息通知

![页面结果](/res/img/blog/ruby_on_rails/xinge.png)

> _**在当前项目创建Xinge 的模块**_

> _**在当前项目xinge/base.rb文件实现对接信鸽的接口**_

[可以参考一个ralis项目的实现][2]

```sh

module Xinge
  class Base

    class << self

      # 打印出推送日志
      def logger
        Yell.new do |l|
          l.adapter :datefile, "#{Settings.log_path}/push.log", level: 'gte.info'
        end
      end

      # push 消息（包括通知和透传消息）给单个设备
      def push_single_device(device_token, message, params = {})
        params.merge!({
          device_token: device_token,
          message: message,
          message_type: 1
          })
        send_request('push', 'single_device', params)
      end

      protected

      def send_request(type, method, params = {})
        request_path = get_request_url(type, method)
        params.merge!({access_id: Settings.xinge_access_id, timestamp: Time.now.to_i})

        # sort params and calculate sign
        params_string = params.sort.map{ |h| h.join('=') }.join
        sign_str = "POST#{Settings.xinge_host}#{request_path}#{params_string}#{Settings.xinge_secret_key}"
        sign = Digest::MD5.hexdigest(sign_str)
        params.merge!({ sign: sign })

        begin
          logger.info "xinge start push: #{params}"
          request_url = "#{Settings.xinge_api_url}#{request_path}"
          reuqest_header = {'Content-Type' => 'application/x-www-form-urlencoded'}
          response = RestClient.post(request_url, params, reuqest_header)
          if response.present?
            result = JSON.parse(response)
            logger.info "xinge end push: #{result}"
            if result
              [result["ret_code"], result["err_msg"]]
            end
          end
        rescue => e
          puts e.response
          return false
        end
      end

      def get_request_url(type, method)
        "/v2/#{type}/#{method}"
      end

    end

  end
end
```

> _**在当前项目xinge/ios.rb文件实现ios对接信鸽的接口**_

```
module Xinge
  class Ios < Base

    class << self

      def push_to_single_device(device_token, title, content)
        push_single_device(device_token, build_simple_message(title, content),
          environment_param)
      end

      protected

      def environment_param
        env_val = Rails.env.production? ? 1 : 2
        { environment: env_val }
      end

      def build_simple_message(title, content)
        {
          aps: {
            alert: {
              title: title,
              body: content
            },
            sound: 'default',
            badge: 5
          }
        }.to_json
      end

    end

  end
end
```

> _**最后在xinge/notification_push.rb文件实现信鸽推送**_

```
module Xinge
  class NotificationPush

    class << self

      def channel_reply_push(xxxx)
        content = "#{xxxx.to_user.name}: 对您发表了新的评论，快去看看吧~"
        do_push(xxxx.to_user_id, content)
      end

      def do_push(user_id, content)
        title = "xxxxxx"
        device = UserDevice.notification_device(user_id)
        if device.present?
          if device.is_ios?
            Xinge::Ios.push_to_single_device(device.device_token, title, content)
          else
            Xinge::Android.push_to_single_device(device.device_token, title, content)
          end
        end
      end

    end

  end
end
```

[1]:http://xg.qq.com
[2]:https://github.com/RobotJiang/ruby-for-xinge
