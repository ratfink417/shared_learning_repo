# Manipulating Images
Okay so Markdown is great and all for all kinds of things but for shit that obsidian doesn't support it gets really crappy. Biggest issue is working with images. So far the only way i knew was injecting some html such as this:

```html
<p align = "center" > 
<img src="https://c.tenor.com/r9YVVor4FS0AAAAC/rick-and-morty-pass-the-butter.gif" /> </p>

```
This produces this:
<p align = "center" > <img src="https://c.tenor.com/r9YVVor4FS0AAAAC/rick-and-morty-pass-the-butter.gif" /> </p>

Well that works great if you want to use HTML but what if you want to link to an image without it being on the internet. Normal way in Obsidian markdown is with

```markdown

![](rick-and-morty-pass-the-butter.gif) 

```
but that produces this:

![](rick-and-morty-pass-the-butter.gif#) 

The problem is you can't use markdown syntax inside html tags. I mean it would be kind of stupid to exit one language to enter another to exit it again. 

# Using CSS Snippets
A little work around is to add a CSS snippet to obsidian specifying a span or a div or whatever with instructions to center the heading in `.obsidian/snippets` and then enable them under **Settings --> Appearance --> CSS Snippets**

Now CSS snippets work great but for some reason no matter what i attempt to specify  in the code it will NOT center an image. If i remove the image header and just leave the code for modifying the text it does it easy peasy lemon squeezy. So here's the HACK. You cant center the image but you can center the image's **alternative text** field!

```CSS

/* Use to float an image Right. When padding, add left padding */
img[alt$=">"] {
 float: right;
 padding-left:10px;
}

/* Use to float an image Right. When padding, add right padding */
img[alt$="<"] {
 float: left;
 padding-right: 10px;
}

img[alt$="><"] {
	display: block;
	max-width: 100%;
	height: auto;
	margin: auto;
	float: none!important;
}
```
Now you can move the image by using "image alt" followed by ">", " <", or "><"!

## Float Right
``` markdown 
![image alt >]("Image File")
```
![image alt >](rick-and-morty-pass-the-butter.gif) 

This will float the image to the right but you will probably need to ensure there is adequate text to shift the image boundary. You may need to throw a few `<br/>` after the text before proceeding on to the next paragraph.
<br/>

## Float Left
``` markdown 
![image alt <]("Image File")
```
![image alt <](rick-and-morty-pass-the-butter.gif) This will float the image left but again as mentioned in [Float Right](#Float%20Right) you will want to add additional space with `<br/>` a few times before moving on to the next paragraph. You may want to specify some padding in the css file as well so the text isn't butted right up against the image.  But knowing the work around is the most important part of this explanation.

## Center Image
``` markdown 
![image alt ><]("Image File")
```
Will produce: 
![image alt ><](rick-and-morty-pass-the-butter.gif) 
## Centering Tables
Tables are easily entered via Markdown utilizing pipes, colons and dashes.

Coding example below:

```markdown
| Left Justified | Center Justified | Right Justified |
|---------------- |:----------------: |-------------: | 
| Field A1 | Field A2 | Field A3 |
| **bold** | _Italics_ | **_Bold Italics_** |
```

| Left Justified | Center Justified | Right Justified |
|---------------- |:----------------: |-------------: | 
| Field A1 | Field A2 | Field A3 |
| **bold** | _Italics_ | **_Bold Italics_** |

The Reason this table is centered is because all tables in these markdown documents are centered thanks to this snippet:

```css
table {
 justify-content: center;
 margin: auto;
}
```
The only issue is i have NO IDEA how to specify the table using class id or anything as none of that works. It maybe an issue with the theme I'm using. All I'm able to do is say, "hey if your a table GO here". Yea.... well work in progress...