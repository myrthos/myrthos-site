---
author: Joost Mans
title: mgallery shortcode installation
date: 2023-07-04
lastmod: 2023-08-25
Description: Information on how to install the mgallery shortcode
layout: docs
showComments: false
---
<!-- cSpell:ignore Joost mgallery shortcode hinode shortcodes mimage lightbox frontmatter lastmod Myrthos -->
## Installation in a Hinode theme

The `mgallery` shortcode and all accompanying files are available as a {{< link "https://github.com/myrthos/mod-mgallery" >}}Hugo module{{< /link >}}.  
To load this module at startup add the following to the `[module]` section in `config/_default/hugo.toml`:

```toml
  [[module.imports]]
    path = "github.com/myrthos/mod-mgallery"
```

And to the `[modules]` section in `config/_default/params/params.toml`, `"mgallery"` to the `optional` parameter.

After this the module will be automatically loaded when `npm run start`, or `npm run build` is executed on the command line.

### Further configuration

As the goal is to load the javascript file only when it is needed, an entry in the frontmatter of the page where the `mgallery` shortcode is used, is required, which is the following:

```yaml
modules: ["mgallery"]
```

## Installation in a non-Hinode theme

Installing the `mgallery` module on a non-Hinode theme has not been tested, but as it is a Hugo module, installation of that module should be possible following the necessary steps. Using the `mgallery` shortcode without Hinode has been tested and also works when the additional shortcode parameter `hinode` is set tot `"false"'. This adds some required additional styling.  
For correct operation of the shortcode, Bootstrap version 5 is required.
