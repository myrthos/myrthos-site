---
author: Joost Mans
title: mstyle shortcode installation
date: 2023-08-24
lastmod: 2023-08-24
Description: Information on how to install the mstyle shortcode
layout: docs
showComments: false
---
<!-- cSpell:ignore Joost mstyle shortcode shortcodes lastmod Myrthos Hinode -->
## Installation to a Hugo site

If you want to use the mstyle shortcode in your Hugo site, just copy the following file from the Myrthos {{< link "https://github.com/myrthos/myrthos-site" >}}repository{{< /link >}} to your site's shortcode folder:

- layouts/shortcodes/mstyle.html

Also copy the following file to your scss/css folder:

- assets/scss/components/_mstyle.scss

If your site is not based on Hinode, rename the file to `mstyle.css`.

Lastly on a site using the Hinode theme add the following to `assets/scss/app.scss`:

```scss
@import "components/mstyle.scss";
```
