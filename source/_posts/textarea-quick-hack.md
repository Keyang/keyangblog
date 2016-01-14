title: textarea quick hack
date: 2016-01-12 10:35:25
tags:
  - html
  - css
---
### Resize
Switch on / off resize of a textarea on horizontal / vertical / both.
```html
<textarea style="resize:horizontal"></textarea>
```
<textarea style="resize:horizontal"></textarea>

The resize property can be either:

* vertical: only resize vertically
* horizontal: only resize horizontally
* none: disable resize (also remove the resize area on right bottom.)

### Outline
Remove or change style of outline when textarea is focused on safari / chrome etc.
```html
<textarea class="customize"></textarea>
```
```css
.customize:focus{
  outline-color:red;
}
```

<style>.customize:focus{ outline-color:red; }</style>
<textarea class="customize"></textarea>
