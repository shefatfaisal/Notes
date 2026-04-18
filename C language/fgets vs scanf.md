

difference between character array and character pointer


char s[100] = "asfisajal";
- this tells  the compiler to allocate a solid block of exactly 100 bytes of memory.
- you cant change variable s to another block of memory
- calling sizeof(s) will return 100

char* s (character pointer) - this simply creates a pointer a variable that holds  a memory address. 
- it allocates enough memory for the pointer itself usually 8 bytes


