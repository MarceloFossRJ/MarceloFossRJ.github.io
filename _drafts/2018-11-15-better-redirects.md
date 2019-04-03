
http://ethilien.net/archives/better-redirects-in-rails/

Better Redirects in Rails
Saturday, March 22nd, 2008
Its been a very long time since I posted to this blog, but I thought I might share some tricks I’ve developed for handling a few special types of redirects a bit more gracefully in Ruby on Rails.

Note: all of the code in this post, except for one view helper method, should be placed in your ApplicationController (app/controllers/application.rb) from which all other controllers inherit.

Most people who have read any book on Rails will probably have run into the redirect_to_index method, which goes something like this:

def redirect_to_index(msg = nil)
  flash[:notice] = msg if msg
  redirect_to :action => 'index'
end
This method is mainly useful when you the user has just edited an object, and you now want to display a message in the flash like “User updated successfully”, and then redirect them back to the index action. But what about if you don’t want to redirect the user back to the index action, or if you want to send them to another controller? Say hello to the flash_redirect method:

def flash_redirect(msg, *params)
  flash[:notice] = msg
  redirect_to(*params)
end
flash_redirect accepts both a message to be put in the flash, along with the params for redirect_to, so that you can flash a message and then send the user anywhere you please.

These two methods by themselves are pretty useful, but what about the common situation where a user attempts to go somewhere they don’t have permission to, or that they need to login before accessing? In the Rails book, this is handled by storing the original url in the session, and then redirecting to the other action, which then has to do special session processing when it returns the user to the original action. Encapsulating all this code into some easily reusable methods would look like this:

# redirect somewhere that will eventually return back to here
def redirect_away(*params)
  session[:original_uri] = request.request_uri
  redirect_to(*params)
end

# returns the person to either the original url from a redirect_away or to a default url
def redirect_back(*params)
  uri = session[:original_uri]
  session[:original_uri] = nil
  if uri
    redirect_to uri
  else
    redirect_to(*params)
  end
end
redirect_away handles sending the user away to another action, and redirect_back will send them back to either the action they were redirected away from, or to a default action. One possible use for these could be to require authorization before accessing an action:

class AdminController < ApplicationController
  before_filter :require_admin, :except => 'login'

  def index
    # unauthorized people shouldn't be able to access this
  end

  def login
    # handle login
    if User.authorize(params[:username], params[:password])
      session[:admin] = true
      redirect_back(:action => 'index')
    end
  end

  private

  def require_admin
    unless session[:admin]
      flash[:notice] = "You must be logged in"
      redirect_away(:action => 'login')
      return false
    end
  end
end
So now we can redirect a user away from an action, and then send them back to it later on. But what if we want to let a user click a link to go somewhere, and then send them back where they came from later? With a simple helper method and a before filter, we can accomplish just that:

# app/helpers/application_helper.rb
def link_away(name, options = {}, html_options = nil)
  link_to(name, { :return_uri => url_for(:only_path => true) }.update(options.symbolize_keys), html_options)
end

# app/controllers/application.rb
before_filter :link_return

private

# handles storing return links in the session
def link_return
  if params[:return_uri]
    session[:original_uri] = params[:return_uri]
  end
end
Now in the view, you can write:

<%= link_away "Edit post", :controller => '/admin/posts', :action => 'edit', :id => post %>
And as long as the controller uses redirect_back, the user can click the link, and when they’re done editing, they will come right back to where they clicked the link. This trick is probably the most useful from a usability standpoint, given that nothing annoys a user more than having to manually navigate back to where they were after each change.

I hope you find these techniques useful for writing more user friendly and concise code!
