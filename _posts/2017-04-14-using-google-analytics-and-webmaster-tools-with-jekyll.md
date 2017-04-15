---
layout: post
title: Using Google Analytics and Webmaster Tools with Jekyll
description: This post talks bout the basic steps to set up Google Analytics and Webmaster Tools in your Jekyll website.
category: Jekyll
---

Google has several interesting tools to help you track information about your website. One of them is **Google Analytics**, a free tool so you can gather information about the your website’s traffic. It is incredibly easy to set it up if you are using a website static generator like Jekyll.

1. Sign in to [Google Analytics](https://www.google.com/analytics/#?modal_active=none) using your Google credentials under *Sign In > Analytics*.
2. On the left pane, go to *Administrator* and then to *Property Configuration*.
3. Fill out the basic information of your website, such as the name and URL.
4. Verify your account by adding the HTML file you downloaded in your root folder.
5. Go back to Google Analytics and verify your *Google Tracking ID*. Copy the script and create a new file inside the `_includes` folder with the content of the script. The script looks something like this:

    ```
    <script>
    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
    (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
    m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
    })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

    ga('create', '{{ site.google_analytics }}', 'auto');
    ga('send', 'pageview');

    </script>
    ```

6. Edit *head.html* file by adding the following code thanks to [Desired Persona](https://desiredpersona.com/google-analytics-jekyll/).

    {% raw %}
    ```
    {% if site.google_analytics and jekyll.environment == 'production' %}
    {% include analytics.html %}
    {% endif %}
    ```
    {% endraw %}

This helps have clean data, as only the Google tracking tool will work only in the production environment and not while testing our website using `bundle exec jekyll serve`.

Now, go back in a day or two to see the information gathered by Google Analytics about your website’s traffic.

Another tool that you may want to use to be able to appear on search engines is **Google Webmaster Tools**. For this tool to work, you just need to create a file called a *sitemap* so your website is easier to crawl. This is really important for new sites to get noticed.

If your website is hosted by Github Pages, you need to set up the sitemap manually. Github Pages only runs Jekyll on safe mode, so it doesn’t allow custom plugins.

1. Create a sitemap.xml file and put in your root folder. Thanks to [David Ensinger](http://davidensinger.com/2013/11/building-a-better-sitemap-xml-with-jekyll/) for this improved sitemap file.

    {% raw %}
    ```xml
    ---
    layout: null
    sitemap:
      exclude: 'yes'
    ---
    <?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
      {% for post in site.posts %}
        {% unless post.published == false %}
        <url>
          <loc>{{ site.url }}{{ post.url }}</loc>
          {% if post.sitemap.lastmod %}
            <lastmod>{{ post.sitemap.lastmod | date: "%Y-%m-%d" }}</lastmod>
          {% elsif post.date %}
            <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
          {% else %}
            <lastmod>{{ site.time | date_to_xmlschema }}</lastmod>
          {% endif %}
          {% if post.sitemap.changefreq %}
            <changefreq>{{ post.sitemap.changefreq }}</changefreq>
          {% else %}
            <changefreq>monthly</changefreq>
          {% endif %}
          {% if post.sitemap.priority %}
            <priority>{{ post.sitemap.priority }}</priority>
          {% else %}
            <priority>0.5</priority>
          {% endif %}
        </url>
        {% endunless %}
      {% endfor %}
      {% for page in site.pages %}
        {% unless page.sitemap.exclude == "yes" %}
          {% unless page.url contains 'index.html' %}
          <url>
            <loc>{{ site.url }}{{ page.url | remove: "index.html" }}</loc>
            {% if page.sitemap.lastmod %}
              <lastmod>{{ page.sitemap.lastmod | date: "%Y-%m-%d" }}</lastmod>
            {% elsif page.date %}
              <lastmod>{{ page.date | date_to_xmlschema }}</lastmod>
            {% else %}
              <lastmod>{{ site.time | date_to_xmlschema }}</lastmod>
            {% endif %}
            {% if page.sitemap.changefreq %}
              <changefreq>{{ page.sitemap.changefreq }}</changefreq>
            {% else %}
              <changefreq>monthly</changefreq>
            {% endif %}
            {% if page.sitemap.priority %}
              <priority>{{ page.sitemap.priority }}</priority>
            {% else %}
              <priority>0.3</priority>
            {% endif %}
          </url>
          {% endunless %}
        {% endunless %}
      {% endfor %}
    </urlset>
    ```
    {% endraw %}

2. Go to [Google Webmaster Tools](https://www.google.com/webmasters/tools/home?hl=en) and click on Add Property to add your website.
3. On the left pane, go to *Crawl > Sitemaps* and click on *Add sitemap*. Enter the file you used for your sitemap and click on *Submit*.

It will take some time for Google to crawl your website with the file you specified. Don’t forget to come back and see the results to make sure your sitemap file is working.
