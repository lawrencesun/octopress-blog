---
layout: post
title: "Rails Notes: Markdown Support"
date: 2014-02-14 12:40:02 +0800
comments: true
categories: [Rails, Ruby]

keywords: rails, markdown, Redcarpet, Railscasts
description: add markdown editor to a rails app using Redcarpet.
---
The default output of the content in my app is directly shown as HTML. The problem is even I entered several paragraphs, it will be shown in one. On the other hand, I intend to add markdown support in the beginning. Thanks Google and Github, I found a gem called [Redcarpet](https://github.com/vmg/redcarpet).

First I found a screencast in [Railscasts](1). Unfortunately, this solution was introduced 2 years ago. However, I found an up-to-date one by SimplizIT below the video. Here it is.

<!-- more -->

* Install it by adding `gem 'redcarpet'` to Gemfile, and run `bundle install`.

* Then add the following code to `application_helper.rb`

```ruby
def markdown(text)
	renderOptions = {hard_wrap: true, filter_html: true}
  markdownOptions = {autolink: true, fenced_code_blocks: true, no_intra_emphasis: true, strikethrough: true}
	markdown = Redcarpet::Markdown.new(Redcarpet::Render::HTML.new(renderOptions), markdownOptions)
  markdown.render(text).html_safe
end
```

<sub>here, we can customise the markdown extension and html output by changing the renderOptions and markdownOptions. The full option shows [here](1).</sub>  

* Apply 'markdown' function in the view template.

`<%= markdown(@post.text) %>`




[1]: (http://railscasts.com/episodes/272-markdown-with-redcarpet)


