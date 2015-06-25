---
layout: post
title: "Browserify and Web Workers"
description: ""
category:
tags: [browserify, web-workers]
---
{% include JB/setup %}

Browserify is awesome tool that lets you run your `nodejs` app in browser. It captures whole dependency tree of your app and bundles everything into a single js file which can be included directly by browser. Basically, that bundle is `IIFE` with module map - you can learn details [here](http://benclinkinbeard.com/posts/how-browserify-works/).

Recently I wanted to add `Web Workers` support to one of my projects that already uses `Browserify`. Current `Web Worker` standard requires passing URL (or string using `URL.createObjectUrl`) to `Web Worker` definition which is a little bit pain in the ass if we want to use 'Browserify'.

My first (not the cleanest) idea was to export globally whole module (--standalone option) and access it from Web Worker but that didn't worked - I guess that it was because of limitations of `Web Workers` and their API.

Finally, I came upon [webworkify](https://github.com/substack/webworkify) which wraps importing (or requiring ;) ) of `Web Worker` and uses `URL.createObjectUrl` to load it from string. With that approach everything worked smoothly.
