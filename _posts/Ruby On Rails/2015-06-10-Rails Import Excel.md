---
layout : life
title : Rails Import Excel
category : Ruby On Rails
duoshuo: true
date : 2015-06-10
---

###简介
>
- _**（Excel）**_:_是Rails下经常使用的一种批量上传手段。_

---------------------------------------

###使用
>
- _**首先在Gemfile中添加说需要的gem:**_

>
```
  gem 'roo', '~> 2.0.0'
```

<!-- more -->


### 然后在所需要的地方添加上传方法:(例如我的)

>
![页面结果](/res/img/blog/ruby_on_rails/excel.png)

> _**给功能添加路由以及对应action**_

```sh
  namespace :admin do
    resources :questions do
      collection do
        post :import
      end
    end
  end
```

_**Controller:**_

```sh
    def import
      error_messages = Question.import(params[:file])
      redirect_to action: 'index', notices: error_messages
    end
```

> _**在当前项目Model文件实现上传功能**_

```
  EXCEL_QUESTION_HEADER = {
    question_id:    '编号',
    title:           '题干',
    answer:          '答案',
    category:       '类型',
    level:           '难度',
    explain:        '解释'
  }

  def build_excel_import(question_id, title, answer, category, level, explain)
    question = Question.new
    question_params = {title: title, answer: answer, category: category, level: level, explain: explain }
    question.set_params(question_params)
    question.id = question_id
    question.save
    question
  end

  def import(file)
    spreadsheet = open_spreadsheet(file)
    header = spreadsheet.row(1)
    (2..spreadsheet.last_row).each do |i|
      row = Hash[[header, spreadsheet.row(i)].transpose]
      notices = {}
      begin
        question_id = row[EXCEL_QUESTION_HEADER[:question_id]]
        answer = row[EXCEL_QUESTION_HEADER[:answer]]
        category = row[EXCEL_QUESTION_HEADER[:category]]
        if category.present? && answer.present?
          title = row[EXCEL_QUESTION_HEADER[:title]]
          level = row[EXCEL_QUESTION_HEADER[:level]]
          explain = row[EXCEL_QUESTION_HEADER[:explain]]
          if title.present? || explain.present? || level.present?
            if title.present? && explain.present? && level.present?
              build_excel_import(question_id.to_i, title, answer, category, level, explain)
            else
              raise
            end
          else
            question_id = question_id
            description = answer
            ordinal = category
            question_option_params = {question_id: question_id.to_i, description: description, ordinal: ordinal }
            question_option = QuestionOption.where(question_id: question_id, ordinal: ordinal ).first
            if question_option.present?
              raise
            else
              QuestionOption.build_question_option(question_option_params)
            end
          end
        else
          raise
        end
      rescue ActiveRecord::RecordNotUnique
        notices[:question_id] = question_id
        notices[:title] = title
        return notices
      rescue RuntimeError
        notices[:question_id] = question_id
        notices[:category] = category
        notices[:answer] = answer
        return notices
      end
    end
  end

  def open_spreadsheet(file)
    case File.extname(file.original_filename)
    when ".csv" then Roo::Csv.new(file.path, file_warning: :ignore)
    when ".xls" then Roo::Excelx.new(file.path, file_warning: :ignore)
    when ".xlsx" then Roo::Excelx.new(file.path, file_warning: :ignore)
    else raise "Unknown file type: #{file.original_filename}"
    end
  end

```


[可以参考一个railscasts的实现][1]




> _**但在视频中使用插件时会碰到一些问题如下:**_

> _**1:error:  uninitialized constant Excel**_

> _**解决方法:将Excelx.new() 换成 Change Roo::Excelx.new()**_

> _**2:Excel support has been extracted to roo-xls due to its dependency on the GPL'd spreadsheet gem. Install roo-xls to use Roo::Excel.**_

> _**解决方法:添加如下Gem:   gem 'roo-xls' ; bundle install**_

> _**3:error:  wrong number of arguments (3 for 2) for Roo::Excel.new() ...**_

> _**解决方法: 将Excelx.new(file.path, nil, :ignore) 换成 Roo::Excelx.new(file.path, file_warning: :ignore)**_


[1]:http://railscasts.com/episodes/396-importing-csv-and-excel?autoplay=true