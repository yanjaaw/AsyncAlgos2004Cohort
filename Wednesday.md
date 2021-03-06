# Palindrome Linked List

## Prompt
Write a function that determines if a singly linked list is a palindrome (the same backwards as forwards). Your function should not alter the linked list itself.

As a refresher, a node in a singly linked list will be an object with a `value` key, and a `next` key that points to the next node (or null).

## Examples
```javascript
Input: 1 -> 2 -> 1         // the head node with value 1
Output: true               // the numbers 1-2-1 are a palindrome

isPalindrome({val: 1, next: {val: 2, next: {val: 1, next: null}}})    //returns true
```
```javascript
Input: 6 -> 3 -> 3 -> 6    // the head node with value 6
Output: true               // the numbers 6-3-3-6 are a palindrome

isPalindrome({val: 6, next: {val: 3, next: {val: 3, next: {val: 6, next: null}}}})    //returns true
```
```javascript
Input: 2 -> 7 -> 5         // the head node with value 2
Output: false              // the numbers 2-7-5 are NOT palindrome

isPalindrome({val: 2, next: {val: 7, next: {val: 5, next: null}}})    //returns false
```

## Interviewer Strategy Guide

- We only need to compare the node values, not the nodes themselves. The nodes themselves will not be equal.

- If the interviewee puts all the linked list values in an array, note that there are many approaches to test if those array values are a palindrome. Any solution that keeps overall runtime at O(n) is fine.

- If your interviewee comes up with a solution that is n time but requires looking at each node value more than once, give them a *bonus challenge* to write a solution that only looks at each node once (Solution 2)
  - Are there any data structures we've learned that could help solve this problem in one pass? Say... a stack?
  - Is there additional info about this linked list that would make this approach easier? Like, the midpoint? How can you find the midpoint of a linked list?

- It is not technically necessary to be able to get to Solution 2- both solutions are `n` time and `n` space. But major style points if you do.

- If we change the rules and make it okay to alter the linked list itself, it is possible to solve this problem in O(n) time and O(1) space, which is better than the solutions below. I linked that solution code at the bottom if you're interested.

## Solutions

### Solution 1: Arrays of Values (Optimized)

**Strategy:** We iterate through the list and populate an array with the node values. Then we iterate through the array to determine if it is a palindrome. The simplest way to check the array for a palindrome is probably with some version of pointers as shown below.

**Time Complexity:** O(n). We iterate through the list once to populate the array of node values. Then we iterate through the array to check that each element has a palindromic partner. Because these traversals of `n` nodes are in sequence, it is still `n` time overall.

**Space Complexity:** O(n). We create an array to hold the values of each node in the linked list.

```javascript
function isPalindrome(node) {
  const arr = [];
  let currentNode = node;

  // Push all node values into an array
  while(currentNode) {
      arr.push(currentNode.val);
      currentNode = currentNode.next;
  }

  // Check if the values in the array are a palindrome
  // There are many ways to do this, choose one that works for you
  for (let i = 0; i < Math.floor(arr.length/2); i++) {

    let element1 = arr[i];
    let element2 = arr[arr.length-1-i]    //this is the el at i spaces from the back

    if (element1 !== element2) return false;
  }

  return true;
}
```

### Solution 2: Tortoise / Hare Pointers and a Stack (Optimized and Stylish)

**Strategy:** Use tortoise and hare pointers to iterate through half of the linked list. The tortoise pointer moves 1 node at a time and adds these nodes to a stack. The hare pointer moves 2 nodes at a time, and serves as an indicator for when the tortoise pointer has iterated through half the list. Once we reach halfway, we will continue iterating the tortoise pointer- but instead of adding nodes to the stack, we now pop off of the stack if there is a match. We know the list is a palindrome if we reach the end of the linked list successfully.

**Time Complexity:** O(n). We only traverse the linked list one time. This is where the style points come in-- we only have to look at each node once. So even though this has the same time complexity as Solution 1, you will probably impress your interviewer.

**Space Complexity:** O(n). We create a stack that holds the first half of the linked list values. Although this may seem like n/2, remember that for Big O calculations you drop any coefficients. So space taken is O(n).


```javascript
function isPalindrome(node) {

  // Initialize your tortoise and hare pointers at the first node
  let tortoise = node;
  let hare = node;

  let stack = [];

  // Iterate through the first half of the list, pushing node vals to stack
  while(hare && hare.next) {
    stack.push(tortoise.val);
    tortoise = tortoise.next;
    hare = hare.next.next;
  }

  // If hare is falsey, it points to null and the list is even. No action necessary.
  // If hare is truthy, it points to the last node and the list is odd so we can
  // skip the middle tortoise node.
  if (hare) tortoise = tortoise.next;

  // Iterate through the 2nd half of the list, popping nodes off the stack
  while(tortoise) {
    if (stack[stack.length-1] === tortoise.val) {
      stack.pop();
      tortoise = tortoise.next;
    } else return false;
  }

  return true;
}
```

**Here's a visual**
```
         1  2  3  4  3  2  1        stack: [1]
tortoise ^
hare     ^

         1  2  3  4  3  2  1        stack: [1, 2]
tortoise    ^
hare           ^

         1  2  3  4  3  2  1        stack: [1, 2, 3]
tortoise       ^
hare                 ^

         1  2  3  4  3  2  1        stack: [1, 2, 3]
tortoise          ^                 we stop adding to the stack & skip current tortoise node...
hare                       ^        ... because the end of linked list is detected by the hare

         1  2  3  4  3  2  1        stack: [1, 2]
tortoise             ^              current node matches last node in stack so pop it off

         1  2  3  4  3  2  1        stack: [1]
tortoise                ^           current node matches last node in stack so pop it off

         1  2  3  4  3  2  1        stack: []
tortoise                   ^        current node matches last node in stack so pop it off
```

## Additional Resources

This problem was inspired by this LeetCode problem: https://leetcode.com/problems/palindrome-linked-list/

If you want to read about the O(n) time and O(1) space solution (written in Java, C & C#): https://www.geeksforgeeks.org/function-to-check-if-a-singly-linked-list-is-palindrome/
