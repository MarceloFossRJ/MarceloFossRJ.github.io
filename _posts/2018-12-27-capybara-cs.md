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

## Capybara cheat sheet

### Form Interaction
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

* Scripting
```ruby
result = page.evaluate_script(‘4 + 4’);
```

* Debugging
```ruby
save_and_open_page
```

### Capybara Finders

```ruby
page.has_xpath?(‘//table/tr’)

page.has_css?(‘table tr.foo’)

page.has_content?(‘foo’)

find_field(‘First Name’).value

find_link(‘Hello’).visible?

find_button(‘Send’).click

find(‘//table/tr’).click

page.all(:xpath, '//a')

page.first(:xpath, '//a')

page.find('//textarea[@id="additional_newline"]')

page.find(:xpath, "//input[@id='form_pets_dog']")['checked']
# => true

page.find(:css, '#with_focus_event').trigger(:focus)

page.find(:css,'.wrapper').hover

page.find_field("test_field").value
# => 'blah'

page.find_by_id('red').tag_name
# => 'a'

# finds invisible elements when false
page.find_by_id("hidden_via_ancestor", visible: false)

page.find_button('What an Awesome')[:value]
# => 'awesome'

page.find_link('abo').text
# => 'labore'

page.find_link('other')[:href]
# => '/some_uri'
```

**Note:** `find` will wait for an element to appear on the page, as explained in the Ajax section. If the element does not appear it will raise an error.

**Note:** In XPath the expression `//` means something very specific, and it might not be what you think. Contrary to common belief, `//` means "anywhere in the document" not "anywhere in the current context".


### Capybara Scoped Finder `within`

```ruby
within(search_form) do
  fill_in 'Name', with: 'iOS 7'
  click_button 'Search'
end

def search_form
  '.search_form'
end

within_fieldset("villain_fieldset") do
  # ...
end

within_table("some_table") do
  # ...
end

# Execute the given block within the given iframe using given frame name or index.
#
within_frame('some_frame') do
end

save_page

# You need to install launchy gem.
save_and_open_page
```

## Capybara Matchers

```ruby
expect(page).to have_content("Some Content")
expect(page).to have_no_content("Some Content")

# True if there is a anchor tag with text matching regex
expect(page).to have_xpath("//a")
expect(page).to have_xpath("//a",:href => "google.com")
expect(page).to have_xpath("//a[@href => 'google.com']")
expect(page).to have_xpath("//a[contains(.,'some string')]")
expect(page).to have_xpath("//p//a", :text => /re[dab]i/i, :count => 1)

 # can take both xpath and css as input and can take arguments similar to both have_css and have_xpath
 expect(page).to have_selector(:xpath, "//p/h1")
 expect(page).to have_selector(:css, "p a#post_edit_path")

 expect(page).to have_css("input#post_title")
 expect(page).to have_css("input#post_title", :value => "Capybara cheatsheet")

 # True if there are 3 input tags in response
 expect(page).to have_css("input", :count => 3)

 # True if there or fewer or equal to 3 input tags
 expect(page).to have_css("input", :maximum => 3)

 # True if there are minimum of 3 input tags
 expect(page).to have_css("input", :minimum => 3)

 # True if there 1 to 3 input tags
 expect(page).to have_css("input", :between => 1..3)

 # True if there is a anchor tag with text hello
 expect(page).to have_css("p a", :text => "hello")
 expect(page).to have_css("p a", :text => /[hH]ello(.+)/i)

# For making capybara to take css as default selector
Capybara.default_selector = :css

# checks for the presence of the input tag
expect(page).to have_selector("input")

# checks for input tag with value
expect(page).to have_selector("input", :value =>"Post Title")

expect(page).to have_no_selector("input")

# For making capybara to take css as default selector
Capybara.default_selector = :xpath
# checks for the presence of the input tag
expect(page).to have_selector("//input")

# checks for input tag with value
expect(page).to have_selector("//input", :value =>"Post Title")

# checks for presence of a input field named FirstName in a form
expect(page).to have_field("FirstName")

expect(page).to have_field("FirstName", :value => "Rambo")
expect(page).to have_field("FirstName", :with => "Rambo")

expect(page).to have_link("Foo")
expect(page).to have_link("Foo", :href=>"googl.com")
expect(page).to have_no_link("Foo", :href=>"google.com")
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
