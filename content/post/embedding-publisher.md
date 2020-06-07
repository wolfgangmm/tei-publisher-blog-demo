---
title: "Embedding TEI Publisher Components"
date: 2020-06-05T20:41:18+02:00
tags: ['TEI Publisher', 'webcomponents']
draft: false
---

This example shows how to embed TEI content into a static website using markdown for the content pages. The site was created with [Hugo](https://gohugo.io/), a popular static website generator, which allows you to get a simple website, e.g. a blog, up and running within minutes.

Hugo creates static files which you can either copy into an existing webserver of your own, or host on free services like [render](https://render.com/). Obviously you still need a TEI Publisher instance somewhere which hosts the TEI content.

## Setup

1. created a new site with `hugo new site`
2. installed a standard theme ([even](https://github.com/olOwOlo/hugo-theme-even)) into `themes`
3. copied `config.toml` from `themes/even/exampleSite` and modified it slightly
4. copied `themes/even/layouts/partials/scripts.html` to `layouts/partials` and added two lines  to import the TEI Publisher component library ([view on github](https://github.com/wolfgangmm/tei-publisher-blog-demo/blob/master/layouts/partials/scripts.html)):
    {{< pb-code-highlight html >}}
    <script src="https://unpkg.com/@webcomponents/webcomponentsjs@2.4.3/webcomponents-loader.js"></script>
    <script type="module" src="https://unpkg.com/@teipublisher/pb-components@latest/dist/pb-components-bundle.js"></script>
    {{</ pb-code-highlight >}}
5. in the same way copied `themes/even/layouts/partials/header.html` to change the logo
6. created a [custom css file](https://github.com/wolfgangmm/tei-publisher-blog-demo/blob/master/static/css/components.css) in `static/css` and linked it from `config.toml`
7. generated a set of favicons and extracted them into `/static`

Given this setup, we can start embedding content provided by TEI Publisher into our markdown:

## Embed HTML directly

For sure you can directly embed HTML into markdown as demonstrated by the example below:

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

The source code for above example is given below:

{{< pb-code-highlight html >}}
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
{{</ pb-code-highlight >}}

The data is loaded from the TEI Publisher instance running on [teipublisher.com](https://teipublisher.com), but you could use any other instance here, given that you allow the host running your hugo site to access TEI Publisher. This is configured in the `modules/config.xqm` of your TEI Publisher or a stand-alone custom edition created by it. Just add a regular expression matching your hugo host to the whitelist. In my case, hugo runs on `https://teipublisher.render.com`:

{{< pb-code-highlight xquery >}}
declare variable $config:origin-whitelist := (
    "(?:https?://localhost:.*|https?://127.0.0.1:.*)",
    "https?://unpkg.com",
    "https?://cdpn.io",
    "https?://teipublisher.onrender.com"
{{</ pb-code-highlight >}}

## Using Shortcodes

While you can use TEI Publisher custom elements directly in markdown, hugo provides a way to extend markdown with custom commands, so called *shortcodes*. Creating a shortcode is fairly straightforward: just create an `.html` file in `layouts/shortcodes` which contains the template snippet you want to insert. I created one called `tei-embed`,  which takes a number of parameters for setting the ODD, document path  and view. The parameters are passed on to the corresponding HTML attributes. Check the code on [github](https://github.com/wolfgangmm/tei-publisher-blog-demo/blob/master/layouts/shortcodes/tei-embed.html).

In general I find Hugo's template syntax a bit difficult, but that's probably because I never  tried it before.

{{< tei-embed id="shakespeare" document="test/F-rom.xml" odd="shakespeare" view="page" >}}

## Embedding Facsimiles

Beyond embedding TEI content, we can also use any other component provided by TEI Publisher, e.g. `pb-facsimile` to integrate a IIIF viewer:

<pb-facsimile base-uri="https://apps.existsolutions.com/cantaloupe/iiif/2/" facsimiles="[&quot;15929_000_IDL5772_BOss12034_IIIp79.jpg&quot;, &quot;15929_000_IDL5772_BOss12034_IIIp80.jpg&quot;, &quot;15929_000_IDL5772_BOss12034_IIIp81.jpg&quot;, &quot;15929_000_IDL5772_BOss12034_IIIp82.jpg&quot;]" default-zoom-level="0" show-navigator="show-navigator" show-navigation-control="show-navigation-control"></pb-facsimile>