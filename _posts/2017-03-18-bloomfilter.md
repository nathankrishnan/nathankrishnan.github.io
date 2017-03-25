---
layout: post
title:  "Using a Bloom filter to prevent your users from accessing malicious sites"
categories: algorithms
image: https://static.pexels.com/photos/29781/pexels-photo-29781.jpg
---


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

The problem with this approach is that as your dossier grows, so will its memory footprint because you are storing each domain name as a string.  In other words, a large dossier will consume a significant amount of memory. Is there a more efficient approach to maintain our list of bad domains without consuming too much memory?  Yes, we can instead use a Bloom filter, which is a space-efficient and probabilistic data structure.  

# The basic concept
A Bloom filter can be conceptually thought of as a fixed-length array of bits. 

```
[0, 0, 0, 0, 0, 0]
```

Instead of inserting the value of an item into the array, we can just set some of these bits to ```1```.  To see if an item is present, we can check to see if its corresponding place in the array has a ```0``` or ```1```. To perform both of these operations we will use hash functions.  This is an important aspect to note.  You will provide multiple hash functions (typically two or three) for your Bloom filter to use, as it will reduce the chances of false positives occurring.

Querying a Bloom filter will either return ```false```, meaning that the item is ***not*** in the set, or ```true```, meaning that the item ***might*** be in the set. In other words, if the Bloom filter returns ```false``` you're guaranteed that the item isn't in the set.  If it returns ```true```, there is a chance it could be false positive.

# Inserting an item
Let's walkthrough the steps of inserting an item and querying to check the presence of an item to solidify these concepts. For this example, we will work with a Bloom filter with a fixed-length size of 6 bits.  If we wanted to insert the string *"virus.io"* we would send it through our first hash function.  Let's say the output of this is: 1967507331.  We would then perform ```(value) moduluo (size of array)``` to get the index of which bit to set the value to 1. In our case ```1967507331 % 6 = 3```.  We will now set the value of the bit at index 3 to ```1```.  Our Bloom filter now looks like:
```
[0, 0, 0, 1, 0, 0]
```
Now we need to pass our string through the second hash function.  Let's say the output of this is: 1164682772.  If we continue following the steps, ```1164682772 % 6 = 2```.  We will now set the value of the bit at index 2 to ```1```.  Our Bloom filter now looks like:
```
[0, 0, 1, 1, 0, 0]
```
These two bit values indicate to the Bloom filter that you've inserted the string *"virus.io"*. To recap: instead of storing the actual string value we can run the string through several hash functions to give us a unique signature, which we can then map to a position in the array.

# Checking the existence an item
To check to see if an item exists, you send the input through the hash functions, which will return array indices.  You then check the value at each array index. If all of the values are ```1``` then the check will return ```true```.  If any, or all, of the values are ```0``` then the check will return ```false```.

**NOTE**: As mentioned earlier, there is a chance that a false positive could occur.  To be more specific, this occurs when the hash values of different input items are mapped to the same indices of the array.  This can be remedied by having a large enough array size or by adding more hash functions that the Bloom filter can use.  However, adding extraneous hash functions will slow down performance.  Refer to this [answer on StackOverflow](http://stackoverflow.com/questions/658439/how-many-hash-functions-does-my-bloom-filter-need), which provides a concise explanation of an equation you can use to calculate the number of needed hash functions.  The performance of the Bloom filter will be **O(k)**, where k is the number of hash functions.

# Implementation in Swift
When implementing a Bloom filter in Swift, we can represent ```0``` or ```1``` with the boolean values of ```false``` or ```true```.  Why would we do this?  Well in Swift boolean values take less memory to represent than integer values.  There is a handy enum in Swift 3 called ```MemoryLayout<T>``` that we can use to verify the aforementioned claim.  Take a look at the output below:
```swift
print(MemoryLayout<Bool>.size)
// A Bool is represented with 1 byte

print(MemoryLayout<Int>.size)
// An Int is represented with 8 bytes
```

Jamil Dhanani and Matthijs Hollemans from the [Swift Algorithm Club](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Bloom%20Filter) have created an implementation of a Bloom filter in Swift that I will use as the basis for explanation. Here is the scaffold of the Bloom filter class:

```swift
public class BloomFilter<T> {
    private var array: [Bool]
    private var hashFunctions: [(T) -> Int]
    
    public init(size: Int = 1024, hashFunctions: [(T) -> Int]) {
        self.array = [Bool](repeating: false, count: size)
        self.hashFunctions = hashFunctions
    }
    
    private func computeHashes(_ value: T) -> [Int] {}
    
    public func insert(_ element: T) {}
    
    public func query(_ value: T) -> Bool {}
   
    public func isEmpty() {}
}
```

In the upcoming sections we will explore the inner workings of each function.  The Bloom filter class is of a generic type.  It has two stored properties:
- An array of type ```Bool``` 
- An array to store the hash functions that we'll use

The first array will be initialized to a fixed size.  If you're unfamiliar with the syntactic sugar, you can initialize an array to repeat a value for a given number of times.  For example: 

```swift 
[Int](repeating: 7, count: 3)
```  

Will create an array with three 7's.
```swift 
[7, 7, 7]
```

The second array will store the hashing functions we will use.  

## Computing Hash Values

Both the insert or query operations the need a way to calculate a hash value.  The ```computeHashes()``` function will be a helper function to do this, which is why its marked as private.  This function will iterate over each hash function in the array containing hash functions and apply the function to the input. Then it will perform modulo on this value by the array's size.  This will give us position in our array of Boolean values to change the value to ```true```.  The output of this function will be an array of positions.

```swift
private func computeHashes(_ value: T) -> [Int] {
  return hashFunctions.map() { hashFunction in abs(hashFunction(value) % array.count) }
}

```

For those unfamiliar with the closure syntax in Swift, here's another way to write out this function:
```swift
private func computeHashes(_ value: T) -> [Int] {
  return hashFunctions.map({
    (hashFunction: (T) -> Int) -> Int in
      return abs(hashFunction(value)) % array.count
  })
}
```

```map()``` will return an array with the integer values that resulted from the operations.  I plan on writing a blog post on ```map()``` and other *higher order functions* that you can perform on arrays in Swift in the future.  For now just know that ```map()``` provides us with a functional alternative to using a ```for``` loop.  For the sake of comparison, this is what the ```computeHashes()``` function would look if we used a ```for``` loop instead:

```swift
private func computeHashes(_ value: T) -> [Int] {
    var results: [Int] = []
    for hashFunction in hashFunctions {
        let value = abs(hashFunction(value)) % array.count
        results.append(value)
    }
    return results
}
```

**NOTE**: Taking the absolute value of the result of performing the hash function on a value is a preventative measure just in case the result is a negative value.



