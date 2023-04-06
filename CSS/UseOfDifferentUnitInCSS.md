# Uses of different units in CSS

Mainly you can see 5 types of units in CSS:

- Px (pixels)
- % (percentage)
- em
- rem
- hv

### px (Pixels):

px is the units used to size the elements. But the disadvantage of pixels is, if we use the px units, it won’t resize when a user changes the browser zoom level. 

For example, when a user changed the font size in the browser from default level to any other size, but we specified the specific px to the font size, the size of the font won’t change, which is a big disadvantage.

### % (Percentage):

% is used to specify the units in a percentage manner. It is more dynamic than pixels.

### em:

em is a unit relative to a font size of a parent element. So even when a user changes the default font size in the browser, according to it the font changes.

For example, if we say the font size is 2em, that means whatever the font size of the parent, it will be doubled. This means if the parent element font size is 20px then if we specify 2em, the font size will be 40px.

### rem:

rem is a unit relative to the font size of the HTML element. It is same as em but the only difference is it is relative to the HTML element.

### hv:

hv is a unit used to size the elements with respect to the view port.

For more info on units: [Units in CSS](https://www.w3schools.com/cssref/css_units.php)