
When you run a program (`./hello`), the OS loads it into memory and organizes it into 4 segments:

High address
─────────────────────
Stack       ← grows downward ↓
    
    
    
Heap        ← grows upward ↑

Data        ← fixed size

Text        ← fixed size (READ ONLY)
─────────────────────
Low address


**1. Text Segment (Code Segment)**

This is where your **compiled machine instructions** live — the actual binary code the CPU executes.

c

````c
int main(){
    printf("hello");  // these instructions
    return 0;         // live in Text segment
}
```

Key facts:
- **READ ONLY** — cannot be modified at runtime
- Fixed size — determined at compile time
- String literals like `"hello"` also live here
- Low address
```
Text
──────────────────
machine instructions
"hello"  ← string literal
"no change" ← string literal
````

---

**2. Data Segment**

This is where **initialized global and static variables** live.

c

````c
int x = 30;          // ← lives in Data segment
char name[] = "RIT"; // ← lives in Data segment

int main(){
    // ...
}
```

Key facts:
- Allocated when program **starts**
- Freed when program **ends**
- Fixed size
- Both readable and writable
```
Data
──────────────────
x = 30
name = "RIT"
````

Note: **Uninitialized** global variables go into a subsection called **BSS segment** (sometimes considered separate from Data).

---

**3. Heap Segment**

This is where **dynamically allocated memory** lives — anything you `malloc()`.


````c
int main(){
    // this memory goes on the HEAP
    int *p = (int *) malloc(10 * sizeof(int));
    
    p[0] = 10;
    p[1] = 20;
    
    free(p);  // YOU must free it manually!
}
```

Key facts:
- **Manually managed** — you allocate with `malloc()`, free with `free()`
- Grows **upward** toward higher addresses
- If you forget to `free()` → **memory leak!**
- If you `free()` twice → **undefined behavior!**
```
Heap
──────────────────
[10][20][?][?]...  ← your malloc'd memory
````

---

**4. Stack Segment**

This is where **local variables, function arguments, and return addresses** live.

c

````c
int main(){
    int a = 15;    // ← lives on Stack
    f(2, 5);       // ← creates new stack frame
}

void f(int x, int y){
    int b = x + 1; // ← lives on Stack
    int c = y * 2; // ← lives on Stack
}
```

Key facts:
- **Automatically managed** — created when function is called, destroyed when function returns
- Grows **downward** toward lower addresses
- Each function call creates a **stack frame**
- Stack frame contains: local variables, return address, saved registers
```
Stack (grows downward ↓)
──────────────────
main's frame:
  a = 15
  return address
──────────────────
f's frame:
  x = 2, y = 5
  b = 3, c = 10
  return address
──────────────────
````

---

**Putting it all together with a real example:**

c

````c
int global = 100;        // DATA segment

int main(){
    int a = 15;          // STACK
    int *p = malloc(4);  // p is on STACK
                         // but what p points to is on HEAP
    char *s = "hello";   // s is on STACK
                         // "hello" is in TEXT segment
    free(p);
}
```
```
High address
─────────────────────────────
Stack
  a = 15          ← local variable
  p = [address]   ← pointer variable
  s = [address]   ← pointer variable

Heap
  [4 bytes]       ← what p points to (malloc'd)

Data
  global = 100    ← global variable

Text (READ ONLY)
  main()          ← machine instructions
  "hello"         ← string literal
─────────────────────────────
Low address
````

---

![[Pasted image 20260304230434.png]]


High address
─────────────────────
Stack   ← starts here, grows DOWN ↓
│
│   (free space in the middle)
│
↑
Heap    ← starts here, grows UP ↑
─────────────────────
Low address


why they move opposite?

This way they **share the free space** in the middle efficiently. Neither one needs a fixed size upfront!

High address
─────────────────────
Stack ↓
Stack ↓
Stack ↓
──────  ← DANGER ZONE!
Heap  ↑
Heap  ↑
─────────────────────
Low address
- Stack hits Heap → **Stack Overflow!** (too many function calls / recursion)
- Heap hits Stack → **Out of memory!** (too many mallocs)

The whole design is about **maximizing flexibility** — since you don't know at compile time how much heap or stack your program will need, growing them toward each other is the smartest use of available memory! 😊


each function in a program creates their own stack frame.

A stack frame containse 4 things


````C
One Stack Frame contains:
─────────────────────────
Arguments/Parameters
Return Address (EIP)
Saved EBP
Local Variables

````

![[Pasted image 20260304234457.png]]


**EBP — "HOW to get back to caller's data"**

EBP points to the **bottom of the current stack frame** so the CPU can find the caller's local variables again.

Here's where your confusion is — let me show why return address alone is NOT enough


````c
int main(){
    int a = 10;          // lives on stack at some address
    int b = 20;          // lives on stack at some address
    int c = add(3, 5);   // call add
    printf("%d\n", a);   // after add returns, need to find a again!
}
```

When `add()` is running, the stack looks like:
```
High address
──────────────────────────────
main's frame
  a = 10      ← at address 1000
  b = 20      ← at address 996
  c = ?       ← at address 992
  ▲
  EBP of main ← points HERE (base of main's frame)
──────────────────────────────
add's frame
  x = 3
  y = 5
  return address → 992 (next line in main)
  saved EBP → 1000 (main's EBP)   ← saves WHERE main's frame starts
  result = 8
  ▲
  current EBP ← points HERE now
──────────────────────────────
Low address
```

---

**What happens when add() returns:**
```
Step 1: restore EBP
  EBP = saved EBP (1000)
  → CPU can find main's variables again! (a=10, b=20)

Step 2: jump to return address (992)
  → CPU goes back to correct line in main
```

**Without saved EBP:**
```
return address → ✅ CPU knows WHERE to go back
EBP            → ✅ CPU knows HOW to find caller's variables
````


![[Pasted image 20260304234610.png]]

