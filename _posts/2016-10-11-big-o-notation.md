---
layout: post
title:  "Big O Notation"
categories: markdown
image: https://upload.wikimedia.org/wikipedia/commons/thumb/7/79/Writing_on_the_whiteboard.jpg/1280px-Writing_on_the_whiteboard.jpg
image2: /assets/article_images/night-track-mobile.JPG
---

To describe how long an algorithm takes to run we use something called Big O Notation. Since hardware and software runtime environments  can vary widely, Big O Notation provides with a uniform method of comparing the efficiency.  For example, your iOS app will definitely run faster on an iPhone 7 Plus than an iPhone 5 because the iPhone 7 plus has better specs.  This does not provide a helpful benchmark because it's a given that performance will be better on a newer phone. In order to judge the performance of your algorithms we need to take a look how long they run:
* Relative to size of the input
* As the input becomes arbitrarily large

Every time you use this notation you literally write a capital O, parenthesis, and some algebraic expression in the parenthesis.  The algebraic expression is always going to be mathematical function of the variable *n*.  Where, *n* represents the length of an input into your function.

Let's take a look at some examples to make this concept seem less foreign.

#Example 1

```bash
var inventory = ["iPhone 7", "Macbook Pro", "Mac Pro"]

func displayFirstInventoryItem(items: [String]) {
    print(items[0])
}
```
This function runs in constant time relative to its input because we are always accessing the first item of the array.  The array could have 3 items or 300,000 items and it wouldn't change the time necessary to return the first item.  In Big O Notation we say this function runs in **O(1)** time.

#Example 2

```
var inventory = ["iPhone 7", "Macbook Pro", "Mac Pro"]

func displayAllInventory(items: [String]){
    for inventoryItem in items {
        print(inventoryItem)
    }
}
```
This function runs in linear time because the number of items we print out is directly proportional to the number of items we have in our inventory. In other words if we had 100 items, they would get printed out 100 times. In Big O Notation we say this function runs in **O(n)** time.
