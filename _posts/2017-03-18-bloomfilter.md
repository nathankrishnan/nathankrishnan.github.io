---
layout: post
title:  "Using a Bloom filter to prevent your users from accessing malicious sites"
categories: algorithms
image: https://static.pexels.com/photos/29781/pexels-photo-29781.jpg
---


In this series of blog posts I will showcase an algorithm or data structure from the [Swift Algorithm Club](https://github.com/raywenderlich/swift-algorithm-club) and highlight how it could be applied.  Without further ado, I'd like to introduce you to the concept of a [Bloom filter](https://en.wikipedia.org/wiki/Bloom_filter).

Imagine that in your iOS app you have a UIWebView that loads an arbitrary URL.  There are plenty of malicious sites on the web and we should certainly take actions to prevent, or warn, our users when they attempt to access a nefarious site.  This begs the question: what's an efficient way of checking if the domain in the URL is known to be a malicious site? 
