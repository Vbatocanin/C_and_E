## 	Mathematical proof of Algorithm Correctness and Efficiency

### Introduction

When designing a completely new algorithm, a very thorough analysis of it's **correctness** and **efficiency** is needed. The last thing you would want is your solution not being adequate for a problem it was **designed** to solve in the first place. 

In this article we will be talking about the following subjects:

- [Mathematical Induction](#mathematicalinduction)
- [Proof Of Correctness](#proofofcorrectness)
- [Loop Invariants](#loopinvariants)
- [Efficiency Analysis: Recurrence Relations](#efficiencyanalysisrecurrencerelations)
- [Recurrence Relations: Linear and Non-Linear](#recurrencerelationslinearandnonlinear)
- [Solving Homogeneous Linear Recurrence Relations](#solvinghomogeneouslinearrecurrencerelations)
- [Computer Science Master Theorem](#computersciencemastertheorem)
- [Example: Dynamic Programming](#exampledynamicprogramming)
- [Example: Binary Search](#examplebinarysearch)
- [Conclusion](#conclusion)



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
2. Homogeneous refers to the fact that all element duplets `a*F(something)` are uniform, meaning a constant can not be present (`a*F(something) = const` can not happen)

These recurrence relations are solved by using the following substitution:

$$
(1) \ \  F(n) = r^n
$$

- `r` being a conveniently chosen (complex) number

>I'll be enumerating useful formulas so that I can more easily reference them in the example

We are using a complex number because we need a variable that can cycle through a number of values, of which all can (but don't have to) be different. **ALL OF WHICH** are `roots` (solutions) to the equation above. 

>Clarification: 
>
>- `complex` numbers have a form of `x = a + bi`, x being the complex number, `a` and `b` being simple integers, and `i` being the constant 
>  $$
>  \begin{align}
>  i=\sqrt{-1}
>  \end{align}
>  $$
>
>- as you can notice `i` is a very particular number, meaning it actually has a `quadric cycle`:
>  $$
>  \begin{align}
>  i=\sqrt{-1}\\
>  i^2=-1\\
>  i^3=-1*\sqrt{-1}\\
>  i^4=1\\
>  i^5=i
>  \end{align}
>  $$
>
>- this means `i` has a cycle of `length = 5`
>
>- other complex numbers can be tailor-made to have a precise cycle, where no two elements are the same (except the starting and ending elements)

Using the above mentioned substitution, we get the `characteristic polynomial`:

$$
r^k − a_1r^{k−1} − a_2r^{k−2} − ... − a_k = 0
$$
This represents a very convenient equation, where `r` can have `k` possible solutions (roots), also, we can represent `F(n)` as a linear combination of all of its predecessors (the proof of this formula's correctness will not be shown for the sake of your and my own sanity):


$$
F(n) = \sum^{k}_{i=1}c_ir^{n}_{i}
$$
- `ci` being unknown coefficients that indicate which `r` has the most impact when calculating the value of `F(n)` 

Also, if a root's value (`r` for example) does come up more than once, we say that `r` has the multiplicity (`m`) greater than . This slightly alters the above equation:
$$
(2) \ \ F(n) = \sum^{s}_{i=1}h_i(n)
$$

- `hi` being the element can contains `ri`, which is calculated (with multiplicity in mind) with the following formula:
  $$
  (3) \ \  h_i(n) = (C_{i,0} + C_{i,1}n + C_{i,2}n^2 + ... + C_{i,mi−1}n^{m_i−1})r^n_i
  $$
  

Congratulations, now we can solve the most **bare bones**  recurrence equations, let's test it out.

### Computer Science Master Theorem

Remember when I said that the above were only the **bare bones** recurrence relations? Well now we'll be looking at a more complicated, but much more useful type of recurrence relation.

The basic form of this new type of recurrence relation being:
$$
T(n) = aT(\frac{n}{b})+ cn^k
$$

- of which all constants are equal or greater that zero`a,b,c,k >= 0` and `b =/= 0`

This is a **much more common** recurrence relation because it's embodies the *divide and conquer*  principle (it calculates `T(n)` by calculating a much smaller problem like `T(n/b)`) .

The formula we use to calculate `T(n)` in the case of this kind of recurrence relation is as follows:
$$
T(n) =\begin{cases}
       O(n^{log_ba}) &\text{for } a>b^k\\
       O(n^{k}log \ n) &\text{for } a=b^k	 \\
       O(n^{k}) &\text{for } a<b^k\\
     \end{cases}
$$


Because the formula above is **logical enough**, and because the proof isn't really trivial, I would advise you to just remember it as is.... but if you still want to see the proof, read [Theorem 5.1 proof 1-2 in this article](https://www.math.dartmouth.edu/archive/m19w03/public_html/Section5-2.pdf).



### Example: Binary Search

If we have a sorted array `A` of length `n` and we want to find out how much time it would take us to find a specific element, let's call it `z` for example. First we need to take a look at the code we'll be using to find said element using Binary Search:

```python
# leftIndex and rightIndex indicate which part of the original array
# we are currently examining, the initial function call is find(A,z,1,n)
import math
def find(A, z, leftIndex, rightIndex):
    # if our search range is narrowed down to one element,
    # we just check if it's equal to z, target being the index of the wanted element
    # A[target]=z
    if leftIndex == rightIndex:
        if A[leftIndex] == z:
            return leftIndex
        else:
            return -1
    else:
        middlePoint = math.ceil((leftIndex + rightIndex) / 2)
        print("{} {} {}".format(leftIndex, rightIndex, middlePoint))
        # because the array is sorted, we know that if z < X[middlePoint],
        # we know it has to be to the left of said element, 
        # same goes if z >= X[middlePoint] and we call
        # find(A, z, leftIndex, middlePoint - 1)
        # if z == A[middlePoint]:
        #     return middlePoint
        if z < A[middlePoint]:
            return find(A, z, leftIndex, middlePoint - 1)
        else:  # z >= A[middlePoint]
            # leaving the middle point in this call is intentional
            # because there is no middle point check
            # except when leftIndex==rightIndex
            return find(A, z, middlePoint, rightIndex)


def main():
    A = [1, 3, 5, 7, 8, 9, 12, 14, 22]
    z = 12
    target = find(A, z, 0, len(A))
    print("Target index: {}".format(target))


if __name__ == "__main__":
    main()
```



The most time intensive part of this search is the recursion, this means that we can represent the time it takes the **Binary search algorithm** to search through an array of length `n` using the following recurrence relation:
$$
T(n)=T(\frac{n}{2})+1
$$
The `1` representing a constant operation like value checking (like `leftIndex == rightIndex`, this constant isn't really that important considering it's a great deal smaller than both `T(n)` and `T(n\b)`).

From matching the master theorem basic formula with the binary search formula we know:
$$
a=1,b=2,c=1,k=0\\
$$


Using the **Master Theorem formula for T(n)** we get that:
$$
T(n) = O(log \ n)
$$
So, binary search really is more efficient than standard linear search.

### Example: Dynamic Programming VS Recursion

Let's take one final look at the Fibonacci sequence (last time, I promise):
$$
Fibonacci(n)=Fibonacci(n-1)+Fibonacci(n-2)
$$
Dynamic programming, as we know from my [last article](https://stackabuse.com/dynamic-programming-in-java/) has the time complexity of `O(n)	` because it uses memoization and generates the array `linearly`, with no `look-backs` (it constructs the array from the ground up). Now lets prove that it's way more efficient to use Dynamic Programming. 

#### Fibonacci Time Complexity Analysis

Let's say that `T(n)` represents the time that is needed to calculate the` n-th` element of the Fibonacci sequence. Then we know that the following formula applies:
$$
T(n)=T(n-1)+T(n-2)
$$
First, we need to get the implicit form of the equation (**math talk** for plonk everything on one side, so that the other side only has a zero): 
$$
T(n)-T(n-1)-T(n-2)=0
$$
 Now, let's use the standard substitution (`formula (1)`): 
$$
 r^n-r^{n-1}-r^{n-2}=0 
$$
To further simplify the equation, let's divide both sides with `r` to the power of the lowest power present in the equation (in this case it's `n-2`):
$$
r^n-r^{n-1}-r^{n-2}=0 \ /r^{n-2} \\ r^{n-(n-2)}-r^{n-1-(n-2)}-r^{n-2-(n-2)}=0 \\ r^{2}-r^{1}-r^{0}=0 \\ r^{2}-r-1=0
$$
This step is done so that we can boil the problem down to a **quadric equation**.

Using the [**quadratic equation formula **](https://www.toppr.com/guides/maths/quadratic-equations/solving-quadratic-equations/) we get the following possible values for `r`: 
$$
r_1=\frac{1+\sqrt{5}}{2},r_1=\frac{1-\sqrt{5}}{2}
$$
Now, using `formula (2)`, we determine the basic formula for Fibonacci(n): 
$$
T(n)=C_1*r_1^n + C_2*r_2^n
$$
Because we know that `Fibonacci(0) = 0` and `Fibonacci(1) = 1`, therefore  `T(0) = 0` and `T(1) = 1` (technically, `T(0)` and `T(1)` could be any constant number of operations needed to calculate their values, but it doesn't really impact the result that much, so for the sake of simplicity, they're `0` and `1`, just like `Fib(0)` and `Fib(1)` ), we can use them to solve the equation above for `C1` and `C2`: 
$$
T(0)=0=C_1*r_1^0 + C_2*r_2^0=C_1+C_2 \\ \text{Which means: }C_1=-C_2
$$

Them, using `T(1)`:

$$
T(1)=1=C_1*r_1^1 + C_2*r_2^1=C_1*r_1+C_2*r_2 \\ \text{Because we know the values of r1 and r2, and the following: }C_1=-C_2 \\ \text{We can substitute them in the main equation: }\ 1=-C_2*\frac{1+\sqrt{5}}{2}+C_2*\frac{1-\sqrt{5}}{2}
$$



When we solve the above equation for `C2` we get:
$$
C_1 = -\frac{1}{\sqrt{5}}\ C_2 = \frac{1}{\sqrt{5}}
$$


Which means that now we have the final solution to the recurrence relation:
$$
T(n) =-\frac{1}{\sqrt{5}}*(\frac{1+\sqrt{5}}{2})^n+\frac{1}{\sqrt{5}}*(\frac{1-\sqrt{5}}{2})^n
$$

#### Deducing Algorithm Complexity From Recurrence Relation

Because `T(n)` represents the number of steps a program needs to calculate the `n-th` element in the sequence, `n` being also the input value, or more commonly, input size in bits. The solution above tells us that the algorithm we are using has an [**exponential complexity**](https://stackabuse.com/big-o-notation-and-algorithm-analysis-with-python-examples/).

>Fun fact: 
>
>The method above can be used to also find the formula for calculating the definite **value** for the `n-th` element in the Fibonacci sequence (the functions would represent the **value** of the` n-th` element rather than how many operations it needs to calculate them)

Because `O(a^n)` (recursive - exponential time complexity) is a much greater order of magnitude than `O(n)` (dynamic programming - linear time complexity), we now have a definitive answer **why dynamic programming is superior timewise to traditional recursion**.

### Conclusion

I know that this article might seem a little redundant. But proofs of correctness and efficiency are the cornerstones of modern Computer Science Theory, and the main reason why this field keeps going forward at a rapid rate.

Programming isn't the same thing as **Computer Science**, it's just one of its many use cases. Programming is basically using all we have learned from decades of mathematical equations and computer science into practice. Thousands of mathematicians have been grinding non stop, just so they can prove the principles we today take as irrelevant. And I think that it would be nice for people to get a better understanding of what it really is, at least solely through this article, show the math some love. Thanks for the read.