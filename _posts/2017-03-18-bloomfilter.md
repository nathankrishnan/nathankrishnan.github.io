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

# The basic concept
A Bloom filter can be conceptually thought of as a fixed-length array of bits. 
```
[0, 0, 0, 0, 0, 0]
```


Instead of inserting the value of an item into the array, we can just set some of these bits to ```1```.  To see if an item is present, we can check to see if its corresponding place in the array has a ```0``` or ```1```. To perform both of these operations we will use hash functions.  This is an important aspect to note.  You will provide multiple hash functions (typically two or three) for your Bloom filter to use, as it will reduce the chances of false positives occurring.

Querying a Bloom filter will either return ```false```, meaning that the item is ***not*** in the set, or ```true```, meaning that the item ***might*** be in the set. In other words, if the Bloom filter returns ```false``` you're guaranteed that the item isn't in the set.  If it returns ```true```, there is a chance it could be false positive.

Let's walkthrough the steps of inserting an item and querying to check the presence of an item to solidify these concepts. For this example, we will work with a Bloom filter with a fixed-length size of 6 bits.  If we wanted to insert the string *"virus.io"* we would send it through our first hash function.  Let's say the output of this is the value: 1967507331.  We would then perform ```(value) moduluo (size of array)``` to get the index of which bit to set the value to 1. In our case ```1967507331 % 6 = 3```.  We will now set the value of the bit at index 3 to ```1```.  Our Bloom filter now looks like:
```
[0, 0, 0, 1, 0, 0]
```


Now we need to pass our string through the second hash function.  Let's say the output of this is the value: 1164682772.  If we continue following the steps, ```1164682772 % 6 = 2```.  We will now set the value of the bit at index 2 to ```1```.  Our Bloom filter now looks like:
```
[0, 0, 1, 1, 0, 0]
```


These two bit values indicate to the Bloom filter that you've inserted the string *"virus.io"*. To recap: instead of storing the actual string value we can run the string through several hash functions to give us a unique signature, which we can then map to a position in the array.

# Implementation in Swift
When implementing a Bloom filter in Swift, we can represent ```0``` or ```1``` as the boolean values of ```false``` or ```true```.  Why would we do this?  Well in Swift boolean values take less memory to represent than integer values.  There is a handy enum in Swift 3 called ```MemoryLayout<T>``` that we can use to verify the aforementioned claim.  Take a look at the output below:
```swift
print(MemoryLayout<Bool>.size)
// A Bool is represented with 1 byte

print(MemoryLayout<Int>.size)
// An Int is represented with 8 bytes
```

