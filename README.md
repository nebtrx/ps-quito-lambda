# Instructions

1. Run `npm install -g markdown-to-slides`

2. Open file `/usr/local/lib/node_modules/markdown-to-slides/template/template.html`
and replace line 16 with the following one:
```
var slideshow = remark.create({ ratio: "16:9" });
```

3. cd into the root dir of this repo and run:

```
markdown-to-slides purescript.md -s style.css -o slideshow.html
```
