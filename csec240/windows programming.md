
Static linking vs dynamic linking

**how do you suspect static linking might handle attaching that packaged library code to your program compared to dynamic linking?**

Static linking takes the actual code from the library and physically _copies_ all the modules you used right into your final executable file.

Because the code is copied directly into your program, it is entirely fixed. If the creators of that library release an update or fix a bug, your program won't see those changes unless you go back and completely recompile your code. This also means your final executable file is larger because it contains all that extra library code inside it


**If static linking physically copies the whole library into your program, how do you think dynamic linking might handle it to keep the program's file size smaller and make updates easier? (Hint: Think about how you might share a massive video with a friend without actually sending them the giant file itself.)**


nstead of attaching pieces to your `.exe`, the library actually remains a completely separate file on your computer's hard drive. In Windows, these are called **DLLs** (Dynamic Link Libraries). When you double-click your program to run it, the operating system steps in and connects your program to that separate DLL file "on the fly" in the computer's memory.

Because that DLL is just sitting there as an independent file

**what do you think happens if _three different programs_ on your computer all need to use the exact same library (like a standard function to draw a window on the screen)? Do they each need their own copy of the DLL saved on the hard drive, or can they do something more efficient?**

The operating system is smart enough to load that DLL into the computer's memory just once. Then, all the different programs that need it just get a "pointer" (a memory address) to that shared code. This saves a massive amount of hard drive space and RAM!

==Windows strongly prefers **Unicode** (where each character typically takes up 2 bytes).==
