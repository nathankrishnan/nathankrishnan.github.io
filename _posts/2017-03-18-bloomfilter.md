---
layout: post
title:  "Using a Bloom filter to prevent your users from accessing malicious sites"
categories: algorithms
image: https://static.pexels.com/photos/29781/pexels-photo-29781.jpg
---


In this series of blog posts I will showcase an algorithm or data structure from the [Swift Algorithm Club](https://github.com/raywenderlich/swift-algorithm-club) and highlight how it could be applied.  Without further ado, I'd like to introduce you to the concept of a [Bloom filter](https://en.wikipedia.org/wiki/Bloom_filter) by first describing the problem it solves.

Imagine that in your iOS app you have a UIWebView that loads an arbitrary URL.  There are plenty of malicious sites on the web and we should certainly take actions to prevent or warn our users if they attempt to access a nefarious site.  This begs the question: what's an efficient way of checking if the domain in a URL is known to be a malicious site? Perhaps as a first pass at the problem, you would find a dossier of malicious domains, store the dossier in a set, and check to see if the domain in the arbitrary URL exists in the set.

```swift
var badDomains: Set<String> = ["badguys.com", "drevil.me", "virus.io"]

let websiteToLoad = NSURL(string: "http://virus.io")
if let validDomain = websiteToLoad?.host {
    if badDomains.contains(validDomain) {
        print("This website is known to be malicious!")
    } else {
        print("This website doesn't exist in our dossier. Let's proceed.")
    }
}
```

The problem with this approach is that a large dossier will consume a significant amount of memory.  Likewise, as your dossier grows, so will its memory footprint because you are storing each domain name as a string.  Is there a more efficient approach to maintain a dossier data list without consuming too much memory?  Yes we can instead use a Bloom filter, which is a space-efficient and probabilistic data structure.  

A Bloom filter can be conceptually thought of as a fixed-length array of bits. 
```
[0, 0, 0, 0, 0, 0]
```
Instead of inserting the value of an item into the array, we can just set some of these bits to ```1```.  To see if an item is present, we can check to see if its corresponding place in the array has a ```0``` or ```1```. To perform both of these operations we will use hash functions.  This is an important aspect to note.  You will provide multiple hash functions (typically two or three) for your Bloom filter to use, as it will reduce the chances of false positives occurring.

Querying a Bloom filter will either return ```false```, meaning that the item is ***not*** in the set, or ```true```, meaning that the item ***might*** be in the set. In other words, if the Bloom filter returns ```false``` you're guaranteed that the item isn't in the set.  If it returns ```true```, there is a chance it could be false positive.
