---
author: Joost Mans
title: mtag usage
date: 2023-08-24
lastmod: 2023-08-22
Description: Description on how to use the mtag shortcode
layout: docs
showComments: false
---
<!-- cSpell:ignore Joost shortcode lastmod mtag shortcodes -->

## mtag shortcode

The source code of the `mtag` shortcode is located in `layouts/shortcodes/mtag.html`.  
The purpose of this shortcode is to allow for limited set of HTML functionality to be used.

### Parameters

The mtag shortcode supports the below parameters.

Parameter|Description
---|---
tag|The HTML tag to insert. The following tags are supported{{< mtag "br" />}}"br"&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Add a line break{{< mtag "br" />}}"nobr"&nbsp;Keep text together to prevent it to be split over two lines
{.table}
{{< mtag "br" />}}
The below sections provide more information about the different tags.

#### br

The `br` tag can be used as follows:

```go-html-template
{{</* mtag "br" /*/>}}
```

#### nobr

The `nobr` tag can be used as follows:

```go-html-template
{{</* mtag "nobr" */>}}text that should stay together{{</* /mtag */>}}
```
