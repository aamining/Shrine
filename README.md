# README

# To set up Shrine as a image uploader

1-

```
rails g scaffold Post image_data:text description:text
```

2- routes: root to:'post#index'

3- add: gem 'shrine' and bundle

4- copy the follow codes in to config >initializers >shrine.rb

```
require "shrine"
require "shrine/storage/file_system"

Shrine.storages = {
  cache: Shrine::Storage::FileSystem.new("public", prefix: "uploads/cache"), # temporary
  store: Shrine::Storage::FileSystem.new("public", prefix: "uploads/store"), # permanent
}

Shrine.plugin :sequel # or :activerecord ##cheange to activerecord in development
Shrine.plugin :cached_attachment_data # for forms
Shrine.plugin :rack_file # for non-Rails apps

```
5- add to post.rb models

```
validates :description, presence: true

```
6- mkdir app/uploaders

 edit app/uploaders/image_uploader.rb

and put following in to that

```

class ImageUploader < Shrine
  # plugins and uploading logic
end

```

7- As the first line put in to models/post.rb

```

include ImageUploader[:image]

```

8-  in to 'views/posts/form' replace Class="field" with:


```
<div class="field">
  <%= f.label :image %>
  <%= f.hidden_field :image, value: f.object.cached_image_data %>
  <%= f.file_field :image %>

</div>

```

9- replace first line of same page with this:

```

<%= form_for(post) do |f|%>

```

10- in same page add this field between Calss field and Class action

```
<div class="field">
  <%= f.label :description %>
  <%= f.text_area :description %>
</div>

```

11- NOW add this one as the first Class="field in the same page"

```
<div class="field">
  <% if post.image.present? %>
  <%= image_tag post.image_url %>
<%end%>

```

12- in views > posts > show.html.erb

```
add under <strong>Image data:</strong>

<% if @post.image.present? %>
<%= image_tag @post.image_url %>
<%end%>

```
```

and remove:

<%= @post.image_data %>

```

13- change prams from :image_data to :image and add :description
