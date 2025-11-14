- he concatenation operator can be used to create a new list by concatenating two other lists together.
    

- b_list =  ["a", "b"] + ["c", "d"]
    

- The concatenation operator only works with other lists.
    

- In other words, you can only concatenate one list onto another.
    
- If you would like to concatenate some other kind of value, you should enclose the value in a literal list.
    
- For example, a_list = a_list + 5 will cause a TypeError because an integer cannot be concatenated onto a list.
    
- But a_list = a_list + [5] will work.
- We have already used the append() function, but there is more than one way to append values to a list.
    
- The extend operator (+=) can be used to append a list onto another list.
    

- This permanently modifies the original list!
    

- In fact, unlike the concatenation operator, the extend operator can be used with any sequence.
    

- All of the elements from the sequence are appended to the list. 
- To be clear, the concatenation operator (+) creates a new list and the original list is not modified in any way.
    
- But the extend operator (+=) does an in-place addition to the list; the original list is modified to include the elements from the other list/sequence.

- Both pop() and insert() are destructive functions. Like the append() function and the extend operator (+=) they permanently modify the list.


**alias, shallow, deep copy**
import copy

a = [1, [2, 3]]

b = a              # (alias) SAME outer object, SAME inner object
c = a.copy()   also c = list(a)  they both are equivalent    # (shallow copy) NEW outer object, SAME inner object
d = copy.deepcopy(a)  # (deep copy) NEW outer object, NEW inner object
- **`a` and `b`**:
    
    - `a is b` ➜ `True` (same outer object, i.e., same memory identity)
        
    - `a[1] is b[1]` ➜ `True` (still the same inner list)
        
- **`a` and `c`** (shallow copy):
    
    - `a is c` ➜ `False` (**new outer list**)
        
    - `a[1] is c[1]` ➜ `True` (**same inner list** shared!)
        
- **`a` and `d`** (deep copy):
    
    - `a is d` ➜ `False` (**new outer list**)
        
    - `a[1] is d[1]` ➜ `False` (**new inner list** too)



**Shallow and deep equality

Here’s the Python-centric idea:

- **Shallow equality (one-level view)**  
    Looks only at the **outer container** (means have different memory reference like both are list and have same value and same referenced inside but outer list are not same referenced **it happens when we do shallow copy** thats not a same outer container shallow equality will results in false)and the **immediate references** (

* ***Immediate references** of `a`: `a[0]`, `a[1]`, `a[2]`.
    
- **Not immediate** to `a`: `a[1][0]`, `a[2]["x"]` (they’re immediate to their _own_ containers).)it holds.  
    It does **not** look _inside_ nested objects.
    
    Concretely: same type, same length, and for each position/key the two containers refer to the **same immediate values** (often checked by identity for nested mutables).
    
- **Deep equality (recursive view)**  
    Looks through **all nested levels** and compares **only values recursively** not references  
    Inner objects don’t need to be the _same objects_—they just need to have the _same contents_
    just check **same value and type** doesnot care references
**Slicing**

	start, stop,step
if step > 0 then it goes left to right
defaults: start=None -> begin, stop=None -> end,

if step < 0 then it goes right to left
- With `step < 0`, if `start` is `None`, it defaults to the **last index**; if `stop` is `None`, it defaults to **before the first**:
ex: 
s[::-1]     # start=None -> last, stop=None -> before first
s[:2:-1]    # from end down to index >2
s[4::-1]    # from index 4 down to start


**List comrehension**:
	its syntactic way to create list. its make a shallow from a sequence
	n Python, multiple `for`-clauses inside **one** comprehension behave like nested loops, **left-to-right**:
	[ EXPR  for A in As   for B in Bs   for C in Cs   if cond(A,B,C



**sets**
	a hash function is a one-way function that, given an arbitrary input value, produces a number as output
	**You cant chage the value means inserting or removing while iterating its gonna crash and get runtime error**


**Hash**: there is a built in funciton in python named hash
hash function is an system time function that means its gonna be same for the same runtime. if the runtime ends hash changes it output for the same value
	