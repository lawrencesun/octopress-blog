---
layout: post
title: "Rails Notes: 利用七牛云储存Paperclip图片"
date: 2014-11-02 20:37:35 +0800
comments: true
categories: Rails
keywords: rails, heroku, paperclip, qiniu
description: 
---
之前使用[Paperclip来实现Rails应用上传图片的功能][1], 但是在部署到Heroku时会有无法储存的问题, 每次push都会删除之前上传的图片。

这是因为Heroku不支持动态上传文件, 很多人碰到这种情况, Stackoverflow上推荐的解决方案是使用Amazon S3, 但是这个服务需要信用卡注册, 另外只能免费使用一年。

这无疑会提高试错和测试阶段的成本。经过另一番搜索, 发现[七牛云][2]储存可以用来实现图片的储存和调用。

<!-- more -->

首先需要调用[paperclip-qiniu][3]的gem, 并对之前使用paperclip的相关文件进行修改。

修改paperclip的初始化内容, `config/initializers/paperclip.rb`:

```ruby
Paperclip::Attachment.default_options[:storage] = :qiniu
Paperclip::Attachment.default_options[:qiniu_credentials] = {
  :access_key => 'youraccesskey',
  :secret_key => 'yoursecretkey'
}
Paperclip::Attachment.default_options[:bucket] = 'topics-1024'
Paperclip::Attachment.default_options[:use_timestamp] = false
Paperclip::Attachment.default_options[:qiniu_host] =
  'http://yourhostname.qiniudn.com'
```
这里access_key, secret_key, qiniu_host分别是七牛云储存的相关信息。

```ruby
has_attached_file :photo, 
		:default_url => 'http://xxx.com/bookmissing.jpg',
  	:path => ":class/:attachment/:id/:basename.:extension"
validates_attachment_content_type :photo, :content_type => /\Aimage\/.*\Z/
```
其中default_url是默认图片, 可以事先上传至七牛。

path是图片储存和调用的地址, 可以根据需要来更改。例如:

`http://yourhost-qiniudn.com/books/photos/1/name.jpg`

接下来需要在view中调用:

```ruby
<%= qiniu_image_tag @movie.photo.url, :thumbnail => '212x316!', :quality => 80 %>
```
这里可以自定义一些选项, 比如thumbnail, gravity, crop, quality等, 具体可以参考七牛云的[高级图像处理接口][4]文档。

至此，实现了paperclip上传图片的存储和调用。

[1]: http://voice.lawrencesun.info/posts/2014/09/21/rails-notes-upload-avatar-using-paperclip/
[2]: https://portal.qiniu.com/signup?code=3lnzd178edmqa
[3]: https://github.com/lidaobing/paperclip-qiniu
[4]: http://docs.qiniutek.com/v3/api/foimg/#imageView