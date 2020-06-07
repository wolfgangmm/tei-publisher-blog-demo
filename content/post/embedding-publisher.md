---
title: "Embedding TEI Publisher Components"
date: 2020-06-05T20:41:18+02:00
tags: ['TEI Publisher', 'webcomponents']
draft: true
---

This example shows how to embed TEI content into a static website using markdown for the content pages. The site was created with [Hugo](https://gohugo.io/), a popular static website generator.

## Embed HTML directly

For sure you can directly embed HTML into markdown as demonstrated by the example below:

{{< rawhtml >}}
<pb-page endpoint="https://teipublisher.com/exist/apps/tei-publisher" emit="kant" class="embedded">
    <pb-document id="kant" path="test/kant_rvernunft_1781.TEI-P5.xml" odd="dta"></pb-document>
    <!-- Navigate to next page -->
    <pb-navigation direction="forward" keyboard="right" emit="kant" subscribe="kant">
        <paper-fab icon="icons:chevron-right"></paper-fab>
    </pb-navigation>
    <!-- Navigate to previous page -->
    <pb-navigation direction="backward" keyboard="left" emit="kant" subscribe="kant">
        <paper-fab icon="icons:chevron-left"></paper-fab>
    </pb-navigation>
    <pb-view src="kant" xpath="//teiHeader/fileDesc/titleStmt/title" emit="kant" subscribe="kant">
        <pb-param name="header" value="short" />
    </pb-view>
    <pb-view class="transcription" src="kant" view="page" emit="kant" subscribe="kant"
        append-footnotes animation></pb-view>
</pb-page>
{{< /rawhtml >}}

The data is loaded from the TEI Publisher instance running on [teipublisher.com](https://teipublisher.com). The source code for above example is given below:

``` html
<pb-page endpoint="https://teipublisher.com/exist/apps/tei-publisher" emit="kant" class="embedded">
    <pb-document id="kant" path="test/kant_rvernunft_1781.TEI-P5.xml" odd="dta"></pb-document>
    <!-- Navigate to next page -->
    <pb-navigation direction="forward" keyboard="right" emit="kant" subscribe="kant">
        <paper-fab icon="icons:chevron-right"></paper-fab>
    </pb-navigation>
    <!-- Navigate to previous page -->
    <pb-navigation direction="backward" keyboard="left" emit="kant" subscribe="kant">
        <paper-fab icon="icons:chevron-left"></paper-fab>
    </pb-navigation>
    <pb-view src="kant" xpath="//teiHeader/fileDesc/titleStmt/title" emit="kant" subscribe="kant">
        <pb-param name="header" value="short" />
    </pb-view>
    <pb-view class="transcription" src="kant" view="page" emit="kant" subscribe="kant"
        append-footnotes animation></pb-view>
</pb-page>
```

## Using Shortcodes

While you can use TEI Publisher custom elements directly in markdown, hugo provides a way to extend markdown with custom commands, so called *shortcodes*. Creating a shortcode is fairly straightforward: just create an `.html` file in `layouts/shortcodes` which contains the template snippet you want to insert. You can then call this shortcode in  markdown with:

```
{{</* tei-embed id="shakespeare" document="test/F-rom.xml" odd="shakespeare" view="page" */>}}
```

{{< tei-embed id="shakespeare" document="test/F-rom.xml" odd="shakespeare" view="page" >}}