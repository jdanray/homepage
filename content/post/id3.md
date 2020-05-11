---
title: "The ID3 Algorithm"
date: 2020-05-11T00:44:24-05:00
draft: false
---

You are given the voting records of a set of Democrats and the voting records of a set of Republicans. Now you are given the voting record of some unidentified politician, and you want to decide whether they are a Democrat or a Republican. How do you do that?

One way to do it is to take the voting records that you're given and construct a [decision tree](https://en.wikipedia.org/wiki/Decision_tree_learning).

To take an example, let's say that all the politicians voted on whether to send aid to El Salvador. So, every voting record has the attribute "el-salvador-aid", and the attribute value can be either "y" or "n". We represent the attributes as nodes in the decision tree and the possible attribute values as edges. To classify a given politician as either a Democrat or a Republican, you start at the root of the tree and determine the value of the corresponding attribute. Did the politician vote to send aid to El Salvador or not? You take the edge that corresponds to the value of the attribute. Now you're at a new node, and you repeat the process for the next voting record attribute. In this way you walk down the tree until you reach a leaf which designates the party that the politician probably belongs to.

The [ID3 algorithm](http://cis-linux1.temple.edu/~giorgio/cis587/readings/id3-c45.html) is a procedure to generate decision trees. ID3 is essentially a [greedy search](https://en.wikipedia.org/wiki/Greedy_algorithm) through the space of decision trees. You start with an empty tree and then iteratively construct the decision tree, starting at the root. At each iteration, you add the node whose corresponding attribute would produce the most [gain in information](http://stackoverflow.com/questions/1859554/what-is-entropy-and-information-gain) if you were given its value.

I implemented the ID3 algorithm in Python. [My implementation](https://github.com/jdanray/blog-code/blob/master/ai/id3/id3.py) is up at [my GitHub](https://github.com/jdanray/blog-code/). 

Each path from the root to a leaf in a decision tree corresponds to a rule. For example, a politician who voted against freezing physician fees and in favor of increasing spending on education is, as a rule, a Democrat. An entire decision tree corresponds to a set of rules. I wrote and included in my implementation a function that builds a ruleset out of a given decision tree.

And I applied the ID3 algorithm to solve the problem that I gave above. [The solution is also up at my GitHub.](https://github.com/jdanray/blog-code/blob/master/ai/id3/voting.py)

The ID3 algorithm's decisions are probabilistic. The decision about a given politician's party affiliation won't be 100% certain. However, my testing showed that the decisions were correct in 94.81% of test cases.
