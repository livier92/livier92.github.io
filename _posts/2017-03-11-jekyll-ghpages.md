---
layout: post
title:  "How to create a blog with Github Pages and Jekyll"
categories: jekyll github pages
---
Okay, so you may be wondering, how do I start a blog from scratch?

The first step is to create your repository on Github. Why Github? Github has
a really cool feature that allows you to serve a website for free called
[Github Pages](https://pages.github.com/).
It will be hosted from your Github repository directly, so all the changes that
you make are live. Let's start by creating a new repository in Github.

* Create a repository in Github named *username.github.io*.

By using the previous name on your new repository, Github will know automatically
that you are trying to host a website so make sure that your username is correct.

* Clone your repository in your local machine.

```
$ git clone https://github.com/username/username.github.io
```

Now, let's test your new site with a simple html page to make sure Github Pages
is working correctly.

* Create a simple html page.

```
$ cd username.github.io
$ echo "Hello World" > index.html
```

Time for an update. Push your new changes to your remote repository.

* Push your changes

```
$ git add --all
$ git commit -m "Initial commit"
$ git push -u origin master
```

* Go to a browser and check out *http://username.github.io* to see "Hello World"
on your new site.

Cool, isn't it?

Now time to add some style to our site. For that purpose, we are using
[Jekyll](https://jekyllrb.com/docs/home/),which is a static site generator that
takes a template directory and turns it into a ready to publish website. The
advantage of Jekyll is the built-in support for Github Pages. This can
save you lots of time and so you focus on the content of your posts.

### **Requirements to install Jekyll**

To install Jekyll, you need to have Ruby 2.1.0 or higher.

* Check the version of Ruby installed

```
$ ruby --version
ruby 2.X.X
```

If you don't, click [here](https://www.ruby-lang.org/en/downloads/)
for more information on how to install Ruby.

Once you make sure you have Ruby installed, you are ready to install Jekyll and
Bundler at the same time.

* Install Jekyll and Bundler

```
$ gem install jekyll bundler
```

Now you can create a website automatically with Jekyll using the `ǹew` keyword.
By default, Jekyll will use the *Minima* theme with this keyword. Although you
can change your theme later on, I would recommend sticking to Minima for the
beginners like me since it has more html templates than the others, such as
drafts.

* Create a Jekyll site on your local folder

```
$ jekyll new .
```

In order to sync your Jekyll environment with Github pages, open your favorite
editor and look for the *Gemfile*. Delete the `#` from the
beginning of this line `gem "github-pages", group: :jekyll_plugins`
to uncomment it.

Then, run `bundle update github-pages` to have the latest updates on the recently
uncommented gem.


### **Building your local Jekyll site**

Once you have created your Jekyll project, navigate to your local root folder.

* Run a development server *http://localhost:4000*

```
$ bundle exec jekyll serve
```

> **Tip**: you can use `bundle exec` to guarantee that your prorgram will run with
the version of the gems specified in the *Gemfile*, even though you have
another setup installed on your computer globally.

Now browse to *http://localhost:4000* to see your new site up and running
with Jekyll.

That's it, you are done. Don't forget to push all these new changes in your
remote repository. Hopefully, this tutorial will help you with the basic
setup for your new blog.

### **Additional information**

While doing this blog and using drafts, I was wondering if there was a command
to publish a draft directly. I find
[Jekyll Compose](https://github.com/jekyll/jekyll-compose), which is a jekyll
plugin that has that and more. Be sure to check it out and add it to your
*Gemfile* for extra functionalities.
