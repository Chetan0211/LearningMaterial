# Use of <head> tag

<head> tag is used to store the metadata(data about the data) inside it. It specifies about the HTML file. This is not displayed on the webpage.

We can use several tags inside <head> tag. Some of them are:

- <title>
- <style>
- <base>
- <link>
- <meta>
- <script>
- <noscript>

### <title> Tag:

This tag is used to specify the title of the page. Below you can find the code snippet for the title.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>This is a title</title>
    </head>
    <body>
        <p>This is a paragraph</p>
    </body>
</html>
```

**OUTPUT:**

![Screenshot 2022-12-27 at 12.52.19 AM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dc62faf1-ada9-49cb-aa23-181df4fa3625/Screenshot_2022-12-27_at_12.52.19_AM.png)

### <style> Tag:

This tag is used to style the HTML. We can specify CSS inside the <style> tag.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>This is a title</title>
        <style>
            #paraStyle{
                color: red;
                font-size: 20;
            }
        </style>
    </head>
    <body>
        <p id="paraStyle">This is a paragraph</p>
    </body>
</html>
```

**OUTPUT:**

As we see we specified the CSS inside the tag and we used that ID to style the paragraph.

![Screenshot 2022-12-27 at 1.04.42 AM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35bad419-3a8d-4da2-a86f-37904d8fe24d/Screenshot_2022-12-27_at_1.04.42_AM.png)

### <base> Tag:

This tag is used to set the base URL for any anchor tag. So that we don’t need to specify the whole link all the time.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>This is a title</title>
        <base href="https://www.freecodecamp.org/" target="_blank">
    </head>
    <body>
        <a href="learn/">Click ME!!</a>
    </body>
</html>
```

As we see in the code, we specified the base URL as [https://www.freecodecamp.org/](https://www.freecodecamp.org/) 

In the anchor tag, we specified “learn/”. So, when the user clicks on Click ME!! he will be redirected to [https://www.freecodecamp.org/learn/](https://www.freecodecamp.org/learn/) 

This will be opened in a new tab because we specified target as _blank.

There are 4 kinds of targets:

1. **_blank :** This is used to open the specified link in a new tab.
2. **_self :** This is used to open the specified link in the same tab.
3. **_parent :** This is used to open the specified link in the parent frame(in this example on the same page).
4. **_top :** This is used to open the specified link in a window. For example, if we click the link in an iframe, instead of opening the page in the iframe it will be opened on the same window.

### <link> Tag:

This tag is used to specify the relationship between the HTML file and the external source.
Normally we will be used to link the stylesheet or linking icons, images, etc.

For more look into the link below: [https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link)

**Sample Input and Output:**

```html
<!DOCTYPE html>
<html>
    <head>
        <title>This is a title</title>
        <link rel="stylesheet" href="LearningCSS.css" />
    </head>
    <body>
        <p class="atag">Para</p>
    </body>
</html>
```

**LearningCSS.css:**

```css
.atag{
    color: red;
    font-size: 30px;
    font-style: italic;
}
```

The output will be looking something like this:

![Screenshot 2022-12-28 at 12.05.45 AM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1ac21262-143e-4aab-8b82-51361dcab7b6/Screenshot_2022-12-28_at_12.05.45_AM.png)

### <meta> Tag:

By using the <meta> tag you can able to specify the metadata for the page. For example, you can set up the Author, keywords, description, etc. But this will not be displayed on the screen.

```css
<!DOCTYPE html>
<html>
    <head>
        <title>This is a title</title>
        <link rel="stylesheet" href="LearningCSS.css" />
        <meta charset="UTF-8">
        <meta name="description" content="Example for meta">
        <meta name="keywords" content="HTML, CSS, JavaScript">
        <meta name="author" content="Sai Chetan">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body>
        <p class="atag">Para</p>
    </body>
</html>
```

For more info: [https://www.w3schools.com/tags/tag_meta.asp](https://www.w3schools.com/tags/tag_meta.asp)

### <script> Tag:

This tag is used to add the JS in HTML.

**Sample Code:**

```html
<!DOCTYPE html>
<html>
    <head>
        <title>This is a title</title>
        <link rel="stylesheet" href="LearningCSS.css" />
        <script>
            function buttonClicked(){
                document.getElementById('data').innerText = "Button clicked";
            }
        </script>
    </head>
    <body>
        <p id="data" class="atag">Para</p>
        <button onclick="buttonClicked()">Click Me!!</button>
    </body>
</html>
```

**OUTPUT:**

Before Button Click:

![Screenshot 2022-12-28 at 12.25.49 AM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d6da7bcd-0727-450a-b3ed-ed21516117d1/Screenshot_2022-12-28_at_12.25.49_AM.png)

After Button Click:

![Screenshot 2022-12-28 at 12.26.10 AM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/682d5818-8db3-4341-9e35-b52d2c2183d6/Screenshot_2022-12-28_at_12.26.10_AM.png)

### <noscript> Tag:

If the script tag is not working in the browser, then the <noscript> tag will show the content present inside of that tag to the user.

```html
<script>
document.write("Hello World!")
</script>
<noscript>Your browser does not support JavaScript!</noscript>
```