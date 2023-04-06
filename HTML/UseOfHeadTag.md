# Use of `<head>` tag

`<head>` tag is used to store the metadata(data about the data) inside it. It specifies about the HTML file. This is not displayed on the webpage.

We can use several tags inside <head> tag. Some of them are:

- `<title>`
- `<style>`
- `<base>`
- `<link>`
- `<meta>`
- `<script>`
- `<noscript>`

### `<title>` Tag:

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

![image](https://user-images.githubusercontent.com/52970245/230412366-d96447fc-3486-48db-b8cf-6052c3d24656.png)

### `<style>` Tag:

This tag is used to style the HTML. We can specify CSS inside the `<style>` tag.

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

![image](https://user-images.githubusercontent.com/52970245/230412517-b4da0698-c153-40e5-9486-babf6091c0b5.png)

### `<base>` Tag:

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

### `<link>` Tag:

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
![image](https://user-images.githubusercontent.com/52970245/230413180-b60740ce-d8e5-4629-80ef-c0e1432daddd.png)

### `<meta>` Tag:

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

### `<script>` Tag:

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

![image](https://user-images.githubusercontent.com/52970245/230413550-af97cd0f-b529-4e57-9d8c-19c00d211735.png)

After Button Click:

![image](https://user-images.githubusercontent.com/52970245/230413635-432500df-7576-49d7-bca6-8c0ee3900146.png)

### `<noscript>` Tag:

If the script tag is not working in the browser, then the <noscript> tag will show the content present inside of that tag to the user.

```html
<script>
document.write("Hello World!")
</script>
<noscript>Your browser does not support JavaScript!</noscript>
```
