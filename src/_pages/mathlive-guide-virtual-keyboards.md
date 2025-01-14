---
layout: single
date: Last Modified
title: MathLive Guide - Virtual Keyboards
permalink: /mathlive/guides/virtual-keyboards/
read_time: false
sidebar:
    - nav: "mathlive"
head:
  stylesheets:
    - https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.48.0/codemirror.min.css
  scripts:
    - https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.48.0/codemirror.min.js
    - https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.48.0/mode/javascript/javascript.min.js
    - https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.48.0/mode/xml/xml.min.js
  modules:
    - /assets/js/code-playground.js
---
<script>
    moduleMap = {
        mathlive: "//unpkg.com/mathlive/dist/mathlive.min.mjs",
        "html-to-image": "///assets/js/html-to-image.js",
    };
</script>

# Virtual Keyboards

Typing math formulas require access to many special symbols. While [keyboard 
shortcuts and inline shortcuts](/mathlive/guides/shortcuts) can help, mobile 
devices require another solution. A virtual keyboard is a keyboard displayed 
on screen that can be customized with specialized symbols for math input.

![](/assets/images/mathfield/virtual-keyboard.png)


To display variants related to a key, press and hold it. Not all keys have
variants, though.

![](/assets/images/mathfield/alt-keys.png){.max-w-md}

![](/assets/images/mathfield/more-alt-keys.png){.max-w-md}

The mathfield virtual keyboard panel can display multiple keyboards. 
The default keyboards are **numeric**, **functions**, **symbols**, 
**roman letters** and **greek letters**.

These keyboards  include the most common math symbols and they can be 
customized to fit your specific needs. For example you can remove some 
keyboards, or create new ones.


## Controlling when the virtual keyboard panel is displayed

The default behavior of the virtual keyboard panel is to only be displayed on 
touch-enabled devices (mobile phones, tablets, laptops with a touch-screen) when
a matfield is focused.

![](/assets/images/mathfield/mobile-virtual-keyboard.png){.max-w-md}


This behavior can be changed with the `virtualKeyboardMode` configuration property:
- `"auto"`:  this is the default behavior: on touch-enabled devices, show the 
virtual keyboard panel when the mathfield is focused, otherwise, don't show it.
- `"manual"`: a toggle button to control the virtual keyboard panel is displayed in the
mathfield
- `"onfocus"`: the virtual keyboard panel is displayed when the mathfield is focused
- `"off"`: never show the virtual keyboard panel

There is only one virtual keyboard panel displayed at a time, but each mathfield
can specify different virtual keyboard panel configurations. {.notice--info}

## Controling which keyboards are displayed

The virtual keyboard panel displays multiple keyboards which can be 
toggled using the keyboard switcher: `numeric`, `functions`, `symbols`, `roman` 
and `greek`.

To choose which keyboards are available, use the `virtualKeyboards` 
configuration property. The value of this property is a space-separated string 
of the name of the keyboards that should be displayed.

<code-playground layout="stack" class="m-lg w-full-lg">
    <div slot="javascript">import MathLive from 'mathlive';
MathLive.makeMathField(document.getElementById('mathfield'),  {
  virtualKeyboardMode: "manual",
  virtualKeyboards: 'numeric symbols'
});
</div>
    <div slot="html">&lt;div id="mathfield"&gt;x=\frac{-b\pm \sqrt{b^2-4ac}}{2a}
&lt;/div&gt;
</div>
</code-playground>


## Defining custom virtual keyboards

A virtual keyboard is made up of one or more "layers". A keyboard layer is a 
set of keys that can be toggled using another key. For example, the `roman` 
keyboard has a regular layer and a layer for symbols. Most keyboards have a 
single layer.

Custom virtual keyboard layouts can be defined with an object literal.

### Defining a layer

First, let's define a layer, which we'll call `"high-school-layer"`.

A layer is an array of rows, a row is an array of keycaps. 

Each keycap can have the following properties:

- `class`: a set of CSS classes to style this keycap. The classes can be custom 
defined (see below about the `styles` layer property), or be one or more of the
 standard ones:
    - `tex`: use the TeX font for its label.
      Using the `tex` class is not necessary if using the `latex` property to 
      define the label.
    - `small`: display the label in a smaller size
    - `modifier`: a modifier (shift/option, etc...) keycap
    - `action`: an "action" keycap (for arrows, return, etc...)
    - `separator w5`: a half-width blank used as a separator. Other widths
    include `w15` (1.5 width), `w20` (double width) and `w50` (five-wide, used 
    for the space bar).
    - `bottom`, `left`, `right`: alignment of the label
- `label`: the HTML markup displayed for the keycap. If no `label` is provided,
  the `latex` value is used. If no `key`, `command` or `insert` property is 
  provided, the label (stripped of any HTML markup) is what will be inserted
  when the keycap is pressed.
- `latex`: a Latex fragment used as a label and as the content to insert when 
  the keycap is pressed.
- `key`: the keypress to emulate when the keycap is pressed, for example `"5"`.
- `aside`: an optional small label displayed below the keycap. This label
may not be displayed if the space available is too small.
- `insert`: a Latex fragment to insert when the keycap is pressed. The `latex`
property is usually sufficient, 
- `altKeys`: the name of a set of alternate keys to display when the keycap is
long-pressed.
- `command`: the command to perform when the keycap is pressed.
- `shifted`: the HTML markup to display as the label of the keycap when the
shift key is pressed
- `shiftedCommand`: the command to perform when the keycap is pressed with
the shift key.

The following properties, in order, are used to determine how to display 
they keycap: `latex`, `label`, `insert`.

When the keycap is pressed, the following properties are used, in order, to 
determine the action to perform: `command`, `insert`, `latex`, `key`.

To associate a CSS stylesheet with this layer, use the `styles` property of the
layer.

The appearance of the layer can be further customized by providing a CSS class 
name for the `backdrop` and `container` properties of the layer.


### Keycap Variants

The default keyboard include **variants** for many of its keycaps. These
variants are accessed with a long press on the keycap. The variants are 
typically related, but less frequently used version of the main keycap.

You can define variants for a custom keyboard by specifying a `variants` 
property with the definition of a keycap. The value of the `variants` property
is an array of `VirtualKeyboardKeycap`. As a shortcut, a string can also be 
used, which is equivalent to a `VirtualKeyboardKeycap` with a `latex` property
equal to the string, that is, it will display the latex string as the keycap
label and insert it when the key is pressed.

```js
rows: [
  [
    { latex: "a", variants: ["A", "\\alpha", "\\Alpha"] }
    ...
  ]
]
```


### Keycap Examples

Each keycap must define its label and what happens when the keycap is pressed.
There are many different ways of doing this using the properties above.

The simplest case is when pressing a key insert some Latex and its label
is the fragment to be inserted. In that case, the `latex` property can be 
used to specify both the label and what happens when the key is pressed.

They `keycap` class indicates that the appearance of the key should be 
of a regular keycap, as opposed to a modified key (`modifier`) or an action 
key (`action`).

```js
  { latex: "\\beta" },
  { latex: "\\mu_0" },
  { latex: "\\frac{1}{2}" },
```

To display the key label as a some HTML markup instead, use the `label` 
property:

```js
  { label: "8", latex: "8" },
  { class: "tex", 
    latex: "<span><i>x</i>&thinsp;²</span>", 
    latex: "8" 
  },
```

The keycap label is displayed using the system font when the `label` property
is used. To display it using the TeX fonts instead, set the value of the `class`
property to `tex`.

To perform a command other than inserting some Latex, use the `command`
property:

```js
  {
    class: "action",
    label: "<svg><use xlink:href='#svg-arrow-left' /></svg>",
    command: ["performWithFeedback", "moveToPreviousChar"]
  },
```



```js
    const HIGH_SCHOOL_KEYBOARD_LAYER = {
      "high-school-layer": {
        styles: "",
        rows: [
          [
            { latex: "a" },
            { latex: "x" },
            { class: "separator w5" },
            { label: "7", key: "7" },
            // Will display the label using the system font. To display 
            // with the TeX font, use:
            // { class: "tex", label: "7", key: "7" },
            // or 
            // { latex: "7"},
            { label: "8", key: "8" },
            { label: "9", key: "9" },
            { latex: "\\div" },
            { class: "separator w5" },
            {
              class: "tex small",
              label: "<span><i>x</i>&thinsp;²</span>",
              insert: "$$#@^{2}$$"
            },
            {
              class: "tex small",
              label: "<span><i>x</i><sup>&thinsp;<i>n</i></sup></span>",
              insert: "$$#@^{}$$"
            },
            {
              class: "small",
              latex: "\\sqrt{#0}",
              insert: "$$\\sqrt{#0}$$",
            }
          ],
          [
            { class: "tex", latex: "b" },
            { class: "tex", latex: "y" },
            { class: "separator w5" },
            { label: "4", latex:"4" },
            { label: "5", key: "5" },
            { label: "6", key: "6" },
            { latex: "\\times" },
            { class: "separator w5" },
            { class: "small", latex: "\\frac{#0}{#0}" },
            { class: "separator" },
            { class: "separator" }
          ],
          [
            { class: "tex", label: "<i>c</i>" },
            { class: "tex", label: "<i>z</i>" },
            { class: "separator w5" },
            { label: "1", key: "1" },
            { label: "2", key: "2" },
            { label: "3", key: "3" },
            { latex: "-" },
            { class: "separator w5" },
            { class: "separator" },
            { class: "separator" },
            { class: "separator" }
          ],
          [
            { latex: "(" },
            { latex: ")" },

            { class: "separator w5" },
            { label: "0", key: "0" },
            { latex: "." },
            { latex: "\\pi" },
            { latex: "+" },
            { class: "separator w5" },
            {
              class: "action",
              label: "<svg><use xlink:href='#svg-arrow-left' /></svg>",
              command: ["performWithFeedback", "moveToPreviousChar"]
            },
            {
              class: "action",
              label: "<svg><use xlink:href='#svg-arrow-right' /></svg>",
              command: ["performWithFeedback", "moveToNextChar"]
            },
            {
              class: "action font-glyph bottom right",
              label: "&#x232b;",
              command: ["performWithFeedback", "deleteBackward"]
            }
          ]
        ]
      }
    };
```

### Defining a keyboard

Now, let's define a new keyboard called `"high-school-keyboard"`.

The keyboard has a `label` which is displayed in the keyboard selector area
of the virtual keyboard. When hovering on this label, a `tooltip` can be 
displayed to further explain what the keyboard is about.

Finally, we must provide the layers that make up this keyboard. 

In this example we have a single layer, so we can set the value of the 
`layer` property to the name of the layer.

If we had several layers, we could set the property `layers` to an 
array of their names.


```js
    const HIGH_SCHOOL_KEYBOARD = {
      "high-school-keyboard": {
        "label": "High School", // Label displayed in the Virtual Keyboard Switcher
        "tooltip": "High School Level", // Tooltip when hovering over the label
        "layer": "high-school-layer"
      }
    };
```

### Adding the layers and keyboards

Now, we just have to call `setOptions()` with our custom layer and custom
keyboard.

The `virtualKeyboards` option indicate which keyboards we want to be available.
Here we only want our keyboard, so we'll just pass its name.

If we wanted multiple keyboards, we could provide a space-separated list, for
example `"numeric high-school-keyboard symbols"`. If we wanted all the default
keyboards, plus ours, we could have used the `"all"` shortcut: 
`"all high-school-keyboard"`.


```js
  mf.setOptions({
    "customVirtualKeyboardLayers": HIGH_SCHOOL_KEYBOARD_LAYER,
    "customVirtualKeyboards": HIGH_SCHOOL_KEYBOARD,
    "virtualKeyboards": "high-school-keyboard"
  });
}
```


## Customizing the appearance of the virtual keyboard toggle

By specifying a CSS rule for the `.ML__virtual-keyboard-toggle` selector the 
appearance of the virtual keyboard toggle can be modified.

Make sure the rule has sufficient CSS specificity to be applied. Add a `!important`
directive or prefix the selector with the id of your mathfield. {.notice--warning}

<code-playground layout="stack" class="m-lg w-full-lg">
    <div slot="javascript">import MathLive from 'mathlive';
MathLive.makeMathField(document.getElementById('mathfield'),  {
  virtualKeyboardMode: 'manual'
});
</div>
    <div slot="html">&lt;style&gt;
  .ML__virtual-keyboard-toggle {
    color: red !important;
  }
&lt;/style&gt;
&lt;div id="mathfield"&gt;x=\frac{-b\pm \sqrt{b^2-4ac}}{2a}
&lt;/div&gt;
</div>
</code-playground>


The icon of the virtual keyboard toggle can be changed using the `virtualKeyboardToggleGlyph`
configuration property.

<code-playground layout="stack" class="m-lg w-full-lg">
    <div slot="javascript">import MathLive from 'mathlive';
MathLive.makeMathField(document.getElementById('mathfield'),  {
  virtualKeyboardMode: "manual",
  virtualKeyboardToggleGlyph: `&lt;span style="width: 21px;">&lt;svg style="width: 21px;" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"&gt;&lt;path fill="currentColor" d="M192 288H32c-18 0-32 14-32 32v160c0 18 14 32 32 32h160c18 0 32-14 32-32V320c0-18-14-32-32-32zm-29 140c3 3 3 8 0 12l-11 11c-4 3-9 3-12 0l-28-28-28 28c-3 3-8 3-12 0l-11-11c-3-4-3-9 0-12l28-28-28-28c-3-3-3-8 0-12l11-11c4-3 9-3 12 0l28 28 28-28c3-3 8-3 12 0l11 11c3 4 3 9 0 12l-28 28 28 28zM480 0H320c-18 0-32 14-32 32v160c0 18 14 32 32 32h160c18 0 32-14 32-32V32c0-18-14-32-32-32zm-16 120c0 4-4 8-8 8h-40v40c0 4-4 8-8 8h-16c-4 0-8-4-8-8v-40h-40c-4 0-8-4-8-8v-16c0-4 4-8 8-8h40V56c0-4 4-8 8-8h16c4 0 8 4 8 8v40h40c4 0 8 4 8 8v16zm16 168H320c-18 0-32 14-32 32v160c0 18 14 32 32 32h160c18 0 32-14 32-32V320c0-18-14-32-32-32zm-16 152c0 4-4 8-8 8H344c-4 0-8-4-8-8v-16c0-4 4-8 8-8h112c4 0 8 4 8 8v16zm0-64c0 4-4 8-8 8H344c-4 0-8-4-8-8v-16c0-4 4-8 8-8h112c4 0 8 4 8 8v16zM192 0H32C14 0 0 14 0 32v160c0 18 14 32 32 32h160c18 0 32-14 32-32V32c0-18-14-32-32-32zm-16 120c0 4-4 8-8 8H56c-4 0-8-4-8-8v-16c0-4 4-8 8-8h112c4 0 8 4 8 8v16z"/&gt;&lt;/svg&gt&lt;/span>`
});
</div>
    <div slot="html">&lt;style&gt;
  div.ML__virtual-keyboard-toggle{
    color: #555;
  }
  div.ML__virtual-keyboard-toggle:hover {
    background: transparent;
    color: var(--primary);
    box-shadow:  none;
  }
&lt;/style&gt;
&lt;div id="mathfield"&gt;x=\frac{-b\pm \sqrt{b^2-4ac}}{2a}
&lt;/div&gt;
</div>
</code-playground>


## Changing the arrangement of alphabetical keys

By default, the layout of the alphabetic virtual keyboard is determined based
on the locale (QWERTY for english speaking countries, AZERTY for french speaking
countries, etc..). 

To select a different alphabetic keyboard layouts, such as DVORAK and COLEMAK, 
use the `virtualKeyboardLayout` configuration property.

<code-playground layout="stack" class="m-lg w-full-lg">
    <div slot="javascript">import MathLive from 'mathlive';
MathLive.makeMathField(document.getElementById('mathfield'),  {
  virtualKeyboardMode: "manual",
  virtualKeyboardLayout: 'dvorak'
});
</div>
    <div slot="html">&lt;div id="mathfield"&gt;x=\frac{-b\pm \sqrt{b^2-4ac}}{2a}
&lt;/div&gt;
</div>
</code-playground>


## Customizing the appearance of the virtual keyboard keycaps

The appearance of keycaps can be controlled with the following CSS variables:
  - `--keycap-height`
  - `--keycap-font-size`
  - `--keycap-small-font-size` (only if needed)
  - `--keycap-extra-small-font-size` (only if needed)
  - `--keycap-tt-font-size` (only if needed)


## Sharing virtual keyboards amongst multiple instances

When there are multiple mathfield elements in a page, they usually each have
their own virtual keyboard.

However, in some cases it might be desirable to have the virtual keyboard
instance "detached" from the mathfield, and potentially shared by multiple 
mathfield elements. 

That's the case for example when mathfield elements are displayed in an 
_iframe_. Since an _iframe_ is essentially a mini embedded document, the 
default virtual keyboard will not display correctly when use in an _iframe_, 
as it will be attached to the _iframe_ instead of being attached to the 
main document.

This can be solved by using a "shared" virtual keyboard.

To use a shared virtual keyboard, call the `makeSharedVirtualKeyboard()` 
function in the context of the main document.

```javascript
makeSharedVirtualKeyboard({
  virtualKeyboardLayout: 'dvorak',
});
```

In the _iframe_ sub-documents, use the `use-shared-virtual-keyboard` attribute on 
the `math-field` elements.

```html
<math-field use-shared-virtual-keyboard></math-field>
```


## Next

<a href="/mathlive/guides/lifecycle">Web Component Lifecycle<span><i class="fas fa-chevron-right navigation"></i><span></span></a>
:    Understand in depth the lifecycle of a MathfieldElement: construction, 
interaction with the DOM and when can you communicate with it.

## See Also
* <a href="/docs/mathlive/">MathLive SDK<span class='ml-sm'><i class="fas fa-chevron-right navigation"></i><span></span></a>
