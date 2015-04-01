---
layout : life
title : Ruby Geocoder
category : Ruby On Rails
duoshuo: true
date : 2015-04-01
---

###简介
>
- _**地理编码**_:_是指将地址信息和地理坐标点信息进行相互转化，包括将地址信息转换为地理坐标点的编码和将地理坐标点转换为地址信息的逆地理编码。_
- _**逆地理编码**_:_逆地理编码又称位置描述或地址解析，即从已知的经纬度坐标到对应的地址描述（如省市、街区、楼层、房间等）的转换。_

---------------------------------------

>
- _**使用**_ `response = Net::HTTP.get_response(URI.parse(URI.encode(url)))` *由于谷歌被封可能需要翻墙!*
- _**使用高德JavaScript API**_
- _**使用百度 Geocoding API--Web服务API**_

---------------------------------------

<!-- more -->

### 高德 --- JavaScript API

**AMap.Geocoder:** 可以将地址信息转换为地理坐标点的编码和将地理坐标点转换为地址信息的逆地理编码。除地址信息/地理坐标点信息外，服务还返回包括地址描述/地理坐标点所在商圈，周边的POI、道路、道路交叉口等信息。

**[详细介绍链接][1]**

> _**不过好像服务器端通过地址获取经纬度只能通过调用JavaScript,无法通过发送http请求的url的形式**_

> _**使用JavaScript API前先获得key**_

![页面结果](/res/img/blog/gaode_key.jpg)

#### _以上海东方明珠为例代码实现_

```sh
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
  <title>地理编码</title>
  <style type="text/css">
    body{
      margin:0;
      height:100%;
      width:100%;
      position:absolute;
    }
    #tip{
    background-color: #fff;
    border-radius: 3px;
    font-size: 12px;
    height: 120px;
    line-height: 20px;
    padding-left: 10px;
    padding-right: 10px;
    }
    #tip input[type="button"]{
      margin-top:10px;
      background-color: #0D9BF2;
      height:25px;
      text-align:center;
      line-height:25px;
      color:#fff;
      font-size:12px;
      border-radius:3px;
      outline: none;
      border:0;
      cursor:pointer;
    }
  </style>
</head>
<body onload="geocoder()">
  <div id="tip" >
    <input type="button" value="地理编码" onload="geocoder()"/>
    <div id="r_title"><b>地理编码结果:</b></div>
    <div id="result" > </div>
  </div>
  <script type="text/javascript" src="http://webapi.amap.com/maps?v=1.3&key=you_key"></script>
  <script type="text/javascript">
    var marker = new Array();
    var windowsArr = new Array();
    function geocoder() {
      var MGeocoder;
      //加载地理编码插件
      AMap.service(["AMap.Geocoder"], function() {
        MGeocoder = new AMap.Geocoder({
            // city:"", //城市，默认：“全国”
            // radius: //范围，默认：500
        });
        //返回地理编码结果
        //地理编码
        MGeocoder.getLocation("上海东方明珠", function(status, result){
          if(status === 'complete' && result.info === 'OK'){
            geocoder_CallBack(result);
          }
        });
      });
    }
    function geocoder_CallBack(data){
        var resultStr="";
        //地理编码结果数组
        var geocode = new Array();
        geocode = data.geocodes;
        for (var i = 0; i < geocode.length; i++) {
            //拼接输出html
            resultStr += "<span style=\"font-size: 12px;padding:0px 0 4px 2px; border-bottom:1px solid #C1FFC1;\">"+
            "<b>经,维</b>:" + geocode[i].location.getLng() +","+ geocode[i].location.getLat() +
            "</span>";
        }
        document.getElementById("result").innerHTML = resultStr;
    }
  </script>
</body>
</html>

```
* _**显示经纬度结果:**_

![页面结果](/res/img/blog/sh_dfmz.png)


---------------------------------------

### 百度 --- Web服务API

**Geocoding API:** 是一类简单的HTTP接口，用于提供从地址到经纬度坐标或者从经纬度坐标到地址的转换服务,可以使用C# 、C++、Java等语言发送HTTP请求且接收JSON、XML的返回数据。

![页面结果](/res/img/blog/geocoding.png)

**[详细介绍链接][2]**

> _**使用AMap.Geocoder前先申请ak(即获取密钥)**_

![页面结果](/res/img/blog/baidu_ak.jpg)


> _**在当前项目lib/tasks目录创建geo_address.rake文件**_

```sh
namespace :geocoder do
  # 将景点地址写入landscape_address文件
  def write_file(line_data)
    file_path = "#{Rails.root}/doc/temp_data/landscape_address.txt"
    File.delete(file_path) if File.exist?(file_path)
    File.open(file_path, 'w'){ |f|
      line_data.each do |line|
        f.write ("#{line}\r\n")
      end
    }
  end
  def write_geocode(result_data)
    # 将经,纬度写入geocoder_callback文件
    geocoder_file_path = "#{Rails.root}/doc/temp_data/geocoder_callback.txt"
    File.delete(geocoder_file_path) if File.exist?(geocoder_file_path)
    File.open(geocoder_file_path, 'w'){ |f|
      result_data.each do |data|
       f.write ("#{data}\r\n")
     end
    }
  end
  def open_url(adress_line)
    result =[]
    adress_line.each do |line|
      # url请求样式
      baidu_api = "http://api.map.baidu.com/geocoder/v2/?address="
      address = "#{line}"
      api_str = "&output=json&ak=xtweXt6hb0WXZoN3Lg68oxXv"
      api_url = URI.escape("#{baidu_api}#{address}#{api_str}")
      client = RestClient::Resource.new(api_url)
      begin
        response = client.get()
        if response.present?
          # 将JSON转换成HASH
          if JSON.parse(response)["status"] == 0
            result << JSON.parse(response)["result"]["location"]
            puts JSON.parse(response)["result"]["location"]
          else
            error_msg = "Internal Service Error:无相关结果"
            error_result = "#{line}:#{error_msg}"
            result << error_result
            puts error_result
          end
        end
      rescue => e
        puts e.response
      end
    end
    write_geocode(result)
  end
  desc "get landscape poi_address "
  task :get_address => :environment do
    address_data = []
    Landscape.all.find_in_batches(batch_size: 100) do |group|
      sleep(2)
      group.each do |record|
        address_data << record.poi_address
      end
      write_file(address_data)
      open_url(address_data)
    end
  end
end
```

> _**执行命令**_ `rake geocoder:get_address`

> _**执行结果**_
![页面结果](/res/img/blog/geocoder_ip.png)

[1]:http://lbs.amap.com/api/javascript-api/guide-2/search_plugin/#geocoder
[2]:http://developer.baidu.com/map/index.php?title=webapi/guide/webservice-geocoding
