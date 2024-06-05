
## Fonts

### Google Fonts locally

1. Browse fonts on https://fonts.google.com/
2. Find the font on https://gwfh.mranftl.com/fonts and download the font
3. Add the following to the css file:

   ```css
    body .reveal {
        font-family: "Yanone Kaffeesatz", "normal";
    }
    ```





## Getting started

To get started with revealjs, do the following steps:

```sh
# use revealjs as a submodule
git submodule add https://github.com/hakimel/reveal.js revealjs
git submodule update --init --recursive

# copy the template index.html to the root folder
cp revealjs/index.html .

# adjust the `href` or `src` paths
perl -i -p -e 's/(href|src)="/$1="revealjs\//g;' index.html 
```


## Blurring background images

To selectivly blur background images, add the following to your custom css:

```css
.blurred .slide-background-content {
  filter: blur(5px)
}
```

If you now apply the class `.blurred` to a revealjs slide, the background image of this slide is blurred. 

Using revealjs natively (see [here](https://github.com/ratnanil/zhaw-statistiker-2022/blob/4d04aa613f3f5af0806b9c94ad1c924ce3a73bfc/index.html#L148)):

```html
<section class="blurred" data-background-image="5790836212_87a89fe3b9_o.jpg">
``` 

When using revealjs in quarto:

```md
##  {background-image="img/AGI_HS20_02_Coding_in_GIS_Ia6.jpg" class="blurred"}
```

## Draw on Images / Highlight parts of images

Sometimes, I want to highlight parts of an image, i.e. draw a square box around something (usually a screenshot). ~~I've not fully understood how to achieve this yet, but I've developped a working system. *Very experimental / Hacky*~~ I think I've found a good way using svg:

In the styles section:

```css
.img-overlay {
    position: relative;
    background-size: contain;
    background-repeat: no-repeat;
    width: 80%;
    padding-top: 43.95%; /* see comment below*/
}

.img-overlay svg {
    position: absolute;
    top: 0;
    left: 0;
}

#container-1{
    background-image: url('Screenshot.png');
    height: 500px;
}

.mark-it{
    fill-opacity: 0;
    stroke: red;
    stroke-width: 2px;

}
```


In the slides-html

```html
<section>				
  <div id="screenshot-0" class="img-overlay">
    <svg viewBox="0 0 1000 1000">
      <rect class="fragment fade-in-then-out mark-it" data-fragment-index="4" ry="5" x="785" y="288" width="100", height="22"/>
    </svg>
  </div>
</section>
``` 

- The padding-top ~~trick is from [here](https://stackoverflow.com/a/43806724/4139249) (But rather than using an `<img>`, I'm using a background image)~~: 

$$\text{padding-top} = \frac{\text{img width in px}}{\text{img height in px}}\times \text{width of div} = \frac{1055px}{1920px} \times 0.8 = 43.95$$

- This seems to work fine also in the mobile version
- Note `viewBox=0 0 1000 1000`
  - this specifies the coordinate system for the svg image (a "canvas" with 1000 units with and height)
  - this makes the svg "canvas" as high as it is wide. Usually, this means the svg canvas is longer than the image, which is fine since the coordinates start (0/0) in the top left corner



## Controlling fragments

You can customize fragment-behaviour for a specific class:

```
.fragment.custom-frag{
  visibility: visible !important;
  opacity: 1 !important;
}

.fragment.custom-frag.visible{
  /* some attributes */
}

.fragment.custom-frag.current-fragment {
  padding: 10px;
  outline: 3px solid green;
  outline-offset: 4px;
}
```


## Notes with Markdown

When simply jotting things down for internal notes, I can use markdown. Simply add teh attribute `data-markdown` to the `<aside>`. However, the notes must be alinged to the left of the document, `data-trim` seems to have no effect.

```
<aside class="notes" data-markdown>
- beer: free of charge
- speech a matter of liberty:
    - use the software as you like
    - see how the software works (source code)
    - redistribute the software
    - improve the software                              
</aside>

```

## Controlling the Presentation with mouse keys


https://www.raymondcamden.com/2012/10/20/Adding-mouse-click-navigation-to-revealjs/

> One thing I was curious about was whether I could use mouse clicks to navigate a presentation. Why? I'm considering using a Bluetooth device to navigate a presentation while I'm away from my laptop
> 
> ...
> 
> The first line sets up the event handler for mouse clicks. The second event handler disables the context menu. This is necessary for right menu clicks. Without it you get the normal right click context menu.
> 
> Finally, the actual event handler is somewhat trivial. You check which button was clicked and then use the Reveal API to go either forward or backward. (The Logitech app supports a center mouse button too. You could bind that to the overview feature, which shows you all the slides.)

```js
window.addEventListener("mousedown", handleClick, false);
window.addEventListener("contextmenu", function (e) { e.preventDefault(); }, false);

function handleClick(e) {
  e.preventDefault();
  if (e.button === 0) Reveal.next();
  if (e.button === 2) Reveal.prev();
}
```


## Code

Codeblocks are wrapped in `pre` and `code` like so:

```html
<pre><code>
    print("hi")
</code></pre>
```

The `<code>`elements accepts the following attributes:

- `data-trim`: trim all white spaces before the code (helpful for indented code in html files)
- `data-noescape`: Don't escape html (see note below)
- `data-line-numbers`: show line numbers.
- `data-ln-start-from="7"`: Offset the line numbers by 7
- `data-line-numbers="3,8-10"`: Highlight these line numbers sequencially
- `class="js"`: Set's the syntax highlighting to javascript. See [supported languages](https://highlightjs.readthedocs.io/en/latest/supported-languages.html). Eg. Plaintext` (plaintext, txt, text), `R`, `Python`, `CSS`


Note: `data-noescape` does not seem to escape `<>`. In this case:

> Content added inside of a `<code>` block is parsed as HTML by the web browser. If you have HTML characters (<>) in your code you will need to escape them ($lt; $gt;).
> To avoid having to escape these characters manually, you can wrap your code in `<script type="text/template">` and we'll handle it for you.

## Decker

https://github.com/mbotsch/mb-reveal-plugins/issues/7

> I teamed up with some other people to develop a tool named [decker](https://github.com/decker-edu/decker), which compiles slides written in Markdown to a Reveal presentation and is also used to present these slides. It contains a couple of Reveal plugins, including one for chartjs, which is a decker-customized version of [this original](https://github.com/rajgoel/reveal.js-plugins).
> 
> If you are interested, [here](https://ls7-gv.cs.tu-dortmund.de/downloads/elearning/slides/eLearning-deck.html#/title-slide) is an example presentation that demonstrates what we can do (unfortunately it's in German). [Here](https://github.com/mbotsch/eLearning) is the source code for this presentation.



## Header / Footer

- For a footer only on a single slide, try this: https://github.com/hakimel/reveal.js/issues/180#issuecomment-1140166635
- For a header / footer on every page, add something like this *after* the `<div class="reveal"></div>` and *before* `<script> ... </script>`
  ```html
  <div style="top: 10px; left: 10px; position: absolute;">
    <img src="images/zhaw_sw_neg.png" style="height: 40px;">
  </div>
  ```

Todo: This often does not scale well with low resolution beamers. Test this!


## Markdown


### External Markdown

If you write your presentation primarily in markdown (but not using quarto), you can use external markdown:


```html
<section> data-markdown="external.md" data-seperator=">>>" data-seperator-vertical="---"
```

- `data-markdown`: Name of the external file
- `data-seperator`: A regex of the seperator to use for seperating slides (what's the default?)
  - If you hat `$$$` literally as you seperator, you would have to escape them (`\$\$\$`)
- `data-seperator-vertical`: A regex of the seperator to use for seperating *vertical* slides (what's the default?)
- `data-seperator-notes`: A seperator for speaker notes:


## Slides

### Name Slides / Link to Slides


Add an `id` attribute to a section:

```html
<section id="my-id">

</section>
```

Which can be linked like this:

```html
<a href="#/my-id">Go to "my-id"-Slide</a>

<a href="#/0">Go to the first slide</a>
```


### Hide Slides

hide slide with `data-visibility` attribute

```html
<section data-visibility="hidden"></section>
```

`uncounted` if it should not appear in the couter:

```html
<section data-visibility="uncounted"></section>
```


### Slide state

`data-state` can be used as an attribute of a slide (`<section>`) to "activate" a state when that slide is visible. This has some cool implications, but I'm not sure how to implement them yet. This is a minimal example:

```htm
<section data-state="make-it-pop">
    This slide has a state `make-it-pop`, but only when it's visible (?)
</section>
```

The slide (and all its children?) get the class `make-it-pop`. This would be a great place to add an animation, right?
```css
.make-it-pop {
  filter: drop-shadow(0 0 10px purple);
}
```

More importantly, `make-it-pop` and be an event trigger in JS. I made [the example on the website](https://revealjs.com/markup/#slide-states) slightly more complicated just to highlight that the event is triggered *every time* the specific slide is made visible:

```js
var i;
i = 1;

Reveal.on('make-it-pop', () => {
		console.log(i++);
} );
```


## Config Options


<section>

See: https://revealjs.com/config/


You can set config options within `Reveal.initialize({})`.

```js
Reveal.initialize({
		hash: true
	})
```

You can change the config at runtime. Try running this in the console:

```js
Reveal.configure({hash: true})
```

You cat get the current state of options. Try running this in the console:

```js
Reveal.getConfig() // or
Reveal.getConfig()["hash"] // or
Reveal.getConfig().hash
```

Toggle!

```js
Reveal.configure({ controls: !Reveal.getConfig.controls})
```

Set configurations via query strings:

```js
mysite.com/myslides?controls=true&progress=false						
```

<!-- 
	https://app.gumroad.com/s/19143d47ed0764a4b30ab6141678b334/M2q2Z8Ec_vTZgcP-Zy5JPA== 
-->


## Help overlay

Config Option: help: true`
Should be active by default, via the `?` Key


## Plugins

- The search pluigin is very helpful (<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F</kbd>)
      
      <script src="revealjs/plugin/search/search.js"></script>

      Reveal.initialize({
			plugins: [RevealSearch]
		});

- [reveal.js-notes-pointer](https://github.com/djsutherland/reveal.js-notes-pointer): A laser pointer usable in the speaker mode
  - see example http://www.ratnaweera.xyz/reveal.js-notes-pointer/example.html
