## 	Mathematical proof of Algorithm Correctness and Efficiency

### Introduction

When designing a completely new algorithm, a very thorough analysis of it's **correctness** and **efficiency** is needed. The last thing you would want is your solution not being adequate for a problem it was **designed** to solve in the first place. 

In this article we will be talking about the following subjects:

- [Mathematical Induction](#mathematicalinduction)
- [Proof Of Correctness](#proofofcorrectness)
- [Loop Invariants](#loopinvariants)
- [Recurrence Relations: Linear and Non-Linear](#recurrencerelationslinearandnonlinear)
- [Solving Homogeneous Linear Recurrence Relations](#solvinghomogeneouslinearrecurrencerelations)
- [Computer Science Master Theorem](#computersciencemastertheorem)
- [Example: Dynamic Programming](#exampledynamicprogramming)
- [Example: Binary Search](#examplebinarysearch)



**DISCLAIMER**: as you can see from the section titles, this is not in any way, shape or form meant for direct application, this is pure `Computer Science Theory`, and is only meant for a deeper understanding of certain fields of practical programming.

### Mathematical Induction

`Mathematical induction`(MI) is an essential tool for proving the statement that proves an algorithm's correctness. The general idea of MI is to prove that a statement is true for every natural number `n`. What does this actually mean?

This means we have to go through 3 steps:

1. **Induction Hypothesis**: Define the rule we want to prove for every `n`, let's call the rule `F(n)`
2. **Induction base**: Proving the rule is valid for an initial value, or rather a starting point
   - this is often proven by solving the Induction Hypothesis `F(n)` for n=1 or whatever initial value is appropriate
3. **Induction step**: Proving that if we know that `F(n)` is true, we can `step` one step forward and assume `F(n+1)` is correct

If you followed these steps, **CONGRATULATIONS**, now have the power to loop! No, really, this basically gives us the ability to do the following:

```python
for(i in range(n)):
    T[i]=True
```

#### Basic Example

**Problem**:

> If we define S(n) as the sum of the first n natural numbers, for example S(3) = 3+2+1, prove that the following formula can be applied to any n:
> $$
> S(n)=\frac{(n+1)*n}{2}
> $$

Let's trace our steps:

1. **Induction Hypothesis**: S(n) defined with the formula above

2. **Induction base**: In this step we have to prove that S(1) = 1:
   $$
   S(1)=\frac{(1+1)*1}{2}=\frac{2}{2}=1
   $$

3. **Induction step**: In this step we need to prove that if the formula applies to S(n), it also applies to S(n+1) as follows:

$$
S(n+1)=\frac{(n+1+1)*(n+1)}{2}=\frac{(n+2)*(n+1)}{2}
$$

This is known as an **implication** (a=>b), which just means that we have to prove `b` is correct providing we know `a` is correct.
$$
S(n+1)=S(n)+(n+1)=\frac{(n+1)*n}{2}+(n+1)=\frac{n^2+n+2n+2}{2}
$$

$$
=\frac{n^2+3n+2}{2}=\frac{(n+2)*(n+1)}{2}
$$

> Note that S(n+1) = S(n) + (n+1) just means we are recursively calculating the sum. Example with literals: 
>
> S(3) = S(2) + 3= S(1) + 2 + 3 = 1 + 2 + 3 = 6

**Q.E.D.**

### Proof Of Correctness 

Because the method we are using to prove an algorithm's correctness is math based, or rather **function based**, the more the solution is similar to a real mathematic function, the easier the proof.

Why is this you may ask? Well, practical imperative programming has this thing called a **state**, this means a program's output is dependent on 3 things:

1. Its sequence of instructions

2. Its input values

3. its **state**, or rather, all previously initialized variables that can in any way change the output value

    

#### Program State Example

```python
def foo(x):
	x = y+1
	return x
```

If I asked you to give me the output value of this function for x=1, naturally you would say:

> Well golly gee sir, how would we know the output value if we don't know that gosh darn y value. 

You see, that's exactly the point, this (imperative) program as any other has a **state**, which is defined by a list of variables and their corresponding values. Only then is this program's output truly **deterministic**.

> `Deterministic` - a system with no random factors

This opens up a whole new story about programming paradigms that have a completely transparent state, or in other words, have **NO VARIABLES**. This might seem insane, but it does exist, and it is semi-frequently used, especially **functional programming in Haskell**.

But because we don't traditionally have functional concepts at our disposal in imperative programming, we settle for next best thing for proving correctness, **recursion**. Recursion is very easy for math interpretation because it's equivalent to recurrence relations (more on recurrence relations in the following segments).

#### Recursion Example

```python
def factorial(n):
    if(n==0):
        return 1
    else:
        return n*factorial(n-1)
```

Converted to recurrence form:
$$
Factorial(n)=n*Factorial(n-1)
$$

### Loop Invariants

This all sounds fine and dandy, but up until now, we haven't said anything about representing loops and program states as math formulas. Variables in a program's state pose a problem because all of them need to be kept in check all the time, just in case one goes haywire. 

Also, loops pose a problem because there are very few mathematical equivalents to them. This means we have to incorporate **mathematical induction** into our `Algorithm Analysis Model`, because it's the only method we know that can iteratively incriminate values in math, like in actual code. 

The simplest way of solving both problems (with mathematical induction) is **Loop invariants**.

> A loop invariant is a logic formula or just a set of rules, that is true before, during and after the loop in question (so it's unbiased to iteration). It is imperative for it to contain rules for all the variables that occur in the said loop because we need to **BIND** them all to the set of values we want them to be.

#### Loop Invariant Selection

A loop invariant can be as complicated and as simple as you want it to be. However, the point is that it should be constructed to resemble the problem at hand as closely as possible.

For example, I can always say that the following is a loop invariant:
$$
(x>y)\or(x<y)\or(x==y)
$$
But, by using a `tautology` (a logic formula that is always correct) as a loop invariant, we don't really achieve anything, the only reason it is technically classified as a loop invariant is it fits all 3 requirements:

1. The formula is correct BEFORE loop execution
2. The formula is correct DURING loop execution, including all the steps in between
3. The formula is correct AFTER loop execution

#### Example: 

Let's take a look at the following code and determine the optimal loop invariant:

```python
x = 10
y = 4
z = 0
n = 0
while(n < x):
    z = z+y
    n = n+1
```

Logically, this code just calculates the value x\*y and stores it in z, this means that `z = x*y`. Another condition we know will always be true is `n <= x` (more on the equals in the example). But do these two really only apply after the program is done computing? 

The `n` value is essentially the number of loops that were already executed, but also, it's the number of times that the `z` value has been increased by `y`. So this means that both `z = n*y` **and** `n <= x` might apply **at all times**. The only thing left to do is to check whether they really can be used as a loop invariant.

#### Loop Invariant Example - Proof by Induction

1. First, we need to prove that the loop invariant is true **before** entering the loop (which is the equivalent of proving and **induction's base**):

```python
# <=> - logical equivalency, left and right sides of the equation have the same logical value (True or False)
# <= - less or equal (not to be confused with implication, which also looks like a arrow to the left)
x = 10
y = 4
z = 0
n = 0
# RULE 1: z == n*y
# 0 == 0*4 = 0 <=> True 
# so RULE 1 applies

# RULE 2: n <= x
# 0 <= 10 <=> True
# so RULE 2 applies, therefore the invariant is valid before entering the loop 
```

2. Second, we need to check if the invariant is true after every finished loop (excluding the last one), we do this by observing the transition from `z,n` to `z',n'`, where `z'` and `n'` are the values of `z ` and `n` after the next loop has executed. Therefore,  `z' = z+y` and `n' = n+1`. We need to essentially prove that if we know that the invariant is true for `z` and `n`, it's also true for `z'` and `n'`. 
   
   $$
   z' = z+y \\
   z = n*y\\
   n' = n+1 \\
   \text{If the follwing is valid, the invariant is valid: }z'=n'*y?  \\  
   z'=(n+1)*y=n*y+y=z+y
   $$
   
3. Third, we need to check if the invariant is true after the last iteration of the loop. Because `n` is an integer and we know that `n-1<x` is true, but `n<x` is false, that means that `n=x` (this is the reason why the invariant has to include `n<=x`, not `n<x`). Therefore we know that `z = x*y`.

   **Q.E.D.**

### Efficiency Analysis: Recurrence Relations

When talking about algorithm efficiency, the first thing that comes up is recurrence relations. This just means that a function such as `f(n)` is dependent on it's preceding and succeeding values, such as `f(n-1)` and `f(n+1)`.  The simplest example of this kind of function would be the Fibonacci sequence:

$$
Fibonacci(n)=Fibonacci(n-1)+Fibonacci(n-2)
$$

You might actually recognize this concept from my article on [Dynamic Programming](<https://stackabuse.com/dynamic-programming-in-java/>). And yes, the problem is very similar, however, the solving method is very different.

When analyzing algorithm efficiency, there are basically two types of relations you will be solving:

1. Linear homogeneous recurrence relations
2. Non-linear recurrence relations - Master Theorem use case



### Solving Homogeneous Linear Recurrence Relations

When reading the title above, you may be asking yourself `"What in the name of god is this math gibberish?!?!"`. Well, first let's take a look at the general formula:


$$
F(n) = a_1F(n − 1) + a_2F(n − 2) + ... + a_kF(n − k).
$$

Now let's break up the definition into byte-size pieces (pun intended):

1. Linear refers to the fact that the function elements `F(something)` are to the first power
2. Homogeneous refers to the fact that all element duplets `a*F(something)` are uniform, meaning a constant can not be present

These recurrence relations are solved by using the following substitution:

$$
F(n) = r^n
$$

- `r` being a conveniently chosen (complex) number

Using the above mentioned substitution, we get the `characteristic polynomial`:


$$
r^k − a_1r^{k−1} − a_2r^{k−2} − ... − a_k = 0
$$
This represents a vert convenient equation, where `r` can have `k` possible solutions (roots), also, we can represent `F(n)` as a linear combination of all of its predecessors:


$$
F(n) = \sum^{k}_{i=1}c_ir^{n}_{i}
$$
- `ci` being unknown coefficients that indicate which `r` has the most impact when calculating the value of `F(n)`



### Computer Science Master Theorem

### Example: Binary Search

### Example: Dynamic Programming VS Recursion

### Conclusion