---
layout: post
title: "Rails Notes 2"
date: 2014-02-11 13:28:40 +0800
comments: true
categories: [Rails, Ruby]

keywords: ruby, rails
description: 2nd note taken for rails learning 
---

Last week, I built a blog prototype based on the twitter-like app in Rails Tutorial. Here is the 2nd note.

<!-- more -->
__Email validation__

Create a database index on the email column, and require that the index be unique. 
```ruby
class AddIndexToUsersEmail < ActiveRecord::Migration
  def change
    add_index :users, :email, unique: true
  end
end
```

Add validation 'app/models/user.rb'
```ruby
before_save { self.email = email.downcase }
validates :username, presence: true, uniqueness: true, length: 	{ maximum: 15 }
VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
validates :email, presence: true, format: { with: VALID_EMAIL_REGEX },
										uniqueness: { case_sensitive: false}
```

__User authentication__

Apply a gem called `bcrypt-ruby`. Presence validations for the password and its confirmation are automatically added by `has_secure_password`. Call method `authenticate` to confirm and   get user.

Generate migration and `add_column :users, :password_digest, :string` to the table.
```
rails g migration add_password_digest_to_users
```

__Sign in user__

create a non-model backed controller `sessions`. A session is a semi-permanent connection between two computers, such as a client computer running a web browser and a server running Rails. Sessions resource will use a cookie, which is a small piece of text placed on the user’s browser. 

non-model backed form for sign-in, using form_tag
alternatively, using form_for(:session, url: sessions_path)
```ruby
<%= form_tag '/signin', method: 'post' do %>
	<%= label_tag :username, "用户名" %>
	<%= text_field_tag :username %>
	<br/>
	<%= label_tag :password, "密码" %>
 	<%= password_field_tag :password %>
	<br/>
	<%= submit_tag "登录", class: "btn btn-primary" %>
<% end %>
```

signin page above for _new_ sessions, signing in will _create_ a session, and signing out will _destroy_ it
```ruby
def create
	user = User.find_by_username(params[:username])
  if user && user.authenticate(params[:session][:password])
    ...

def destroy
	session[:user_id] = nil
		...
```

routes:
```
get  	'/signup', to: 'users#new'
get		'/signin', to: 'sessions#new'
post 	'/signin', to: 'sessions#create'
get '/signout', to: 'sessions#destroy'
```

__Current_user__

define current_user in ApplicationController and add `helper_method :current_user, :sign_in?, …` to make it available to view templates. Alternatively, we can add methods in ApplicationHelper, and add`include SessionsHelper` in ApplicationController to make it available in controller.

```ruby
def current_user
  @current_user ||= User.find(session[:user_id]) if session[:user_id]
end

def signed_in?
	!!current_user
end

def require_user
	unless signed_in?
  	flash[:error] = "请登录."
  	redirect_to '/signin'
 	end
end
```
"||=" assigning to a variable if it’s nil but otherwise leaving it alone. _memorization_: calls the find_by method the first time current_user is called, but on subsequent invocations returns @current_user without hitting the database.

sign in user upon sign up:
```ruby
if @user.save
	session[:user_id] = @user.id
	...
```

`add_user_id_to_post` and assign user_id to new post
```ruby
def create
	@post = Post.new(post_params)
	@post.user = current_user
	...
```

__Update user__

```ruby
def update
	if @user.update_attributes(user_params)
	...
end

<%= f.submit(@user.new_record? ? "注册" : "更新", class: "btn btn-primary") %>
```

__Add gravatar__

add in 'app/helper/user_helper.rb' and call <%= gravatar_for @user %>
```ruby
module UsersHelper
  # Returns the Gravatar (http://gravatar.com/) for the given user.
  def gravatar_for(user)
    gravatar_id = Digest::MD5::hexdigest(user.email.downcase)
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}"
    image_tag(gravatar_url, alt: user.username, class: "gravatar")
	end 
end
```

add some styles
```css
/* gravatar */
.gravatar-sm {
	margin-right: 10px;
	height:48px;
	width:48px;
}

.gravatar-normal {
	height:64px;
	width:64px;
}
```

__Require the same user__

'app/controllers/application_controller.rb'
```ruby
def correct_user?(user)
  user == current_user
end
```
'app/controllers/users_controller.rb'
```ruby
before_action :correct_user, only:[:edit, :update]

def correct_user
  @user = User.find(params[:id])
  redirect_to root_path unless correct_user?(@user)
end
```

__Change date format__

```ruby
module ApplicationHelper
	def display_date(dt)
		dt.strftime("%d/%m/%Y") 
	end
end
```

__Others__

apart from @import bootstrap in app/assets/stylesheets/custom.css.scss
//= require bootstrap in app/assets/javascripts/application.js

flash.now #disappear as soon as there is an additional request.

issue:
when I submit blank comment, it leads to error. this is because I run the _comments.html.erb before render the error message. 

solution:
add <% unless comment.body.blank? %> .... <% end %> in _comments partial.

Strong parameters:
The reason is that initializing the entire params hash is extremely dangerous—it arranges to pass to User.new all data submitted by a user. In particular, suppose that, in addition to the current attributes, the User model included an admin attribute used to identify administrative users of the site. The way to set such an attribute to true is to pass the value admin=’1’ as part of params[:user], a task that is easy to accomplish using a command-line HTTP client such as curl. The result would be that, by passing in the entire params hash to User.new, we would allow any user of the site to gain administrative access by including admin=’1’ in the web request.

<%= render 'shared/posts' , objects: @posts%> to reduce duplication
