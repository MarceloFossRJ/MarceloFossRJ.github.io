---
layout: post
title: Build Bootstrap forms with simple_form in Ruby on Rails
date:   2020-02-19 18:44:25 -0300
category: [article]
tags: [ruby, rails, simple_form, forms, Bootstrap]
author: Marcelo Foss
intro: |
---


https://almassapargali.com/2015/01/15/build-bootstrap-forms-with-simple_form-in-ruby-on-rails.html

Build Bootstrap forms with simple_form in Ruby on Rails
Jan 15, 2015

I’m building Rails application which is full of many Bootstrap forms. We use placeholders instead of labels, and mostly use horizontal forms, which most of the time has more than one field on one line. Since we’re adding/editing lots of forms, to keep moving fast I made few wrappers for simple_form which allows build horizontal bootstrap forms fast.

form-group behaves as row
Please note that form-group behaves as row inside form-horizontal, so we can use all col-* classes without adding row.

One-line fields of various width
simple_form generates this wrapper to use for horizontal forms:

config.wrappers :horizontal_form, tag: 'div', class: 'form-group', error_class: 'has-error' do |b|
  b.use :html5
  b.use :placeholder
  b.optional :maxlength
  b.optional :pattern
  b.optional :min_max
  b.optional :readonly
  b.use :label, class: 'sr-only'

  b.wrapper tag: 'div', class: 'col-sm-9' do |ba|
    ba.use :input, class: 'form-control'
    ba.use :error, wrap_with: { tag: 'span', class: 'help-block' }
    ba.use :hint,  wrap_with: { tag: 'p', class: 'help-block' }
  end
end
which is great, but it uses 9 columns as default width. But imagine this form: Multiple width form

It has fields of various widths. So I used this code to create wrappers for different column sizes:

1.upto(12) do |col|
  config.wrappers "field#{col}".to_sym, tag: 'div', class: 'form-group', error_class: 'has-error' do |mdf|
    mdf.use :html5
    mdf.use :placeholder
    mdf.use :label, class: 'sr-only'

    mdf.wrapper tag: 'div', class: "col-sm-#{col}" do |wr|
      wr.use :input, class: 'form-control'
      wr.use :error, wrap_with: { tag: 'span', class: 'help-block' }
      wr.use :hint,  wrap_with: { tag: 'p', class: 'help-block' }
    end
  end
end
So to create above form we’d write

<%= simple_form_for coupon, remote: true do |f| %>
  <%= f.input :title, wrapper: :field4 %>
  <%= f.input :description, wrapper: :field8 %>
  <%= f.input :image, wrapper: :field8 %>
  <%= f.input :expiry_date, wrapper: :ig4, wrapper_html: {id: "expiry-date-input"} do %>
    <%= f.input_field :expiry_date, as: :string, class: "form-control" %>
    <span class="input-group-addon"><i class="fa fa-calendar"></i></span>
  <% end %>
  <%= f.button :submit %>
<% end %>
And think how much time I saved having dozens of forms like this. (If you wonder what ig4 wrapper for, it works exactly as field4 except it’s for inline groups, I used this as reference)

Multiple fields in one line
Now let’s take a look at this form: Multiple fields on one line

We have more than one fields on one line. Most of answers on SO suggested making use of CSS, which I didn’t like. So, I defined another set of wrappers for inline fields:

1.upto(12) do |col|
  config.wrappers "inline_field#{col}".to_sym, tag: 'div', class: "col-sm-#{col}", error_class: 'has-error' do |ic|
    ic.use :html5
    ic.use :placeholder
    ic.use :label, class: 'sr-only'
    ic.use :input, class: 'form-control'
    ic.use :error, wrap_with: { tag: 'span', class: 'help-block' }
    ic.use :hint,  wrap_with: { tag: 'p', class: 'help-block' }
  end

  config.wrappers "inline_bool#{col}".to_sym, tag: 'div', class: "col-sm-#{col}", error_class: 'has-error' do |ib|
    ib.use :html5
    ib.optional :readonly

    ib.wrapper tag: 'div', class: 'checkbox' do |ba|
      ba.use :input
      ba.use :label
    end
    ib.use :error, wrap_with: { tag: 'span', class: 'help-block' }
    ib.use :hint,  wrap_with: { tag: 'p', class: 'help-block' }
  end
end
The most notable change here is that it doesn’t wrap fields in form-group and leaves that to us. So code of above form looks like:

<%= simple_form_for location, remote: true, defaults: {wrapper: :inline_field4} do |f| %>
  <div class="form-group">
    <%= f.input :name %>
    <%= f.input :street_line_one %>
    <%= f.input :city %>
  </div>
  <div class="form-group">
    <%= f.input :state %>
    <%= f.input :postal_code %>
  </div>
  <div class="form-group">
    <%= f.input :email %>
    <%= f.input :phone_number %>
    <%= f.input :image %>
  </div>

  <%= f.button :submit %>
<% end %>
I found this pretty cool. Also notice I used defaults: {wrapper: :inline_field4} instead of defining wrapper on every field.

Why I didn’t use input_field? Because that way we’d lose our sr-only labels, and I like having support for accessibility.

And if we ever need inline boolean, we’d just write

<div class="form-group">
  <%= f.input :email, wrapper: :inline_field6 %>
  <%= f.input :hide_my_email, wrapper: :inline_bool4 %>
</div>
Yay!
