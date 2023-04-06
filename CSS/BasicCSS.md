# Basic CSS

Normally CSS is used to present the HTML in such a. way that it looks good to human eyes. It is. basically styles the HTML.

We can include the CSS in HTML in 3 different ways:

- In-Line StyleSheet
- Internal StyleSheet
- External StyleSheet

### In-Line StyleSheet:

We specify the styles of the tag inside that particular tag itself.

For Example:

```html

<p styles="color: red;">This is a Paragraph</p>
```

So, here we mentioned the style of that particular paragraph inside itself. So now that paragraph text color will be in red.

### Internal StyleSheet:

In this kind, we mention all the styles inside the style tag.

For Example:

```html
<head>
	<style>
		p {
				color: red;
			}
	</style>
</head>
<body>
	<p>This is a paragraph</p>
</body>
```

Now all the p tag color will be turned to red because we specified the color in stylesheet.

### External StyleSheet:

Here, we create a new file with .css extension and we write all the CSS content over there. And we will be linking that stylesheet to HTML page.

For Example:
Consider we created a file styles.css and wrote the css there.

```css
p{
	color: red;
}
```

We will be linking that stylesheet to HTML like below code:

```html
<head>
	<link rel="stylesheet" href="styles.css"/>
</head>
```

Now the CSS will be referred to in HTML will use the stylesheet and render the elements.