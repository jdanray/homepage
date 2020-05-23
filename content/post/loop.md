---
title: "Write loops like Dijkstra"
date: 2020-05-20T23:40:27-05:00
draft: false
---

You do the best you can. You try your hardest to get a program right, and it looks like you've done it. But then you run your program on some test cases, and you realize that your program is buggy. So, you modify it, hoping that soon it will be error-free. But then your modified program still fails on some test cases, and you may even find that your efforts have introduced some new bugs. Finally, however, the program passes all of your test cases -- but you can't help but doubt whether it will always run correctly. Could you have covered every possible case?

The famous computer scientist Edsger Dijkstra pioneered an approach to programming that can decrease the presence of bugs and increase your confidence in your programs. David Gries systematized Dijkstra's approach, and I will provide you with an informal explanation of his system. I will restrict the focus to loops. The entire system is too big to cover in one blog post. I choose loops because they are very important.

This approach isn't the end-all, be-all. Programming is difficult. It requires many different techniques. However, the following method can help you in many situations. The time spent running test cases, looking through output, and searching for errors could be spent in other ways.

Let's begin by examining a simple while-loop:

```
	int N = nums.length;
	int i = 0;
	int s = 0;
	while (i != N)
	{
		s += nums[i];
		i++;
	}
```

The purpose of this loop is to sum the elements of a given integer array, nums. The loop has six parts: 

**(1) Precondition**

A precondition describes the initial state of the program. In this case, the precondition is that nums is an array of N integers (where N >= 0). 

**(2) Postcondition**

A postcondition describes a state we want the program to be in. In this case, the postcondition is that s == the sum of the elements in nums[0..N - 1].  

**(3) Loop invariant**

We want a guarantee that the loop does what we want it to do. A loop invariant can help ensure that if the loop begins in a state that satisfies the precondition, it will terminate in a state that satisfies the postcondition.

A loop invariant is a claim that is true at the beginning and end of each iteration of the loop. In this case, the loop invariant is 

&emsp;&emsp;&emsp;&emsp; s == the sum of the elements in nums[0..i - 1].

You don't need to worry about how we found this. I will explain how to arrive at loop invariants in more detail soon. 

**(4) Initialization**

Initialization establishes the loop invariant. In this case, the initialization is:

```
	int i = 0;
	int s = 0; 
```

After this initialization, the loop invariant is true. When i == 0, we have nums[0..-1], which is an empty array. We define the sum of an empty array to equal 0, so the loop invariant holds.

**(5) Termination condition**

The termination condition is simply the condition in which the loop terminates. In this case, the termination condition is i == N. While i != N, the loop executes. When i == N, the loop terminates.

The loop invariant and the termination condition are all that is necessary to ensure that, if the loop terminates, it terminates in a state that satisfies the postcondition. For instance, when both our loop invariant and termination condition are true, we have

&emsp;&emsp;&emsp;&emsp; (s == the sum of the elements in nums[0..i - 1]) and (i == N).

So, we have

&emsp;&emsp;&emsp;&emsp; s == the sum of the elements in nums[0..N - 1].

And that is our postcondition. Together, the loop invariant and the termination condition imply the postcondition. 

*The key to writing loops is to develop loop invariants and termination conditions that ensure the postcondition. Before we write any code, we want to have those two things in hand.*

**(6) Loop body**

The loop body is the sequence of statements executed during an iteration of the loop. In this case, the loop body is:

```
	s += nums[i];
	i++;
```

Because the loop invariant and the termination condition ensure the postcondition, the loop body just has two purposes:

(a) Make progress toward termination   
(b) Maintain the loop invariant

The statement ```i++;``` makes progress toward termination. Initially, i == 0, and the loop terminates when i == N. So, the statement ```i++;``` ensures that the loop gets closer to termination with each iteration.

The statement ```s += nums[i];``` maintains the loop invariant. Incrementing i effectively replaces i with i + 1. So, to maintain the loop invariant, we must add the current nums[i] to s. Now the loop invariant will remain true after the iteration. 

**The method**

When you write a loop, it is very beneficial for you to attend to these six points. I'm going to give you a step-by-step procedure to help you do just that. 

To illustrate the procedure, let's work an example. We'll go from problem statement to source code.

*Problem statement*: 

Given an unsorted array of integers, find the length of the longest increasing subarray.

Example:  
Input: [1,3,5,4,7]   
Output: 3   
Explanation: The longest increasing subarray is [1,3,5], and its length is 3. 

**(1) Define the precondition**

nums is an array of N integers, where N >= 0.

**(2) Define the postcondition**

m == the length of the longest increasing subarray in nums[0..N - 1].

**(3) Derive the loop invariant**

In general, the postcondition is a conjunction of the loop invariant and the termination condition. For instance, in our above example we found that

&emsp;&emsp;&emsp;&emsp; (s == the sum of the elements in nums[0..i - 1]) and (i == N)

was the same as the postcondition. This suggests that we can derive the loop invariant and termination condition from the postcondition. 

Let's try to use our current postcondition to derive a good loop invariant. To do that, we employ a common strategy. We replace a constant in the postcondition with a variable:  

&emsp;&emsp;&emsp;&emsp; m == the length of the longest increasing subarray in nums[0..i - 1].

This looks good, but it's not strong enough. To find the length of the longest increasing subarray in nums[0..i - 1], it is useful to also know the length of the increasing subarray that ends at nums[i - 1]. If the increasing subarray that ends at nums[i - 1] is longer than any increasing subarray that ends before nums[i - 1], then it's the longest increasing subarray in nums[0..i - 1]. So, we would like for our loop invariant to keep track of that information. Although we could use a nested loop to find the length of the increasing subarray that ends at nums[i - 1] for each 0 <= i < N, it is more efficient to strengthen our loop invariant:

&emsp;&emsp;&emsp;&emsp; (m == the length of the longest increasing subarray in nums[0..i - 1]) and    
&emsp;&emsp;&emsp;&emsp; (l == the length of the increasing subarray that ends at nums[i - 1]) 

For more strategies for developing loop invariants, see _The Science of Programming_, by David Gries. Gries discusses several rules for mechanically deriving loop invariants. 

**(4) Initialize the loop invariant**

We want to traverse the whole array. So, we begin at i == 0. Now we must assign values to m and l so that the loop invariant is initially true. The following statements will serve as a good initialization:

```
	int i = 0;
	int m = 0;
	int l = 0;
```

When i == 0, we have nums[0..-1] and nums[-1]. We define the length of the longest increasing subarray in an empty array (like nums[0..-1]) to be equal to 0. We define the length of the increasing subarray that ends at a location outside of nums (like nums[-1]) to be equal to 0. So, after these statements are executed, the loop invariant is true.

**(5) Derive the termination condition**

We can formulate the postcondition this way:
	
&emsp;&emsp;&emsp;&emsp; (m == the length of the longest increasing subarray in nums[0..i - 1]) and (i == N)

So, the termination condition is obvious. The loop should terminate when i == N.

**(6) Determine the loop body**

Our priority is to make progress toward termination. If progress falsifies the loop invariant, then we restore it. In this way we both make progress toward termination and maintain the loop invariant. 

The following statement obviously makes progess toward termination:

```
	i++;
```

However, that statement may falsify our loop invariant. Unless we update l and m at each iteration, we have no guarantee that the loop invariant will remain true after we increment i. 

*Update l:* We want the loop invariant to remain true for l when the i-th iteration ends. When the i-th iteration begins, we have that l == the length of the increasing subarray that ends at nums[i - 1]. If nums[i] > nums[i - 1], then the length of the increasing subarray that ends at nums[i] is equal to l + 1. Otherwise, the length is just 1. We maintain the first part of the loop invariant.

*Update m:* If the increasing subarray that ends at nums[i] is longer than any increasing subarray that ends before nums[i], then it's the longest increasing subarray in nums[0..i]. We have that m == the length of the longest increasing subarray in nums[0..i - 1]. So, to keep m up to date for the i-th iteration, we compare m and l and then set m to the larger of the two. We maintain the second part of the loop invariant.

Now let's put all of the pieces together. We get the following program:

```
public int lengthOfLIS(int[] nums) {
	int N = nums.length;

	int i = 0;
	int m = 0;
	int l = 0;

	while (i != N) {
		if (i > 0 && nums[i] > nums[i - 1])
			l++;
		else
			l = 1;

		m = Math.max(m, l);
		i++;
	}

	return m;
}
```

**Summary:** 

The pre- and postconditions define the problem. The program will begin in a state characterized by the precondition, and it must terminate in a state that satisfies the postcondition. The loop invariant and the termination condition should imply the postcondition, and we can derive them from the postcondition. The loop invariant must be true before and after each iteration of the loop. The initialization makes the loop invariant true before any iteration, and the loop body maintains the truth of the loop invariant and makes progress toward termination. Derive the loop invariant and the termination condition before you begin writing code.

*Our method for writing a loop:*

(1) Define the precondition    
(2) Define the postcondition  
(3) Derive the loop invariant  
(4) Determine the initialization   
(5) Derive the termination condition    
(6) Develop the loop body

**Further resources:** 

*Books:*

The Science of Programming, by David Gries  
A Discipline of Programming, by Edsger Dijkstra  
Introduction to Algorithms, by Cormen, Leisersen, Rivest, and Stein   
How to Think About Algorithms, by Jeff Edmonds   
Programming Pearls, by Jon Bentley

*Web resources:*

https://core.ac.uk/download/pdf/82596333.pdf
https://www.cs.scranton.edu//~mccloske/courses/cmps144/invariants_lec.html
https://www.win.tue.nl/~kbuchin/teaching/JBP030/notebooks/loop-invariants.html
http://www.cs.rug.nl/~wim/pub/whh348.pdf
https://courses.cs.washington.edu/courses/cse331/13sp/conceptual-info/hoare-logic.pdf
https://www.eecs.yorku.ca/~jeff/courses/3101/ass/steps1.pdf
https://ro.uow.edu.au/cgi/viewcontent.cgi?article=1059&context=compsciwp
http://liamoc.net/posts/2013-11-13-imperativereasoning.html
https://cseweb.ucsd.edu/classes/sp16/cse21-bd/howto.pdf
http://www.utsc.utoronto.ca/~nick/cscB36/additional-notes/correctness.pdf
