---
layout: post
title: "Rails Notes: Remember Me and Reset Password"
date: 2014-08-12 16:14:58 +0800
comments: true
categories: [Rails, Ruby]

keywords: rails, ruby, user authentication
description: Extend user authentication with remember me and reset password features from scratch.
---
__1. Remember Me__

We used sessions to store user's id in order to let them sign in just once. However, when users close browser, the session cookie is deleted. So we would like to replace it with a permanent one. Besides, if we store and expose user's id, it could be dangerous.

Therefore, we will generate and store a unique token for each user. First, create a new column to store the token.

<!-- more -->

```
$ rails g migration add_remember_token_to_users remember_token:string
$ rake db:migrate
```

Then write a method to generate remember_token before creating a new user in models/user.rb

```ruby
class User < ActiveRecord::Base
...
before_create { generate_token(:remember_token)}
...
def generate_token(column)
	begin 
		self[column] = SecureRandom.urlsafe_base64
	end while User.exists?(column => self[column])
end
```

Add a checkbox in views/sessions/new.html.erb for user to let them choose if they want us to remember.

```html
<div class="checkbox">
	<label>
		<%= check_box_tag :remember_me, 1, params[:remember_me] %>
		<%= t('remember me') %>
	</label><br>
</div>
```

Modify the SessionsController so that we store remember_token in cookies when user signed in.

```ruby
def create
	user = User.find_by_username(params[:username])
	if user && user.authenticate(params[:password])
		if params[:remember_me]
			cookies.permanent[:remember_token] = user.remember_token
		else
			cookies[:remember_token] = user.remember_token
		end
		flash[:success] = t('flash.session.create.success') + ", #{params[:username]}."
		redirect_to root_path
	else
		flash.now[:error] = t('flash.session.create.error')
		render 'new'
	end
end
```

Modify the current_user method in application.rb so it reads the remember_token from cookies rather than the user.id from the session.

```ruby
def current_user
  @current_user ||= User.find_by_remember_token(cookies[:remember_token]) if cookies[:remember_token]
end
```

Finally, we want to fill the remember_token column for existing users. Write a file called rebuild_remember_token.rake in lib/tasks and run `rake user:rebuild_remember_token`.

```ruby
namespace :user do
  desc "Rebuild Remember-Tokens"
  task :rebuild_remember_token => :environment do
    User.transaction do
      User.all.each { |u|
        u.update_attribute :remember_token, SecureRandom.urlsafe_base64
      }
    end
  end
end
```

__2. Password Reset__

We would like to allow users to reset their password when they forget. First, we will generate the route: `resources :password_resets`.

Then add a link in the signin page(views/sessions/new.html.erb).

```ruby
<div class="checkbox">
	<label>
		<%= check_box_tag :remember_me, 1, params[:remember_me] %>
		<%= t('.remember_me') %> | <%= link_to t('.forgotten_password'), new_password_reset_path %>
	</label><br/>
</div>
```

Create a password_resets_controller.rb file and a new.html.erb file in views/password_resets.

```ruby
class PasswordResetsController < ApplicationController
	
	def new
	end

	def create
		@user = User.find_by_email(params[:email])
		if @user
			@user.send_password_reset
			flash[:success] = t('flash.reset.email.success')
			redirect_to root_path
		else 
			flash.now[:error] =  t('flash.reset.email.error')
			render 'new'
		end
	end
```

```ruby
<div class="row">
	<div class="col-md-6 col-md-offset-3">
		<div class="well">
			<h4><%= t('.password_reset') %></h4>
			<hr/>
			<%= form_tag password_resets_path, method: 'post' do %>
  		<div class="form-group">
    		<%= label_tag :email %><br/>
    		<%= text_field_tag :email, params[:email], class: "form-control" %>
  		</div>
  		<%= submit_tag t('.password_reset'), class: "btn btn-primary" %>
			<% end %>
		</div>
	</div>
</div>
```

Then we will add columns to store the a token for the reset request and the expire time of it. 

```ruby
$ rails g migration add_password_reset_to_users password_reset_token:string password_reset_sent_at:datetime
```

When the user is found by the email, we will generate a token and send a reset email using send_password_reset method in /models/user.rb

```ruby
def send_password_reset
	self.update_column(:password_reset_token, SecureRandom.urlsafe_base64)
  self.update_column(:password_reset_sent_at, Time.zone.now)
  UserMailer.password_reset(self).deliver
end
```

Create the UserMailer and email template in /views/user_mailer/password_reset_text.erb

```ruby
$ rails g mailer user_mailer password_reset 
```

```ruby
class UserMailer < ActionMailer::Base
  default from: "from@example.com"

  def password_reset(user)
    @user = user 
    mail to: user.email, subject: "Password Reset"
  end
end
```

```ruby
To reset your password click the URL below.

<%= edit_password_reset_url(@user.password_reset_token) %>

If you did not request your password to be reset please ignore this email and your password will stay as it is.
```

We will need to alter enviroment configuration in /config/environment/development.rb to get URLs working. Similar line in production.rb with the live domain name.

```ruby
Auth::Application.configure do
  config.action_mailer.default_url_options = { :host => "localhost:3000" }
end
```

The URL includes the token as the id parameter, which we will use it in edit action. Create a form for users to reset password in edit.html.erb

```ruby
def edit
	@user = User.find_by_password_reset_token!(params[:id])
end
```

```ruby
<h4><%= t('.password_reset') %></h4><hr/>
<%= render 'shared/error', object: @user %>
<%= form_for @user, url: password_reset_path(params[:id]) do |f| %>
  <div class="form-group">
		<%= f.label :password, t('password') %>
		<%= f.password_field :password, class:"form-control", placeholder: "At least 6 digits"%>
	</div>
	<div class="form-group">
		<%= f.label :password_confirmation, t('password_confirmation') %>
		<%= f.password_field :password_confirmation, class:"form-control" %>
	</div>
	<%= submit_tag t('update'), class: "btn btn-primary" %>
<% end %>	
```

Here we use form_for in this form as we’re modifying a resource. Because of this we have to explicitly set the :url parameter so that the form isn’t POSTed to the UsersController. Instead it is sent to the PasswordResetsController’s update action, passing in the reset token as the id. 

Finally, we will write the update action.

```ruby
def update
  @user = User.find_by_password_reset_token!(params[:id])
  if @user.password_reset_sent_at < 2.hours.ago
  	flash[:error] = t('flash.reset.error')
    redirect_to new_password_reset_path
  elsif @user.update_attributes(params.permit![:user])
  	@user.update_attribute(:password_reset_token, nil)
  	flash[:success] = t('flash.reset.success')
    redirect_to signin_path
  else
    render 'edit'
  end
end
```

<sub>Reference: [Railscasts](http://railscasts.com/episodes/274-remember-me-reset-password?view=asciicast)</sub>

