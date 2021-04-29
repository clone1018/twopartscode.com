---
title: "Introduction to Waffle"
date: 2020-10-30T11:37:29-04:00
draft: true
showToc: true
weight: 1
---


### Ingredients 
* Elixir 1.11.1
* PostgreSQL 13
* Waffle 1.1.3
* Waffle Ecto x.x.x

Welcome to the first ever blog series on Two Parts Code! Our first highlight will be a library for Elixir called Waffle. It's a simple neat library that is essential for handling user uploads.


## Article Plan
Basically, for expanded content available on the site I want to have multiple posts attached to a single concept, this way we can drive SEO for specific questions, and the content will be easier to reference.

1. Introduction, common use-cases & origin story
2. Usage of Waffle with Phoenix Controllers
3. Usage of Waffle with Phoenix Live View
4. Advance usage (configuration, validation, storage)

### Purpose of Waffle
Waffle is a flexible file upload library for Elixir with straightforward integrations for Amazon S3 and ImageMagick. Waffle was originally forked from a deprecated project called Arc. The new fork has a focus on simplicity and maintenance. 


### Uses
Waffle is an extremely generic library so it's easy to drop into many different types of projects. 

* Simple file uploading & storage
* Image uploading, conversion, and storage
