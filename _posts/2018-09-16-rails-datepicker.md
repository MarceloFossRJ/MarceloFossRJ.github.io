
---
layout: post
title: Vim CheatSheet
date:   2018-08-25 18:44:25 -0300
category: [cheatsheet]
tags: [vim, cheatsheet]
author: Marcelo Foss
intro: |
  [Vim](http://www.vim.org/) is a very efficient text editor. This reference was made for Vim 8.0.
---

```javascript
//application.js
$('.form_datetime').datepicker({
    autoclose: true,
    todayBtn: true,
    pickerPosition: "bottom-left",
    format: 'mm-dd-yyyy'
});

$(function () {
    $('[data-toggle="tooltip"]').tooltip()
});

$(document).ready(function(){
    $('.datepicker').datepicker({
            autoclose: true,
            todayBtn: true,
            format: 'yyyy-mm-dd',
            clearBtn: true,
            orientation: 'bottom left'
    });
});

$(document).ready(function(){
    $('.monthpicker').datepicker({
        startView: 'months',
        minViewMode: 'months',
        autoclose: true,
        todayBtn: true,
        format: 'yyyy-mm',
        clearBtn: true,
        orientation: 'bottom left'
    });
});

$(document).ready(function () {

    $("#addRow").click(function () {
        $("#myTable").append("<tr><td>row</td><td><input type='text'></td></tr>");
    });
});

function add_fields(link, association, content) {
    var new_id = new Date().getTime();

    // find the new_ + "association" that was defined in Rails helper
    var regexp = new RegExp("new_" + association, "g");

    // find the container and append in the sub field content
    $(link).prev().append(content.replace(regexp, new_id));
    return false;
}

function add_fields_to_table_row(link, association, content, id) {
    var new_id = new Date().getTime();

    // find the new_ + "association" that was defined in Rails helper
    var regexp = new RegExp("new_" + association, "g");

    // find the container and append in the sub field content
    $(id).append(content.replace(regexp, new_id));
    return false;
}
```




```ruby
#app/inputs/date_time_picker_input.rb
class DateTimePickerInput < SimpleForm::Inputs::Base
  def input
    template.content_tag(:div, class: 'input-group date') do
      template.concat @builder.text_field(attribute_name, input_html_options)
      #template.concat span_remove
      #template.concat span_table
    end
  end

  def input_html_options
    super.merge({class: 'form-control form_datetime'})
  end

  def span_remove
    template.content_tag(:span, class: 'input-group-addon') do
      template.concat icon_remove
    end
  end

  def span_table
    template.content_tag(:span, class: 'input-group-addon') do
      template.concat icon_table
    end
  end

  def icon_remove
    "<i class='glyphicon glyphicon-remove'></i>".html_safe
  end

  def icon_table
    "<i class='glyphicon glyphicon-th'></i>".html_safe
  end

end

```
