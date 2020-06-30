---
layout: post
title:  "Media File Type Tags"
date:   2020-06-28 20:45:00 -0700
categories: jekyll pinboard development
author: Bruce THOMAS
---
This blog post presents a simple example of JAM stack (JavaScript, API, and Markup).
It's based on an example from *Hacking del.icio.us*, 2006 [^1], updated
to use **Pinboard** JSON feeds. I quote the book and offer my updates below.

![image bookmarks](/assets/ekstelu_info_images.png)

### Using tags to filter JSON feeds ###

**Pinboard** offers JSON feeds of user's public bookmarks, and these can be
filtered by tags. Interesting things can be done with these bookmarks depending
upon what the user wants the tags to mean. First, let's look at an example of
what **del.icio.us** did with bookmarks for certain types of files.

When users bookmarked audio, video, or image files on **del.icio.us**, the bookmarking service would automatically add tags to indicate the type of file being bookmarked. Here is a table of the *File Extensions Mapped to File Type and Media Tags* (from *Hacking del.icio.us* p. 129, Table 6-1).

 |*File Extension*  |*File Type Tag*        |*Media Tag*
 |`.mp3`            |`system:filetype:mp3`  |`system:media:audio`
 |`.wav`            |`system:filetype:wav`  |`system:media:audio`
 |`.mpg`            |`system:filetype:mpg`  |`system:media:video`
 |`.mpeg`           |`system:filetype:mpeg` |`system:media:video`
 |`.avi`            |`system:filetype:avi`  |`system:media:video`
 |`.wmv`            |`system:filetype:wmv`  |`system:media:video`
 |`.mov`            |`system:filetype:mov`  |`system:media:video`
 |`.tiff`           |`system:filetype:tiff` |`system:media:image`
 |`.jpeg`           |`system:filetype:jpeg` |`system:media:image`
 |`.gif`            |`system:filetype:gif`  |`system:media:image`
 |`.png`            |`system:filetype:png`  |`system:media:image`
 |`.doc`            |`system:filetype:doc`  |`system:media:document`
 |`.pdf`            |`system:filetype:pdf`  |`system:media:document`

> The notion behind media and file type tags is this:
> ...the URL is checked against a set of known file
> extensions. If one of these is found at the end of the URL, the corresponding
> file type tag is automatically applied.

This is an example of how **Pinboard** differs from what **del.icio.us** used to do.
At this time, **Pinboard** will allow users to add these tags themselves, but the
service will not automatically add them on the server-side like **del.icio.us** did. This means it is still possible to take advantage of these tags in JSON feeds, but only after users choose to add these tags when they save or edit their own bookmarks.

> One of the main beneifts...is the ability to filter for bookmarks of a certain
> file format...If you combine a JSON feed filtered on system:media:image with
> a little in-browser JavaScript, you can construct a photo gallery...

So, in this example, we want to get my bookmarks from **Pinboard** in a JSON feed,
using the `system:media:image` tag that I applied when I saved some bookmarks for
images from my other blog.

![image bookmark](/assets/system_media_image_bookmark.png)

Here is the URL for the JSON feed from **Pinboard** [^2]

  [https://feeds.pinboard.in/json/u:rheotaxis/t:system.media.image?count=16](https://feeds.pinboard.in/json/u:rheotaxis/t:system.media.image?count=16)

which returns JSON that begins like this:

~~~
[ {"u":"https:\/\/ekstelu.info\/assets\/joyce\/wake\/oconee_fluo.png",
   "d":"oconee_fluo.png (300×338)",
   "n":"mapo",
   "dt":"2019-10-27T22:08:06Z",
   "a":"rheotaxis",
   "t":["system.media.image"]},
~~~

### Getting bookmark data into HTML pages ###

Now let's display bookmark data from a JSON feed in an HTML page.
The *Hacking del.icio.us* book has an example for this on pages 130-133.
Looking at this from the perspective of the JAM stack, we have three parts to
deal with: JavaScript; API; and Markup. We already looked at the **Pinboard**
API above. We will look at the other two parts together below.
I had to update the
book's example to use JSON feeds from **Pinboard** instead of **del.icio.us**.
You can find the updated code in my public
GitHub repository at this URL. (I use the same file names from the book).

  [https://github.com/rheotaxis/loving-pinboard/tree/master/bookmark_lightbox](https://github.com/rheotaxis/loving-pinboard/tree/master/bookmark_lightbox)

#### HTML Markup and AJAX ####

The initial HTML markup presents an empty page except for the title. The
JavaScript will generate the content to display in the browser.
The first file to look at is `ch06_bookmark_lightbox.html`
~~~
<html>
    <head>
        <title>Bookmark Lightbox</title>
        <link href="/pinboard/ch06_bookmark_lightbox.css"
              type="text/css" rel="stylesheet" />
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="/pinboard/ch06_bookmark_lightbox.js"
              type="text/javascript"></script>
    </head>
    <body>
      <h1>Bookmark Lightbox</h1>
      <ul id="thumbs"></ul>
      <script>
$.ajax({
  url:'https://feeds.pinboard.in/json/u:rheotaxis/t:system.media.image?count=16',
  jsonp:'cb',
  dataType:'jsonp',
  success: function(data) {
    Pinboard.init(data);
  }
});
      </script>
    </body>
</html>
~~~

> a shell of an HTML page meant to host
> the CSS and JavaScript that will build the photo gallery.

There is an important difference between the book's version and the one given here.
The JSON feed for **Pinboard** is wrapped in `<script>` block in the `<body>` instead
of the `<head>` because we are using jQuery `$.ajax` method to get the JSON feed.
There is more information about this method at the jQuery help page. TL;DR [^3]

To simplify what's happening here, the browser has already loaded the `<script>`
elements contained in the page `<head>`. That contains a reference to a JavaScript
object `Pinboard` defined in separate file shown below. The HTML file
has an `<ul id="thumbs"></ul>` which is an empty, unordered list when the page is loaded. Then, the browser hits the next `<script>` element after this unordered list.

At this point, everything is already loaded in the browser except the JSON feed.
The browser then runs this JavaScript to get that feed using the jQeury.ajax
method.

~~~
$.ajax({
  url:'https://feeds.pinboard.in/json/u:rheotaxis/t:system.media.image?count=16',
  jsonp:'cb',
  dataType:'jsonp',
  success: function(data) {
    Pinboard.init(data);
  }
});
~~~

Just look after the line where it says `success: function(data)` and you can see
the JSON feed data being passed to the `Pinboard.init()` method which you will
see below.

#### Control the Page Layout ####

The next file has the CSS we need, unchanged from the book's example.
`ch06_bookmark_lightbox.cs`
~~~
body, div, td, li {
    font: 12px arial;
}
#thumbs {
    list-style: none;
    margin: 0;
    padding: 0;
}
#thumbs li {
    float: left;
    width: 23%;
    height: auto;
    padding: 0;
    margin: 0.5ex;
}
img.thumb {
    width: 100%;
    height: auto;
}
~~~

> the intent here is to style the list of thumbnails as a rough grid of
> floating images...however it scales the images down in width and allows
> the height to vary in order to retain the image's shape and aspect ratio.

#### Display Image from Bookmarks ####

Lastly, the book offers the `ch06_bookmark_lightbox.js` JavaScript file which
the book says:

> turns...bookmarks into thumbnails on the page.

~~~
/**
    ch06_bookmark_lightbox.js
    Display bookmark images in a "lightbox" of thumbnails.
*/
Pinboard = {
  init: function init(data) {
    // Grab a reference to the container for image thumbnails
    var lbox = document.getElementById('thumbs');

    // Iterate through the list of bookmarks loaded via JSON.
    for (var i=0, post; post=data[i]; i++)
    {
        // create a new list element for the thumbnail
        var li = document.createElement('li');

        // build a link to wrap around the images
        var a = document.createElement('a');
        a.setAttribute('href', post.u);
        a.setAttribute('title', post.n);

        // build the actual image from bookmark data
        var img = document.createElement('img');
        img.setAttribute('class', 'thumb');
        img.setAttribute('src', post.u);
        img.setAttribute('title', post.d);
        img.setAttribute('alt', post.n);

        // nest the image in the link
        a.appendChild(img);

        // nest the link in the list item
        li.appendChild(a);

        // add the list item to the list of thumbs
        lbox.appendChild(li);
    }
  }
}
~~~

Again, some updates were required to make the example work with the **Pinboard**
JSON feed. Essentially, we create a **Pinboard** JavaScript object with
a `init` function that will be get the data returned from the JSON feed.
The code loops through all the images in the JSON feed, and displays them
in the browser.

I have an example of this running on my other blog:

  [https://ekstelu.info/pinboard/ch06_bookmark_lightbox.html](https://ekstelu.info/pinboard/ch06_bookmark_lightbox.html)

One last quote from the book.

> While this isn't a very richly featured photo gallery, it should give you
> a small taste of how you can use...tagging to facilitate the collection and
> management of resources beyond plain links to Web pages.

#### Footnotes ####

[^1]: Orchard, Leslie M. *Hacking del.icio.us*, 2006.
[^2]: You can read more about **Pinboard** feeds [here](https://pinboard.in/howto/#rss)
[^3]: jQuery help page [https://api.jquery.com/jQuery.ajax/](https://api.jquery.com/jQuery.ajax/)
