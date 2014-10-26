---
layout: post
title: "Rails Notes: Upload Avatar Using Paperclip"
date: 2014-09-21 17:28:49 +0800
comments: true
categories: Rails
keywords: rails, ruby, paperclip, avatar
description: 
---

In my previous rails apps, I used gravatar_for method to link the gravatar to users. In a recent app, I wanted to add cover photos to the books and allow users to upload the images.

So I decided to use paperclip after seeing one of the [episode](http://railscasts.com/episodes/134-paperclip) of railscast. Instead of applying the plugin, I installed paperclip gem.

Add `gem "paperclip", "~> 4.2"` to Gemfile and run `bundle install`.

Then add photo columns to the existing books table.

<!-- more -->

`rails g migration AddPhotosToBooks`

Migration:

```ruby
class AddAvatarColumnsToUsers < ActiveRecord::Migration
  def up
    add_attachment :books, :photo
  end

  def down
    remove_attachment :books, :photo
  end
end
```

This will insert four columns into books table: photo_file_name, photo_file_size, photo_content_type, photo_updated_at.

Add the following lines to Book Model:

```ruby
has_attached_file :photo, :styles => { :medium => "300x300>", :thumb => "100x100>" }, :default_url => "missing.png",
		:url => "/assets/books/:id/:style/:basename.:extension",
  	:path => ":rails_root/public/assets/books/:id/:style/:basename.:extension"
	validates_attachment :photo, 
  :content_type => { :content_type => ["image/jpeg", "image/jpg", "image/gif", "image/png"] },
  :size => { :less_than => 5.megabytes }
```

Forms to be updated:

```ruby
<div class="form-group">
	<%= f.label "cover" %>
	<%= f.file_field :photo %>
</div>
```

And the updated book's show view with the uploaded photo.

```ruby
<%= image_tag @book.photo.url(:medium) %>
```

Besides, in order to run paperclip, ImageMagick is required. We can simply install it by homebrew.

`brew install imagemagick`

However, when I installed homebrew, several issues came out.

It was a long story, briefly speeking, I ran `brew doctor` and tried to solve the warning issues by searching the net. Basiclly, I updated Git and Command Line Tool and reinstall the homebrew.



