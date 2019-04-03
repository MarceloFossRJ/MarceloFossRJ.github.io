---
layout: post
title: Capybara Cheat Sheet
date:   2018-12-27 18:44:25 -0300
category: [cheatsheet]
tags: [rspec, capybara, deply, deployment, release, cheatsheet, test, testing, tdd]
author: Marcelo Foss
intro: |
  [Capybara](https://github.com/capistrano/capistrano) is a framework for building automated deployment scripts. Although Capistrano itself is written in Ruby, it can easily be used to deploy projects of any language or framework, be it Rails, Java, or PHP.
---

## Capybara cheat cheat sheet

* Navigating
```ruby
visit(‘/projects’)
visit(post_comments_path(post))
```

* Clicking links and buttons
```ruby
click_link(‘id-of-link’)
click_link(‘Link Text’)
click_button(‘Save’)
click(‘Link Text’) # Click either a link or a button
click(‘Button Value’)
```

* Click the first link with the name of ‘Show’
```ruby
click_link(‘Show’, match: :first) # or
first(:link, ‘Show’)
```

* Click the first button with the name of ‘Update’
```ruby
click_button(‘Update’, match: :first) # or
first(:button, ‘Update’)
```

* Interacting with forms
```ruby
fill_in(‘First Name’, :with => ‘John’)
fill_in(‘Password’, :with => ‘Seekrit’)
fill_in(‘Description’, :with => ‘Really Long Text…’)
choose(‘A Radio Button’)
check(‘A Checkbox’)
uncheck(‘A Checkbox’)
attach_file(‘Image’, ‘/path/to/image.jpg’)
select(‘Option’, :from => ‘Select Box’)
```

* Scoping
```ruby
within(“//li[@id=’employee’]”) do
   fill_in ‘Name’, :with => ‘Jimmy’
end
within(:css, “li#employee”) do
  fill_in ‘Name’, :with => ‘Jimmy’
end
within_fieldset(‘Employee’) do
  fill_in ‘Name’, :with => ‘Jimmy’
end
within_table(‘Employee’) do
  fill_in ‘Name’, :with => ‘Jimmy’
end
```

* Querying
```ruby
page.has_xpath?(‘//table/tr’)
page.has_css?(‘table tr.foo’)
page.has_content?(‘foo’)
page.should have_xpath(‘//table/tr’)
page.should have_css(‘table tr.foo’)
page.should have_content(‘foo’)
page.should have_no_content(‘foo’)
find_field(‘First Name’).value
find_link(‘Hello’).visible?
find_button(‘Send’).click
find(‘//table/tr’).click
locate(“//*[@id=’overlay'”).find(“//h1”).click
all(‘a’).each { |a| a[:href] }
```

* Scripting
```ruby
result = page.evaluate_script(‘4 + 4’);
```

* Debugging
```ruby
save_and_open_page
```

### Asynchronous JavaScript
```ruby
click_link(‘foo’)
click_link(‘bar’)
page.should have_content(‘baz’)
page.should_not have_xpath(‘//a’)
page.should have_no_xpath(‘//a’)
```

* =XPath and CSS=
```ruby
within(:css, ‘ul li’) { … }
find(:css, ‘ul li’).text
locate(:css, ‘input#name’).value
Capybara.default_selector = :css
within(‘ul li’) { … }
find(‘ul li’).text
locate(‘input#name’).value
```

* Accept alert
```ruby
page.driver.browser.switch_to.alert.accept
```

* Find meta tag
```ruby
page.find(“meta[name=’keywords’]”, visible: false)
page.find(“meta[name=’description’]”, visible: false)
```

* Close previous opened window
```ruby
within_window(page.driver.browser.window_handles.first) do
page.driver.browser.close
end
```

* Set hidden input value
```ruby
page.execute_script(“$(‘#hidden_input_id’).val(‘value’)”)
```
