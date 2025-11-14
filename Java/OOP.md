
**almost everything i create will be part of a class**
* Classes can have state information
* states need an access modifier and a type
* class is a template or blueprint for some category of thing
**class atributes/Field**
* variables
* declare within the body of the class and outside of any method
* The scope of these fields is the entire body of the class.
* 
**Method**
* add behavior to a class
* since all functions in java must be a part of a class so all functions are methods
* An access modifies is required (by default **package-private**)
	**packagae-private not equal to private**
	- **private**: accessible **only inside the same top-level class** (and its nested members). Not visible to any other class—even if it’s in the same package.
	- **package-private (default)**: you write **no modifier**. Accessible to **any class in the same package**, but **not** to classes in other packages.


==static 
### The Golden Rule of `static` Methods ⭐️==

==A `static` method **cannot** access `non-static` (instance) fields or methods.

==**Why?** The `static` method belongs to the _class_, which exists independently of any _object_. If a `static` method tried to access an instance field like `name`, it would have no idea _which_ object's `name` to get.


public class Counter


    private static int total = 0;  // shared across all Counter objects

	    private int id;                // per-instance
	
	    public Counter() {
	        total++;                   // allowed (we are inside the class)
	        this.id = total;
	    }
	
	    public static int getTotal() { // static method: no 'this'
	        return total;
	    }

**Default constructor**:
- 0 for numeric types, including characters.
- false for boolean values.
* null for any reference types, like strings.
**Custom Constructors**
* Constructors in Java are named the same as the class it is a special method
* do not declare a return type - the return value is implicitly a the new object being constructed.




**Encapsulation** refers to the fact that an object acts like a container that holds its state and behavior together as one, cohesive unit.

the major reason we encapsulate because of data privacy. To achieve this we use access modifier to change the visibility of methods and fields.
- public – grants access to all parts of the program.
- protected – grants access to classes in the same package and child classes (we'll talk about this more in the next unit).
- private – grants access to objects of the same class.
- none - grants access to other classes in the same package but not child classes. Also known as "package private.



**An accessor is a method that returns the current value of a field.**

 **A mutator is a method that can be used to change the current value of a field.**


**toString():**

All classes contain a toString() method with the definition of: public String toString() {...}

if we do not replace the default implementation, it will return the name of the class + hashstring to change this behavior we use override
**Override**: Replacing an existing method with your own implementation

	When overriding, we should use the @Override annotation.


**== and equals()**:

Primitives: We only use == . it checks values 
```java
3 == 3        // true
3.0 == 3      // true (after numeric promotion)
```


Reference(objects):
- `==` compares **identity** (same object in memory?).  
    “Are these two references pointing to the exact same object?” when we create a new object using new operator then the object gains a new identity.
- `equals()` compares **meaning**/**content** (value equality) — it only checks value 
	we can customize our own equals using overridden
```java
String a = new String("hi");
String b = new String("hi");
a == b          // false (different objects)
a.equals(b)     // true  (same characters)
```


- In general you will want to use equals instead of == when working with non-primitive objects. 
- Even though == will appear to work sometimes with strings, you should generally use equals when comparing two strings. because string are referenced type if we create string like
  String a = "A";
  String b = "A" then a == b will be true

==**important**==
==for Class default implementation for equals method is same as ==== 

==The `String` class is a special, fundamental class in Java. The people who wrote Java _overrode_ the default `equals()` method inside the `String` class.==


**Scanner**: is used to read anything in java
- next() returns the next word typed by the user (up to the next whitespace).
    
- nextLine() return everything up to the point where the user pressed the enter key.
    
- nextInt(), nextLong(), nextFloat(), etc. returns the next word as the corresponding type.
**its a good practice to close the scanner that we start at the very end of the programming** (not inside a loop)


**enum**
enum is constant data type

in a complex enum we can have a filed for those values in enum data types. we then have to declare constructor to define the field 
```java
public enum MaterialType {

CERAMIC(12323),

STAINLESS_STEEL(100),

GLASS(3430),

BAMBOO(100),

PORCELAIN(100),

PLASTIC(100);

private double density;

MaterialType (double density) {

	this.density = density;

}

public double getDensity() {

return this.density;

}

}
```


enum.toString() converts enum into String which is similiar to class but enum has some other feature that class dont

enum.valueof(a_string) converts String into enum

if we concat an enum with String it typecast into String

```java
@Override

public String toString() {

return name() + " ( " + this.price + " )";

}
```
here i override toString() method so to get the enum value not field i use the name()

that means if in main **GLASS** is in the enum if we call **GLASS.toString()** then name() would be GLASS

**UML(Unified Modeling Language)**:
> - **Generalization/Inheritance**  
>     It indicates that one of the two related classes (the _subclass_) is considered to be a specialized form of the other (the _super type_) and the superclass is considered a Generalization of the subclass.  
>     The UML graphical representation of a Generalization is a hollow triangle shape on the superclass end of the line (or tree of lines) that connects it to one or more subtypes.
>     
> - **Realization/Implementation**  
>     In UML modelling, a realization relationship is a relationship between two model elements, in which one model element (the client) realizes (implements or executes) the behavior that the other model element (the supplier) specifies. The UML graphical representation of a Realization is a hollow triangle shape on the interface end of the _dashed_ line (or tree of lines) that connects it to one or more implementers.
>     

It is also illustrated with this picture:

[![enter image description here](https://i.sstatic.net/YCegU.png)](https://i.sstatic.net/YCegU.png)


So yes, your assumption in your example was right: solid line means it extends the interface, and dashed line means it implements the interface.


**Polymorphism**



**Abstract Calss**
we cant create or instantiated a new instance for abstract class



**final (variable, class, method)**
variable will be constant
class cannot be inherited means no one can extends this class
method cannot be overridden means child cannot make  their own implementation of parents class's method




![[Pasted image 20251027093106.png]]


here LinkedList is a generic type 

```java
public class LinkedList<E> implements List<E>
```
but when i do instance of i have to do regular LinkdedList because generic only occures compile time not runtime in runtime 


In Java, you cannot use the

`instanceof` operator with a generic type because of a feature called **type erasure**. This means that generic type information, such as `String` in `List<String>`, is removed by the compiler after checking for type safety, and is not available at runtime. 

At runtime, the JVM knows that a `List<String>` is simply a `List`. It has no way of telling the difference between a `List<String>` and a `List<Integer>`.



**generic type is just another type** think it that way will be easy

**if one class is implemented in another class and outer class is  generic if inner class is using generic type of outer class than we dont have to specify that because its gonna shadow (override)the outer one **


```java
public class OuterClass<T> {
    private T outerValue;

    public OuterClass(T value) {
        this.outerValue = value;
    }

    public class InnerClass {
        private T innerValue; // Directly uses T from OuterClass

        public InnerClass(T value) {
            this.innerValue = value;
        }

        public void printValues() {
            System.out.println("Outer value: " + outerValue);
            System.out.println("Inner value: " + innerValue);
        }
    }

    public static void main(String[] args) {
        OuterClass<String> outer = new OuterClass<>("Hello");
        OuterClass<String>.InnerClass inner = outer.new InnerClass("World");
        inner.printValues(); 
        // Output:
        // Outer value: Hello
        // Inner value: World
    }
}
```
In this example, `InnerClass` does not need to declare `<T>` because it automatically inherits the `T` from `OuterClass`. The `innerValue` within `InnerClass` will be of the same type as `outerValue` in the `OuterClass` instance.



**Question:** 
public interface BinarySearchTree<E> extends Comparable<E>{ ... }

  

public interface BinarySearchTree<E extends Comparable<E>>

  

what's the interpretation each of them and whats the differences

This is the most important question for understanding this assignment. The difference is subtle in text but massive in meaning.

Here is the breakdown of each.

---

### 1. `public interface BinarySearchTree<E> extends Comparable<E>`

- **Interpretation:** "I am defining a new interface called `BinarySearchTree`. The **tree itself** must be a `Comparable` object."
    
- **What it means:** This forces any class that implements your interface (like `NodeBST`) to have a method: `public int compareTo(E value)`.
    
- **The Problem (This is a logical error):** This says you must be able to compare your _entire tree_ to a _single element_ (`E`). This makes no sense. Why would you compare an entire `BinarySearchTree<Integer>` object to the number `5`?
    
    It **does not** guarantee that the elements (`E`) inside the tree are comparable to each other.
    

---

### 2. `public interface BinarySearchTree<E extends Comparable<E>>`

- **Interpretation:** "I am defining a new interface called `BinarySearchTree`. It will be generic and hold elements of type `E`. However, I am adding a **restriction** on `E`: whatever type `E` is, it **must** be a class that implements `Comparable`."
    
- **What it means:** This is called a **bounded type parameter**.
    
    - A user **can** create a `BinarySearchTree<Integer>` (because `Integer` implements `Comparable`).
        
    - A user **can** create a `BinarySearchTree<String>` (because `String` implements `Comparable`).
        
    - A user **cannot** create a `BinarySearchTree<Scanner>` (because `Scanner` does not implement `Comparable`).
        
- **The Solution (This is correct):** This is exactly what you want. It guarantees that any element `E` you put into the tree _will have_ a `.compareTo()` method. This allows you to safely write the comparison logic inside your `insert` and `search` methods.
    
### Summary of Differences

|Feature|`...<E> extends Comparable<E>` (Your File)|`<E extends Comparable<E>>` (Correct)|
|---|---|---|
|**Who must be `Comparable`?**|The **Tree** itself (e.g., `NodeBST`).|The **Elements** (`E`) inside the tree.|
|**What it means**|The `NodeBST` class must have a `compareTo(E other)` method.|The type `E` (e.g., `Integer`, `String`) must have a `compareTo(E other)` method.|
|**Is it correct?**|**No.** This is a logical error.|**Yes.** This is the correct way to build a BST.|
|**What it's called**|Extending an interface.|A **bounded type parameter**.|
|**Why it matters**|This **does not** let you call `value.compareTo(node.getValue())` inside your `insert` method.|This **does** let you call `value.compareTo(node.getValue())` inside your `insert` method.|



**Assignment Frog Hopper**

so there is an issue whenever i add new custome type(Hole) which is a pair of row and col i override the equals method of this hole type as if row and col both of the obj are same then both obj are same but there is a rule in java if i override equals method then i have to override the hashCode for that class too.
The problem occurs whenever i use HashMap ds and Hole as key. so hash map  converts my hole into hash code since i didnot provide any overridden hascode it by default use Object hash code which is based on memory location thats why same Hole means both row and col are same but got different hash code and giving me null pointer exception error

Key Takeaway:
For any custom class you write (like `Hole`), if you plan to use it as a key in a `HashMap` or store it in a `HashSet`, you **must** override both `equals()` and `hashCode()`.

