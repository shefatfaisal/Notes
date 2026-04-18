25 question  

csec mid term

  

modulo 1 to 4

1 -> 5

2-> 5

3 -> 5

4 -> 10

in strongly typed language data types are enforced and type errors are caught during compile time ex: java, python

in weak as long as the data is the same size as the variable, everything ok
C/C++ is weakly typed 


• printf(“%d”, *&x); is the same as printf(“%d”, x);

![[Pasted image 20260304023724.png]]
 
 
 Arrays are created at compile time and cannot use run-time data for
sizing

If you try to use a function name before it is defined, the C compiler
won’t recognize it
• This is a very common error, often presents as Unresolved External Symbol


char * b = "hello"

here b creates in stack but hello is string literal creates in text segment in memory which read only thats why we can't do b[0] = 'H' because we are trying to write in the read only memory.

so its gonna crash at runtime

so we can use```
``` 
const char *  b = "hello"
```
here compile gonna yelled at me at compile time

https://claude.ai/share/e6ec79ed-05bc-432b-a92c-56fefd89365e


