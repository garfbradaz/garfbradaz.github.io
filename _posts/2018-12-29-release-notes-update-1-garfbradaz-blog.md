---
layout: post
author: garfbradaz
category: blog
tags: [release-notes,disqus]
excerpt_separator: <!--more-->
comments: true
title: Release Notes - Update 1
---
## Release Notes - Update 1

Whenever I do an update to this blog itself, I will be providing some Release Notes on changes, including where possible, what I have done, as this may be helpful to someone else. <!--more-->

### Version Notes

I was going to use a proper Semantic Version (SemVer) for the site, but decided just to do incremental number releases (like Chrome). Just, well because!. No other reason than ease.

### Blog Post Comments

I have now switched on comments using the service [Disqus](https://disqus.com/) for which you can sign up for free. Because Jekyll doesn't have a back end data store, this made the most sense to me (& Disqus has good support).

How did I do it? Well this is my site, so you may need to add it different places depending on your site structure, **but** I followed the standard *Jekyll* site creation for Github Pages, the structure is what I would call **vanilla**.

- Sign up to Disqus.

- Take note of the the *shortname* you create for your site.

- Edit your `_config.yml` and add the following:

```yaml
disqus:
    shortname: <replace-with-your-short-name>
```

- Create a `comments.html` and and save it to your `_includes` directory with the following snippet. This is the embedded code provided by Disqus:

```html
{% if page.comments %}
    <div class="comments">
        <div id="disqus_thread"></div>
        <script type="text/javascript">
            var disqus_shortname = '{{ site.disqus.shortname }}';
            (function() {
            var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
            dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
            (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq); })();    
        </script>
    <noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
    </div> 
{% endif %}
```

- Add a reference to `comments.html` to your `posts.html`. Add the following to the bottom of the `posts.html` (or whatever your HTMl file is called):

![include-comments](/assets/img/posts/include-comments.png)

- You need to turn a comment **on** using the following Front Matter value in each post file:

```
comments: true
```

- Rebuild your posts:

```bash
bundle exec jekyll build
```

- Voila, you will have a nice comment at the bottom of your page:

![disqus](/assets/img/posts/disqus.png)
