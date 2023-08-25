---
author: Joost Mans
title: mstyle usage
date: 2023-08-24
lastmod: 2023-08-22
Description: Description on how to use the mstyle shortcode
layout: docs
showComments: false
---
<!-- cSpell:ignore Joost shortcode lastmod mstyle shortcodes -->

## mstyle shortcode

The source code of the `mstyle` shortcode is located in `layouts/shortcodes/mstyle.html`.  
The purpose of this shortcode is to allow for limited set of styling functionality that can be applied to text.

### Parameters

The mstyle shortcode supports the below parameters.

Parameter|Description
---|---
class|The class or classes that are to be applied.
{.table}
{{< mstyle "br" />}}

The `mstyle` tag is to be used as follows:

```go-html-template
{{</* mstyle "class1 class2 class3" */>}}The text to style{{</* /mstyle */>}}
```

Any existing class can be used.

### Examples

```go-html-template
This is text using subscript: H{{</* mstyle "sub" */>}}2{{</* /mstyle */>}}O.
```

This is text using subscript: H{{< mstyle "sub" >}}2{{< /mstyle >}}O.

```go-html-template
This is text using superscript: I{{</* mstyle "sup" */>}}2{{</* /mstyle */>}}C.
```

This is text using superscript: I{{< mstyle "sup" >}}2{{< /mstyle >}}C.

```go-html-template
This is {{</* mstyle "small" */>}}small{{</* /mstyle */>}} text that is also {{</* mstyle "small underline" */>}}underlined{{</* /mstyle */>}}.
```

This is {{< mstyle "small" >}}small{{< /mstyle >}} text that is also {{< mstyle "small underline" >}}underlined{{< /mstyle >}}.
