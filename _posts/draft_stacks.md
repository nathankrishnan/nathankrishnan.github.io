---
layout: post
title:  "Using stacks to solve problems"
categories: markdown
image: http://www.publicdomainpictures.net/pictures/80000/velka/old-books-stacked-1391968605amg.jpg
---

What comes to mind when you see the word stacks?  

Perhaps... 


![Stacks of blueberry pancakes](http://www.krusteaz.com/sites/krusteaz.com/files/styles/product_hero/public/images/products/heroes/krusteaz-blueberry-pancakes.jpg) 


Or maybe this?


![No judgement if this is your first thought](https://media.giphy.com/media/oDZNktNDuLHoY/giphy.gif)


We're not going to talk about either in this post because when I say ***stacks*** I'm referring to the abstract data structure that is an ordered collection of items. 
In this collection, new items will be added to the top and removal of items will start from the top.  This ordering principle is known as LIFO, last-in first-out.


Think of it as a stack of books or a stack of dishes in your sink.


The most useful idea of a stack is that the order items are removed is exactly the reverse order that they were placed. i.e. removal is the reverse order of insertion. This is important because we can use stacks to reverse the order of things. 


You've actually seen stacks in action before.  For example, the Back button in a web browser (URLs going on a stack) or the back button in a music player (songs are going on a stack).

For abstract data structures there are many ways to implement them. However, all stacks will have the following operations in common:

```
# creates a new stack that is empty
s = Stack()


# adds a new item to the top of the stack
push(item)


# removes the top item from the stack and returns it
pop()


# returns the first item from the stack but doesn't remove it
peek()


# returns True or False if the stack is empty
isEmpty()


# returns the number of items in the stack
size()
```

For the sake of simplicity, we can implement a stack using an Array in Swift.

```
Insert array implementation in Swift
```

# Solving problems using stacks
Stacks are super helpful for solving problems where we care about the reverse order of items.  In this section we will explore some examples that could emerge in a technical interview.


