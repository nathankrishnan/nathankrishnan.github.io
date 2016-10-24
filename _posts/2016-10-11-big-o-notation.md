---
layout: post
title:  "Big O Notation"
categories: markdown
image: https://upload.wikimedia.org/wikipedia/commons/thumb/7/79/Writing_on_the_whiteboard.jpg/1280px-Writing_on_the_whiteboard.jpg
image2: https://static.pexels.com/photos/7067/notes-clean-hero-minimal.jpg
---

To describe how long an algorithm takes to run we use something called Big O Notation. Since hardware and software runtime environments  can vary widely, Big O Notation provides with a uniform way of comparing efficiencies.  For example, the code in your iOS app will definitely run faster on an iPhone 7 Plus than an iPhone 5.  This does not provide a helpful benchmark because it's a given that performance will be better on a newer phone with better specs. In order to judge the performance of your algorithms we need to take a look how long they run:


- Relative to size of the input
- As the input becomes arbitrarily large

Every time you use this notation you literally write a capital O, parenthesis, and some algebraic expression in the parenthesis.  The algebraic expression is always going to be mathematical function of the variable *n*.  Where, *n* represents the length of an input into your function.

Let's take a look at some examples to make this concept seem less foreign.

# Example 1

```
var inventory = ["iPhone 7", "Macbook Pro", "Mac Pro"]

func displayFirstInventoryItem(items: [String]) {
    print(items[0])
}
```
This function runs in constant time relative to its input because we are always accessing the first item of the array.  The array could have 3 items or 300,000 items and it wouldn't change the time necessary to return the first item.  In Big O Notation we say this function runs in **O(1)** time.

# Example 2

```
var inventory = ["iPhone 7", "Macbook Pro", "Mac Pro"]

func displayAllInventory(items: [String]){
    for inventoryItem in items {
        print(inventoryItem)
    }
}
```
This function runs in linear time because the number of items we print out is directly proportional to the number of items we have in our inventory. In other words if we had 100 items, they would get printed out 100 times. In Big O Notation we say this function runs in **O(n)** time.

# Example 3

```
var inventory = ["iPhone 7", "Macbook Pro", "Mac Pro"]

func displayAllPairsOfInventory(items: [String]) {
    var counter = 0
    for inventoryItemOne in items {
        for inventoryItemTwo in items {
            counter += 1
            print("Pair \(counter): \(inventoryItemOne), \(inventoryItemTwo)")
        }
    }
}
```
This function runs in **O(n^2)** time. The outer loop runs n times and the inner loop runs n times for each iteration of the outer loop.


# Comparing Efficiencies
Imagine you're trying to find the smallest in-app purchase you've recieved from a customer.  How would you go about doing this?  Let's take a look at two different approaches and compare them. 

## The Brute Force Approach
Perhaps the first thing to cross your mind would be to compare a each item to every item in the list.  This works, but it's not a very efficient approach. If our inventory list had 100 items then our function would have to print 10,000 times.

This solution runs in **O(n^2)** time.

```
var inAppPurchases = [11, 7, 5, 2, 9]

func findMinPurchase(list: [Int]) -> Int{
    var minNumber: Int = list[0]
    for i in inAppPurchases {
        var isMin = true
        for j in inAppPurchases {
            if i > j {
                isMin = false
            }
        }
        if isMin == true {
            minNumber = i
        }
    }
    return minNumber
}
```
## A More Thoughtful Approach

Instead of having to dive into nested loops you could select the first item in the list and iterate through the list to see if the selected item is still the smallest number.  If we come across a number that is smaller than our arbitrarily selected one, we can reclaim it as the smaller number.

This solution runs in **O(n)** time.

```
func moreEfficientFindMinPurchase(list: [Int]) -> Int {
    var minNumber = list[0]
    for i in inAppPurchases {
        if i < minNumber {
            minNumber = i
        }
    }
    return minNumber
}
```

# Common orders of magnitude

For your r

| f(n)          | Name          |
| ------------- |---------------|
| O(1)          | Constant      |
| O(log n)      | Logarithmic   |  
| O(n)          | Linear        |
| O(n log n)    | Log Linear    |
| O(n^2)        | Quadratic     |
| O(n^3)        | Cubic         |
| O(2^n)        | Exponential   |
