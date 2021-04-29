---
title: "Waffle with Phoenix Controllers"
date: 2020-10-30T11:37:29-04:00
draft: true
showToc: true

---

### Ingredients 
* Elixir 1.11.1
* PostgreSQL 13.0
* Phoenix 1.5.6
* Waffle 1.1.3
* Waffle Ecto 0.0.9

In this tutorial we'll be showing you how to use Waffle and Waffle Ecto directly inside a traditional Phoenix Controller based application, our next guide covers how to use it more magically with Phoenix LiveView. Our example project will be a simple file upload tool users can use to store plain text files and images. If you are interested in using Waffle without Waffle Ecto, you'll just need to omit the imports and handle the state saving yourself. We'll cover a basic walkthrough of this in the [advanced usage](/elixir/waffle/advanced-usage) article.
 
> ### Previous Article
> If you're looking for a quick intro to Waffle, please check out [Introduction to Waffle](/elixir/waffle/introduction-to-waffle).

## Phoenix Setup
### Bootstrap some HTML
This step is optional if you are adding a Waffle Upload to an existing form or schema. 

```bash
# Use Phoenix to generate some HTML scaffolding for us. We'll call 
# the context Uploads -- since we'll be storing images later as well, 
# and we'll call the individual models a File.
#
# Fields:
# file - We'll be using the file field to store the actual string 
#        representation of the waffle upload. Waffle will translate 
#        this back into a servable file as well. Use a string type 
#        so Waffle can save it correctly.
# description - A short description of the file
$ mix phx.gen.html Uploads File files file:string description:string 
```

After you generate the HTML, make sure you follow the steps to add the route and migrate the database.

![Phoenix Scaffolding File List](/uploads/elixir/waffle/scaffolding-file-list.png)
![Phoenix Scaffolding New File](/uploads/elixir/waffle/scaffolding-new-file.png)

## Waffle Setup
### Setup the Waffle Storage Provider
In order to use Waffle in any capacity you'll need to setup the storage provider in one of your configs. A common location for your dev environment is `config/dev.exs`.

```elixir
# config/dev.exs
...

# Configure Waffle to store all uploads in a local filesystem location.
config :waffle,
  storage: Waffle.Storage.Local
```

### Setup your endpoint.ex to handle the uploads
Anytime you're using the local storage method with waffle and not otherwise handling the assets with some other webserver (like nginx) you'll need to configure your `endpoint.ex` to allow for the static assets to be served.

```elixir
# lib/waffle_example_web/endpoint.ex
defmodule WaffleExampleWeb.Endpoint do
    ...

    # Make your /uploads dir available to any web clients. 
    # Don't replace your existing Plug.Static, as this 
    # will just be supplemental.
    plug Plug.Static,
        # Accepts web requests at example.com/uploads
        at: "/uploads",
        # Looks for the file anywhere in waffle_example/uploads
        from: Path.expand("./uploads"),
        gzip: false

    ...
end
```

### Define the Waffle Definition
A Waffle Definition defines an interface for your upload to automatically work with, so you are not doing your validation & transformations outside of your business logic code. With Phoenix, it makes the most sense to store these files in `lib/waffle_example_web/uploads/` to keep them in a single location. I choose the `_web` directory because Waffle exists to translate a web http upload into a file on your system, and then your business logic can do whatever it wants with that file.

### Simplest plain text file upload
Like I mentioned before, Waffle can be configured to be very feature-full and powerful, or you can use it in less lines than your entire Ecto Schema you store it in.

You can generate an Waffle Definition using `mix waffle.g text_file` or simply create a new file.

```elixir
# lib/waffle_example_web/uploaders/text_file_upload.ex
defmodule WaffleExampleWeb.TextFileUpload do
  use Waffle.Definition
  use Waffle.Ecto.Definition

  @extension_whitelist ~w(.txt)

  # The validate function is also optional :)
  def validate({file, _}) do
    file_extension = file.file_name |> Path.extname |> String.downcase
    Enum.member?(@extension_whitelist, file_extension)
  end
end
```

You also need to update your Ecto Schema to utilize the Waffle File definition we created.

```elixir
# lib/waffle_example/uploads/file.ex
defmodule WaffleExample.Uploads.File do
  ... 
  # Use the Waffle Ecto Schema
  use Waffle.Ecto.Schema

  schema "files" do
    field :description, :string
    # Change the existing file field definition to point to the 
    # Waffle definition's built-in `Type` definition.

    # field :file, :string -- original field definition
    field :file, WaffleExampleWeb.TextFileUpload.Type 

    timestamps()
  end

  ...

  # Modify your changesets to cast the file from a %Plug.Upload{} 
  def changeset(file, attrs) do
    file
    # Remove the :file from the cast, because you handle it below
    # |> cast(attrs, [:file, :description])
    |> cast(attrs, [:description])
    # Add a new cast_attachments call provided by Waffle.Ecto.Schema
    |> cast_attachments(attrs, [:file])
    |> validate_required([:file, :description])
  end

  ...
end
```

After you've done that Ecto now knows how to treat any file uploads. Behind the scenes [waffle_ecto is checking for any matching pattern](https://github.com/elixir-waffle/waffle_ecto/blob/72e9843d860a1ff0dd7588e27f7c8b4aae827d70/lib/waffle_ecto/schema.ex#L92) to cast into the changeset. 

It's worth mentioning that the cast_attachments can also handle remote files by using a similar syntax 

# Todo

```elixir
changeset 
|> cast_attachments(attrs, [:fetched_remote_file], allow_urls: true)

```

## Uploading From the Browser
Surprisingly, you don't need to modify any of your controller logic for Phoenix, since they are only used for translating the web request to the domain logic. Since you are not changing the structure, just the types, you can skip the controller actions.

What you do need to do however, is update the generated `form.html.eex` to use a file upload input type instead of a text input type. Phoenix provides helpers for this:

```elixir
  <%= label f, :file %>
  <%= file_input f, :file, accept: "plain/text" %>
  <%= error_tag f, :file %>
```

If you made the change successfully you should now have a page with an file input form and a description. The final test is to try and upload a text file of your choosing!

![Waffle New File](/uploads/elixir/waffle/waffle-new-file.png)

Using the file either by showing it to users, or offering to download it is extremely simple as well. Simply by calling the `url` function you can get the path. 

```elixir
# Will output /uploads/your-text-file.txt
<%= WaffleExampleWeb.TextFileUpload.url(file.file) %>

# Will output http://localhost:4000/uploads/your-text-file.txt
<%= Routes.static_url(@conn, WaffleExampleWeb.TextFileUpload.url(file.file)) %>
```

So as you can see, adding a basic end-to-end file upload to a Phoenix application is super simple with Waffle. 

# Next Article: Using Waffle with Live View