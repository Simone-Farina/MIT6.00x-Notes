## Bisection Search

- We know that the square root of _x_ lies between 1 and _x_, from mathematics

- Rather than exhaustively trying things starting at 1, suppose instead we pick a number in the middle of this range

  ![image-20200908180827188](C:\Users\sfarina\AppData\Roaming\Typora\typora-user-images\image-20200908180827188.png)

  - If we are lucky, this answer is close enough
  - If not close enough, is guess too big or too small?

- If _g**2 > x_, then know _g_ is too big; but now search

  ![image-20200908180930091](C:\Users\sfarina\AppData\Roaming\Typora\typora-user-images\image-20200908180930091.png)

- And if, for example, this new _g_ is such that _g**2 < x_, then know too small; so now search

  ![image-20200908181054283](C:\Users\sfarina\AppData\Roaming\Typora\typora-user-images\image-20200908181054283.png)

#### Square Root Python Example:

```python
x = 25
epsilon = 0.01
numGuesses = 0
low = 1.0
high = x
ans = (high + low)/2.0

while abs(ans**2 - x) >= epsilon:
	print('low = ' + str(low) + ' high = ' + str(high) + ' ans = ' + str(ans))
	numGuesses += 1
	if ans**2 < x:
		low = ans
	else:
		high = ans
	ans = (high + low)/2.0

print('numGuesses = ' + str(numGuesses))
print(str(ans) + ' is close to square root of ' + str(x))
```

#### Cube Root Python Example:

```python
x = 27
epsilon = 0.01
numGuesses = 0
low = 1.0
high = x
ans = (high + low)/2.0

while abs(ans**3 - x) >= epsilon:
	if ans**3 < x:
		low = ans
	else:
		high = ans
	ans = (high + low)/2.0
    numGuesses += 1

print('numGuesses = ' + str(numGuesses))
print(str(ans) + ' is close to cube root of ' + str(x))
```

Using **Guess and Check** method, calculating the cube root of 27 with an epsilon of 0.01 took **30000** guesses.

Using this new **Binary Search** method, calculating the cube root of 27 with an epsilon of 0.01 took **14** guesses.

```python
low = 0
high = 100
ans = ''

print('Please think of a number between 0 and 100!')
while True:
  guess = (high + low)//2
  ans = input("Is your secret number " + str(guess) + '?')
  if ans == 'c':
    break
  elif ans == 'h':
    high = guess
  elif ans == 'l':
    low = guess
  else:
    print('Sorry, I did not understand your input.')
    
print("Game over. Your secret number was: " + str(guess))
```



## Floats and Fractions

- Floats approximate real numbers, but useful to understand how
- Decimal number:
  - 302 = 3 * 10^2 + 0 * 10^1 + 2 * 10^0
- Binary number:
  - 10011 = 1 * 2^4 + 0 * 2^3 + 0 * 2^2 + 1 * 2^1 + 1 * 2^0
  - which is decimal 16 + 2 + 1= 19
- Internally computer represents numbers in binary

#### Converting decimal integer to binary

- Consider example of
  -  x = 1 * 2^4 + 0 * 2^3 + 0 * 2^2 + 1 * 2^1 + 1 * 2^0 = 10011
- If we take the remainder relative to 2 (x%2) of this number, that gives us the last binary bit
- If we than divide x by 2 (x//2), all the bits get shifted right
  - x//2 = 1 * 2^3 + 0 * 2^2 + 0 * 2^1 + 1 * 2^0 = 1001
- Keep doing successive divisions, now remainder gets next bit, and so on

#### Decimal to binary converter in Python

```python
if num < 0:
  isNeg = True
  num = abs(num)
else:
  isNeg = False

result = ''
if num == 0:
  result = '0'
  
while num > 0:
  result = str(num%2) + result
  num = num // 2
if isNeg:
  result = '-' + result
```

#### Dealing with floats

- 3/8 = 0.375 = 3 * 10^-1 + 7 * 10^-2 + 5 * 10^-3
- So if we multiply by a **power of 2** big enough to convert into a whole number, can then convert to binary, and then divide by the same power of 2
- 0.375 * (2 ** 3) = 3
- Convert 3 to binary (now 11)
- Divide by 2 ** 3 (shift right 3 positions) to get 0.011 (binary) = 0.375

#### Floating point to binary converter in Python

```python
x = float(input("Enter a decimal number between 0 and 1: "))

p = 0
while ((2**p) *x) %1 != 0:
  print('Remainder = ' + str((2**p) *x - int((2**p) *x)))
  p += 1

num = int(x* (2**p))

result = ''
if num == 0:
  result = '0'
while num > 0:
  result = str(num%2) + result
  num = num//2
  
for i in range(p - len(result)):
  result = '0' + result
  
result = result[0:-p] + '.' + result[-p:]
print("The binary representation of the decimal " + str(x) + " is " + result)
```

#### Some implications

- If there is no integer p such that x* (2**p) is a whole number, then internal representation is always an approximation
- This suggest that testing equality of floats is not exact
  - Use *abs(x-y) < some small number* rather than *x == y*
- Why does *print(0.1)* return 0.1 if not exact?
  - Because Python designers set it up this way to automatically round

## Newton-Rapson Method

- General approximation algorithm to find roots of a polynomial in one variable

$$
p(x) = a_nx^n + a_{n-1}x^{n-1} + ... + a_1x + a_0
$$

- Want to find r such that *p(r) = 0*
- For example, to find out the square root of 24, find the root of

$$
p(x) = x^2 - 24
$$

- Newton showed that if g is an approximation to the root then

$$
g - p(g)/p'(g)
$$

​	  is a better approximation; where p' is derivative of p
$$
so\ if\ polynomial\ is\ x^2 + k,\ then\ the\ derivative\ is\ 2x\\
Newton-Raphson\ says\ given\ a\ guess\ g\ for\ root,\ a\ better\ guess\ is\\
g - \frac{g^2 + k}{2g}
$$

#### Python implementation

- This new method gives us another way of generating guesses, which we can check; very efficient

```python
epsilon = 0.01
y = 24.0
guess = y/2.0
numGuesses = 0

while abs(guess**2 - y) >= epsilon:
  numGuess += 1
  guess = guess - (((guess**2) - y)/(2*guess))
  
print("numGuesses = " + str(numGuesses))
print("Square root of " + str(y) + " is about " + str(guess))
```

This method give a solution of every equation very quickly

#### Iterative algorithms

- Guess and Check methods build on reusing same code
  - Use a looping construct to generate guesses, then check and continue
- Generating guesses
  - Exhaustive enumeration
  - Bisection search
  - Newton-Raphson (for root finding)

- The first square root calculator, using **Exhaustive Enumeration** method, led us to a solution in 30000 guesses
- Using **Bisection Search**, for the same number, it took 15 guesses
- Using **Newton-Raphson** method it took only 4 guesses

## Decomposition and Abstraction

#### Example - Projector

- A projector is a black box
- Don't know how it works but know the interface: input/output
- Black box somehow converts image from input source to a wall, magnifying it
- **ABSTRACTION IDEA: ** do not need to know how projector works to use it

#### Example - Olympics Projector

- Projecting large image for Olympics decomposed into separate projectors
- Each projector takes input and produces separate output
- All projectors work together to produce larger image
- **DECOMPOSITION IDEA:** different devices work together to achieve an end goal

#### Decomposition

- Break problem into different, self-contained, pieces

#### Abstraction

- Suppress details of method to compute something from use of that computation

### Create structure with DECOMPOSITION

- In example, separate devices
- In programming, divide code into **modules**
  - Are *self-contained*
  - Used to *break up* code
  - Intended to be *reusable*
  - Keep code *organized*
  - Keep code *coherent*
- This lecture achieve decomposition with **functions**
- Later up with this course, achieve decomposition with **classes**

### Suppress details with ABSTRACTION

- In example, no need to know how to build a projector
- In programming, think of a piece of code as a **black box**
  - Cannot see details
  - Do not need to see details
  - Do not want to see details
  - Hide tedious coding details
- Achieve abstraction with **function specifications** or **docstrings**

## Calling Functions and Scope

#### Variable Scope

- **Formal parameter** gets bound to the value of the **actual parameter** when the function is called
- New **scope** is created when enter a function
- **Scope** is mapping of names to objects

```python
def f(x):
    x = x + 1
    print("in f(x): x =", x)
    return x
x = 3
z = f(x)
```

![image-20200914145322151](C:\Users\sfarina\AppData\Roaming\Typora\typora-user-images\image-20200914145322151.png)

- Python returns the value **None** if no return is given
- Represents the absence of a value

#### Functions As Arguments

- Arguments can take on any type, even functions

```python
def func_a():
    print("Inside func_a")
def func_b(y):
    print("Inside func_b")
    return y
def func_c(z):
    print("Inside func_c")
    return z()

print(func_a())
print(5 + func_b(2))
print(func_c(func_a))
```

The code shown above will print out:

```shell
Inside func_a
None
Inside func_b
7
Inside func_c
Inside func_a
None
```

## Iteration vs. Recursion

#### What Is Recursion

- A way to design solutions to problems by **divide-and-conquer** or **decrease-and-conquer**
- A programming technique where a **function calls itself**
- In programming, goal is NOT to have infinite recursion
  - Must have **1 or more** base cases
  - Must solve the same problem on **some other input** with the goal of simplifying the larger problem input

#### Iterative Algorithms

- Looping constructs (while and for loops) lead to iterative algorithms
- Can capture computation in a set of state variables that update on each iteration through loop

#### Multiplication - Iterative Solution

- "Multiply _a_ * _b_" is equivalent to "add _a_ to itself _b_ times"
- Capture **state** by 
  - An **iteration** number (i) starts at b
    - _i <- i-1_ and stop when 0
  - A current **value of computation** (result)
    - _result <- result + a_

```python
def mult_iter(a, b):
    result = 0
    while b > 0:
       	result += a
       	b -= 1
	return result        
    
```

#### Multiplication - Recursive Solution

- **Recursive step**
  - Think how to reduce problem to a **simpler/smaller** version of the same problem
- **Base case**
  - Keep reducing problem until reach a simple case that can be **solved directly**
  - When b = 1, a*b = a

```python
def mult_rec(a, b):
    if b == 1:
        return a
    else:
        return a + mult_rec(a, b-1)
```

#### Factorial - Recursive Solution

$$
n! = n*(n-1)*(n-2)*(n-3)*...*1
$$

```python
def factorial(n):
    # base case
    if n == 1:
        return 1
    # recursive step
    else:
        return n*factorial(n-1)
    
print(factorial(4))
```

#### Some Observations

- Each recursive call to a function creates its own **scope/environment**
- **Bindings of variables** in a scope is not changed by recursive call
- Flow of control passes back to **previous scope** once function call returns value

#### Iteration vs. Recursion

- Recursion may be simpler, more intuitive
- Recursion may be efficient from programmer POV
- Recursion may not be efficient from computer POV

#### Exercise - Recursive Power

```python
def recurPower(base, exp):
    '''
    base: int or float
    exp: int >= 0
    
    returns: int or float, base^exp
    '''
    if exp <= 0:
        return 1
    
    return base * recurPower(base, exp - 1)
```

## Inductive Reasoning

- How do we know that our recursive code will work?

#### Mathematical Induction

- To prove a statement indexed on integers is true for all values of n:
  - Prove it is true when n is smallest value (e.g. n = 0 or n = 1)
  - Then prove that if it is true for an arbitrary value of n, one can show that it must be true for n+1

#### Example of Induction

- Sum from 0 to n formula is = (n(n+1))/2
- 0 + 1 + 2 + 3 + ... + n = (n(n+1))/2
- Proof
  - If n = 0, then LHS is 0 and RHS is 0*1/2 = 0, so true
  - Assume true for some k, then need to show that
    - 0 + 1 + 2 + ... + k + (k + 1) = ((k+1)(k+2))/2
    - LHS is k(k + 1)/2 + (k + 1) 
    - This becomes, by algebra, ((k+1)(k+2))/2

#### Relevance to Code

```python
def mult(a, b):
  if b == 1:
    return a
  else:
    return a + mult(a, b-1)
```

- Base case, we can show that *mult* must return correct answer
- For recursive case, we can assume that *mult* correctly returns an answer for problems of size smaller than b, the by the addition step, it must also return a correct answer for problem of size b
- Thus, by induction, code returns correct answer

## Towers of Hanoi

- The story:
  - 3 tall spikes
  - Stack of 64 defferent sized discs - startt on one spike
  - Need to move stack to second spike
  - Can only move one disk at the time, and larger disc can never cover up a small discs

```python
def printMove(fr, to):
  print("Move from " + str(fr) + " to " + str(to))
  
def Towers(n, fr, to, spare):
  if n == 1:
    printMove(fr, to)
  else:
    Towers(n-1, fr, spare, to)
    Towers(1, fr, to, spare)
    Towers(n-1, spare, to, fr)
    
print(Towers(4, 'P1', 'P2', 'P3'))
```

#### Exercise - Greatest Common Divisor (Iterative)

```python
def gcdIter(a, b):
  result = 0
  smaller = a if a < b else b
  while smaller > 0:
    if a % smalller == 0 and b % smaller == 0:
      result = smaller
      return result
    smaller -= 1
  return None

print(gcdIter(9, 12))
```

#### Exercise - Greatest Common Divisor (Recursive)

- My solution (1 step efficient if a < b)

```python
def gcdRecur(a, b):
  greater = a if a > b else b
  smaller = a if a < b else b
  
  if a == 0:
    return b
  elif b == 0:
    return a
  
  return gcdRecur(smaller, greater % smaller) 
    
```

- Edx solution (more understandable and readable)

```python
def gcdRecur(a, b):
  if b == 0:
    return a
  
  return gcdRecur(b, a % b)
```

## Recursion on Non-Numerics

- How to check if a string of characters is palindrome
  - "Able was I, ere I saw Elba"

#### Solving Recursively

- First convert the string to just characters, by stripping out punctuation, and converting upper case to lower case
- Then:
  - Base case: 
    - A string of length 0 or 1 is palindrome
  - Recursive case:
    - If first character matches last character, then is a palindrome if middle section is a palindrome

#### Example

- Able wai I, ere I saw Elba -> "ablewasiereisawelba"

```python
isPalindrome("ablewasiereisawelba")
```

```python
def isPalindrome(s):
  
  def toChars(s):
    s = s.lower()
    ans = ""
    for c in s:
      if c in "abcdefghijklmnopqrstuvwxyz":
        ans = ans + c
    return ans
  
  def isPal(s):
    if len(s) <= 1:
      return True
    else:
      return s[0] == s[-1] and isPal(s[1:-1])
    
 return isPal(toChars(s))
```

#### Exercise is in

Use **bisection search** to determine if a character is in a string, so long as the string is sorted in alphabetical order

```python
def isIn(char, aStr):
	if len(aStr):
    
    idx = len(aStr)//2
    guess = aStr[idx]
    
    if len(aStr) == 1 and guess != char:
      return False
    
    if guess == char:
      return True
    else:
      if guess < char:
        newStr = aStr[idx + 1:]
      else:
        newStr = aStr[:idx]
      
      return isIn(char, newStr)
  else:
    return False
```

## Modules and Files

- Have assumed that all our code is stored in one file
- A **module** is a .py file containing a collection of Python definitions and statements

#### Example Module

- The file _circle.py_ contains

```python
pi = 3.14159
def area(radius):
  return pi*(radius**2)

def circumference(radius):
  return 2*pi*radius
```

- Then we can import and use this module

```python
import circle
pi = 3
print(pi)
print(circle.pi)
print(circle.area(3))
print(circle.circumference(3))
```

#### Other Importing

- If we don't want to refer to functions and variables by their module, and the names don't collide with other bindings, then we can use:

```python
from circle import *
print(pi)
print(area(3))
```

- This has the effect of creating bindings within the current scope for all objects defined within circle
- Statements within a module are executed only the first time a module is imported

#### Files

- Need a way tyo save our work for later use
- Every operating system has its own way of handling files; Python provides an operating-system inependent means to access files; using a **file handle**

```python
nameHandle = open('kids', 'w')
```

- Creates a file named _kids_ and returns file handle which we can name and thus reference. The _w_ indicates that the file is opened for writing into

```python
nameHandle = open('kids', 'w')
for i in range(2):
  name = input("Enter name: ")
  nameHandler.write(name + '\n')
nameHandle.close()
```

```python
nameHandle = open('kids', 'r')
for line in nameHandle:
	print(line)
nameHandle.close()
```

## Tuples and Lists

#### Tuples

- An ordered sequence of elements, can mix element types
- **Immutable**, cannot change element values
- Represented with parenthesis

```python
t = ()
t = (2, "one", 3)
t[0] # evaluates to 2
(2, "one", 3) + (5, 6) # evaluates to (2, "one", 3, 5, 6)
t[1:2] # slice tuple, evaluates to ("one",)
t[1:3] # slice tuple, evaluates to ("one", 3)
t[1] = 4 # gives an error, can't modify object
```

- Conveniently used to **swap** variable values 

```python
(x, y) = (y, x)
```

- Used to return **more than one value** from a function

```python
def quotient_and_remainder(x, y):
  q = x // y
  r = x % y
  return (q, r)

(quot, rem) = quotient_and_remainder(4, 5)
```

#### Manipulating Tuples

```python
def get_data(aTuple):
  '''
  input: a tuple of tuples -> ((int, str), (int, str), ..)
  returns: number of unique words, min and max number
  '''
  nums = ()
  words = ()
  for t in aTuple():
    nums = nums + (t[0],)
    if t[1] not in words:
      words = words + (t[1],)
  min_nums = min(nums)
  max_nums = max(nums)
  unique_words = len(words)
  return (min_nums, max_nums, unique_words)
```

#### Lists

- **Ordered sequence** of information, accessible by index
- A list is denoted by **square brackets**, [ ]
- A list contains elements:
  - Usually homogeneous
  - Can contain mixed types
- List elements can be changed, so a list is **mutable**

- An element of a list is at a position (aka **index**) in list, indices start at 0
- Index can be a variable expression, must evaluate to an *int*

#### Changing Elements

- Assigning to an element at an index changes the value

```python
L = [1, 2, 3]
L[1] = 4

# L is now [1, 4, 3]
```

#### List Operations - Add

- **Add** element to end of a list with *L.append( element )*
- **Mutates** the list:

```python
L = [1, 2, 3]
L.append(5)
# L is now [1, 2, 3, 5]
```

- To combine lists togheter use **concatenation**, operator **+**
- **Mutate** list with *L.extend( some_list )*

```python
L1 = [1, 2, 3]
L2 = [4, 5, 6]
L3 = L1 + L2
# L3 is now [1, 2, 3, 4, 5, 6]
L1.extend([0, 6])
# L1 is now [1, 2, 3, 0, 6]
```

#### List Operations - Remove

- Delete elements at a specific index with *del ( L [ index ] )*
- Remove element at **end of list** with L.pop( ), returns the removed element
- Remove a **specific element** with *L.remove( element )*
  - Looks the element and removes it
  - If element occurs multiple times, removes the first occurrence
  - If element not in list, gives an error

#### Convert List to Strings

- Convert **string to list** with _list( s )_, returns a list with every character from s an element in L
- Can use _s.split( )_, to **split a string on a character** parameter, splits on spaces if called without a parameter
- Use _' '.join( L )_ to turn a **list of characters into a string**, can give a character in quotes to add char between every element

```python
s = "I <3 CS"
list(s)					# returns ['I', ' ', '<', '3', ' ', 'C', 'S']
s.split('<')		# returns ['I', '3 CS']
L = ['a', 'b', 'c']
''.join(L)			# returns 'abc'
'_'.join(L)			# returns 'a_b_c'
```

#### Other List Operations

- _sort( )_ and _sorted( )_
- _reverse( )_

```python
L = [9, 6, 0, 3]
sorted(L)			# returns sorted list, does not mutate L
L.sort()			# mutates L = [0, 3, 6, 9]
L.reverse()		# mutates L = [9, 6, 3, 0]
```

## Mutation, Aliasing, Cloning

#### Lists in Memory

- Lists are **mutable**
- Behave differently than immutable types
- Is an object in memory
- Variable names points to an object
- Any variable pointing to that object is affected
- Key phrase to keep in mind when working with lists is **side effects**

#### An Analogy

- Attributes of a person
  - Singer, rich
- He is known by many names
- All nicknames point to the **same person**
  - Add a new attribute to **one nickname**
    - Justin Bieber: singer, rich, **troublemaker**
  - **All of his nicknames** refer to old attributes and all new ones
    - The Bieb is: Singer, rich, **troublemaker**
    - JBeebs is:   Singer, rich, **troublemaker**

#### Aliases

- _hot_ is an **alias** for _warm_ - changing one changes the other!
- _append( )_ has a side effect

```python
a = 1
b = a 
print(a)
print(b)

warm = ['red', 'yellow', 'orange']
hot = warm
```



<img src="/Users/simone/Desktop/Edx/MIT6.00x/Schermata 2020-09-25 alle 15.07.03.png" alt="Schermata 2020-09-25 alle 15.07.03" style="zoom: 67%;" />

```python
hot.append('pink')
print(warm)		# mutated by append() called on hot
```

#### Print Is Not ==

- If two lists print the same thing, does not mean they are the same structure

```python
cool = ['blue', 'green', 'grey']
chill = ['blue', 'green', 'grey']
```

<img src="/Users/simone/Desktop/Edx/MIT6.00x/Schermata 2020-09-25 alle 15.12.39.png" alt="Schermata 2020-09-25 alle 15.12.39" style="zoom:67%;" />

#### Cloning a List

- Create a new list and **copy everby element** using

```python
chill = cool[:]
```

- If now i mutate _chill_, _cool_ won't change because they are two different structures

#### Sorting Lists

- Calling _sort( )_ **mutates** the list, returns nothing
- Calling _sorted( )_ **does not mutate** list, must assign result to a variable

```python
warm = ['red', 'yellow', 'orange']
sortedwarm = warm.sort()
print(warm)
print(sortedwarm)
# sortedwarm will be None, warm will be mutated

cool = ['grey', 'green', 'blue']
sortedcool = sorted(cool)
print(cool)
print(sortedcool)
# sortedcool will be a new list containing sorted cool list, cool will be unchanged
```

#### Lists of Lists of Lists of...

- Can have **nested** lists
- Side effects still possible after mutation

```python
warm = ['yellow', 'orange']
hot = ['red']
brightcolors = [warm]
brightcolors.append(hot)
hot.append('pink')
```

<img src="/Users/simone/Desktop/Edx/MIT6.00x/Schermata 2020-09-25 alle 15.27.05.png" alt="Schermata 2020-09-25 alle 15.27.05" style="zoom:67%;" />

#### Mutation and Iteration

- **Avoid** mutating a list as you are iterating over it

```python
def remove_dups(L1, L2):
  for e in L1:
    if e in L2:
      L1.remove(e)
      
L1 = [1, 2, 3, 4]
L2 = [1, 2, 5, 6]
remove_dups(L1, L2)
```

- L1 is [2, 3, 4] not [3, 4], why?
  - Python uses an internal counter to keep track of index it is in the loop
  - Mutating changes the list length but Python doesn't update the counter
  - Loop never sees element 2

- Solution:

```python
def remove_dups(L1, L2):
  L1_copy = L1[:]
  for e in L1_copy:
    if e in L2:
      L1.remove(e)
```

## Functions as Objects

```python
def applyToEach(L, f):
  """assumes L is a list, f a function
     mutates L by replacing each element,
     e, of L by f(e)"""
  for i in range(len(L)):
    L[i] = f(L[i])
    
L = [1, -2, 3.4]
applyToEach(L, abs)		# L = [1, 2, 3.4]
applyToEach(L, int)   # L = [1, 2, 3]
applyToEach(L, fact)	# L = [1, 2, 6]
applyToEach(L, fib)		# L = [1, 2, 13]

```

- Useful when given a data structure, give me back a version of that data structure where i've done something to each element of the list

#### Lists of Functions

```python
def applyFuns(L, x):
  for f in L:
    print(f(x))
    
applyFuns([abs, int, fact, fib], 4)
# output: 4, 4, 24, 5
```

#### Generalization of HOPS

- Python provides a general purpse HOP (high order program), _map_
- Simple form - a unary function and a collection of suitable arguments
  - _map( abs, [1, -2, 3, -4] )_
- Produces a 'iterable', so need to walk down on it

```python
for elt in map(abs, [1, -2, 3, -4]):
  print(elt)
# output: [1, 2, 3, 4]
```

















