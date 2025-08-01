Here are detailed revision notes, topics by topic, drawing on the provided sources:

### Polymorphism

*   **Definition**: In object-oriented programming, polymorphism primarily refers to the effect of **dynamic dispatch**.
*   **Mechanism**: It occurs when a **subclass S overrides a method f() defined in its superclass T**. If a variable `v` of type `T` is assigned an object of type `S`, the call `v.f()` will execute the definition of `f()` from `S` rather than `T`. The core idea is that "Every object 'knows' what it needs to do".
*   **Structural Polymorphism**: More generally, polymorphism also refers to behaviour that depends only on specific capabilities, known as structural polymorphism.
    *   **Examples**: Operations like reversing an array/list (should work for any type), searching for an element (needs an equality check), and sorting an array/list (needs value comparison) illustrate this concept.
    *   **Simulation in Java**: This can be simulated using the Java class hierarchy.
        *   For a **polymorphic `reverse`**, `Object[]` can be used.
        *   For a **polymorphic `find`**, `==` translates to `Object.equals()`.
        *   For a **polymorphic `sort`**, **interfaces are used to capture necessary capabilities**. For instance, a `Comparable` interface with an `abstract int cmp(Comparable s)` method allows `quicksort` to compare elements using `a[i].cmp(a[j])`.

### Type Consistency (Pre-Generics Context)

*   **`arraycopy` function**: A polymorphic function like `arraycopy(Object[] src, Object[] tgt)` needs to ensure that the target array is type compatible with the source array.
*   **Compile-time vs. Run-time Errors**:
    *   Ideally, type errors should be flagged at **compile time**.
    *   However, copying `Date[]` to `Employee[]` using such a generic `arraycopy` could lead to a **run-time error**.
*   **Subtype Assignment**: A source array can be a subtype of the target array (e.g., `ETicket[]` assigned to `Ticket[]` is allowed). But the **converse is illegal** (`Ticket[]` to `ETicket[]`).

### Polymorphic Data Structures (Challenges Addressed by Generics)

*   **Goal**: Data structures like arrays and lists should ideally allow arbitrary elements.
*   **Problems with `Object`**: When a `LinkedList` is designed to store `Object` values (e.g., methods returning `Object` or taking `Object` as arguments), two main issues arise:
    *   **Type information is lost**: This necessitates explicit **casts** when retrieving elements, as `head()` might return an `Object` that needs to be cast back to its original type (e.g., `t2 = (Ticket)(list.head())`).
    *   **Lack of Homogeneity**: The list can become non-homogenous, allowing objects of different, unrelated types (e.g., `Ticket` and `Date`) to be inserted into the same list, which can lead to type errors later.

### Generic Programming in Java

*   **Purpose**: Java introduced generic programming to directly address the type safety and homogeneity issues encountered with `Object`-based polymorphic data structures.
*   **Type Parameters**: **Classes and functions can have type parameters** (e.g., `class LinearList<T>`).
    *   Methods within these classes can then use the type parameter `T` (e.g., `public T head()` would return a value of the specific type `T` used when creating the list).
*   **Subclass Relationships**: Generics allow describing subclass relationships between type variables. For example, `public static <S extends T, T> void arraycopy(S[] src, T[] tgt)` means that the source array type `S` must be `T` or a subtype of `T`. This provides a type-safe `arraycopy` where the source type can extend the target type.
*   **Structural Polymorphism with Generics**: Generics introduce structural polymorphism into Java through type variables.
    *   **Polymorphic `reverse`**: Achieved by introducing a type quantifier before the return type, such as `public <T> void reverse(T[] objarr)`, meaning "For every type T...".
    *   **Polymorphic `find`**: With generics, attempting to search for a value of an incompatible type becomes a **compile-time error**, enhancing type safety.
*   **Polymorphic Data Structures (Generic `LinkedList`)**:
    *   A generic `LinkedList<T>` has `T` apply to the entire class.
    *   Internally, `Node`s store data of type `T`, and `head()` returns `T` while `insert()` takes `T` as an argument, ensuring type consistency throughout.
    *   Generic classes are **instantiated using concrete types**, such as `LinkedList<Ticket>` or `LinkedList<Date>`.
*   **Caution: Hiding Type Variables**: It's crucial to avoid accidentally hiding a type variable. If a method within a generic class (e.g., `LinkedList<T>`) declares its own type parameter with the same name (e.g., `public <T> void insert(T newdata){...}`), this inner `<T>` **masks the outer `LinkedList<T>`'s type parameter**, making the argument `T` a *new, distinct* type. This behavior differs from static generic methods like `arraycopy` where the type variable is exclusively scoped to the method.

### Java Generics and Subtyping

*   **Array Covariance**: Java's array typing is **covariant**. This means if type `S` is a subtype of type `T`, then `S[]` is considered a subtype of `T[]` (e.g., `ETicket[]` is a subtype of `Ticket[]`). While seemingly convenient, this can lead to **run-time type errors** if an object of the supertype `T` is assigned to an element of the `S[]` array (e.g., `ticketarr = new Ticket();` where `ticketarr` refers to an `ETicket[]` instance).
*   **Generic Class Non-Covariance**: In contrast, **generic classes are not covariant**. `LinkedList<String>` is **not compatible** with `LinkedList<Object>`. This prevents a `LinkedList<Object>` variable from holding a `LinkedList<String>` instance, which is a design decision to ensure type safety at compile time.
*   **Workarounds for Non-Covariance**: To write methods that can operate on generic types with unknown specific parameters, Java offers:
    *   **Generic Methods**: Define the method with its own type variable (e.g., `public static <T> void printlist(LinkedList<T> l)`). The `<T>` acts as a type quantifier, meaning "For every type T,...".
    *   **Wildcards (`?`)**: Use `?` as a wildcard type variable where the exact type is arbitrary or unknown (e.g., `public static void printlist(LinkedList<?> l)`). This avoids unnecessary type variable quantification when the specific type `T` is not needed for method signatures or local variables within the function. However, one must be cautious when assigning values to wildcard-typed variables, as the compiler might flag errors due to inability to guarantee type matching (e.g., `l.add(new Object())` on `LinkedList<?> l = new LinkedList<String>()` would be a compile-time error).
    *   **Bounded Wildcards**: Allow specifying constraints on the wildcard type.
        *   `<? extends T>`: Specifies an **upper bound**, meaning the type can be `T` or any of its subclasses (e.g., `LinkedList<? extends Shape>` can hold `Shape`, `Circle`, `Square`, `Rectangle` objects, useful for methods like `drawAll` that consume data).
        *   `<? super T>`: Specifies a **lower bound**, meaning the type can be `T` or any of its superclasses (e.g., `LinkedList<? super T>` can take elements of type `T` or any subtype, useful for methods that produce or add data to a collection, ensuring they are placed in a compatible container).

### Reflection

*   **Definition**: Reflection (or reflective programming) is the ability of a process to **examine, introspect, and modify its own structure and behaviour** at runtime.
*   **Components**:
    *   **Introspection**: A program's ability to **observe and reason about its own state** (e.g., determining an object's type when not known at compile time).
    *   **Intercession**: A program's ability to **modify its execution state** or alter its own interpretation or meaning.
*   **Introspection in Java**:
    *   **`getClass()`**: Returns an `Object`'s `Class` object, which encapsulates information about its class. Java creates a unique `Class` object for each loaded class, representing a **reification** (concrete form) of execution state.
    *   **`Class` Object Capabilities**:
        *   **Dynamic Instantiation**: New instances of a class can be created at runtime using `Class.newInstance()` or `Class.forName(String className).newInstance()`.
        *   **Metadata Extraction**: A `Class` object allows extraction of details about its **constructors, methods, and fields** using `getConstructors()`, `getMethods()`, and `getFields()` respectively. These return arrays of `Constructor`, `Method`, and `Field` objects, which in turn provide more specific details (e.g., arguments, return types, modifiers).
        *   **Dynamic Invocation/Manipulation**: Reflection allows invoking methods (`Method.invoke()`) and examining/setting field values (`Field.get()`, `Field.set()`) at runtime on an object.
*   **Security Concerns**:
    *   Methods like `getConstructors()` typically return only **publicly defined** members.
    *   To access private components, specific methods like `getDeclaredConstructors()`, `getDeclaredMethods()`, and `getDeclaredFields()` are available.
    *   However, accessing private members through reflection can be considered a **security issue** and may be restricted by external security policies.
*   **Practical Use**: Environments like BlueJ use Java's reflective capabilities to enable users to interact with compiled Java code (create objects, invoke methods, examine state) without the environment needing to maintain its own debugging information for each class.
*   **Limitations**: Java's reflection API **cannot create or modify classes at run time** (e.g., `new Class(...)` is not allowed). New classes must be compiled by the Java compiler before they can be used. This is a distinction from more dynamic languages like Python or Smalltalk, which allow redefining methods at runtime.

### Java Generics at Run Time (Type Erasure)

*   **Type Erasure**: A fundamental aspect of Java generics is **type erasure**. This means that at run time, Java **does not keep separate records for different instantiations of a generic type** (e.g., `LinkedList<String>` and `LinkedList<Date>` do not exist as distinct types at runtime).
    *   During compilation, all type variables are **promoted to `Object`** (e.g., `LinkedList<T>` becomes `LinkedList<Object>`).
    *   If a type variable has an upper bound, it is promoted to that upper bound (e.g., `LinkedList<? extends Shape>` becomes `LinkedList<Shape>`).
*   **Implications of Type Erasure**:
    *   **No `instanceof` with Generic Types**: You cannot use `instanceof` with generic type parameters (e.g., `if (s instanceof LinkedList<String>)` or `if (o instanceof T)`) because the generic type information is not available at runtime.
    *   **Overloading Restrictions**: You **cannot overload function signatures using different instantiations of generic types**. For example, `public void printlist(LinkedList<String> strList)` and `public void printlist(LinkedList<Date> dateList)` are illegal because after type erasure, both methods would have the same signature (`printlist(LinkedList objList)`), leading to a compilation error.
    *   **No Generic Array Instantiation**: You **cannot directly instantiate arrays of generic types** (e.g., `new T` is illegal). This restriction helps prevent the run-time type errors that arise from Java's array covariance. An "ugly workaround" involves casting a newly created `Object[]` array to `T[]` (e.g., `(T[]) new Object`), which generates a compiler warning but works.
    *   **Wrapper Classes for Primitives**: Primitive types (e.g., `int`, `double`) are not compatible with `Object`. Therefore, you **cannot use basic types directly as generic type variables** (e.g., `LinkedList<int>`). Instead, Java provides **wrapper classes** (e.g., `Integer` for `int`, `Double` for `double`) for each primitive type. All wrapper classes except `Boolean` and `Character` extend the `Number` class. Java's **autoboxing** feature allows for implicit conversion between primitive types and their corresponding wrapper types, simplifying their use in generic contexts.

### The Benefits of Indirection

*   **Abstract Data Types (ADTs)**: ADTs promote the separation of a public interface from its private implementation. For example, a `Queue<E>` can be conceptualized independently of whether it's implemented as a circular array or a linked list.
*   **User's Perspective**: While the public interface remains consistent, the user is not always indifferent to the choice of implementation. Factors like **efficiency** (e.g., circular array's single storage allocation) and **flexibility** (e.g., linked list's unbounded size) differ between implementations.
*   **Problem without Indirection**: If a specific implementation (e.g., `CircularArrayQueue<Date>`) is used directly throughout the code, changing to a different implementation (e.g., `LinkedListQueue<Date>`) later would necessitate **multiple updates** across declarations, function headers, and auxiliary variables.
*   **Solution: Interfaces as Indirection**:
    *   Define an **interface** (e.g., `Queue<E>`) that specifies the abstract behavior.
    *   Concrete implementations (e.g., `CircularArrayQueue<E>`, `LinkedListQueue<E>`) then **implement this interface**.
    *   Crucially, **variables are declared using the interface type** (e.g., `Queue<Date> dateq;`), and the specific implementation is chosen only at instantiation (e.g., `dateq = new CircularArrayQueue<Date>();`).
    *   The primary **benefit of indirection** here is that to switch to a different underlying implementation, **only the instantiation line needs to be updated** (e.g., `dateq = new LinkedListQueue<Date>();`), leaving the rest of the code that uses `dateq` unchanged.
*   **"Fundamental Theorem of Software Engineering"**: This concept is famously summarized by Butler Lampson: "All problems in computer science can be solved by another level of indirection".

### Collections Framework

*   **Evolution**: Java originally provided various built-in collective data types like `Vector`, `Stack`, and `Hashtable`. To improve flexibility and reduce the impact of changing implementation choices, Java introduced a structured Collections Framework.
*   **Design Principle**: The framework organizes data structures by functionality, using a **hierarchy of abstract interfaces and concrete implementations** to provide a crucial level of indirection.
*   **`Collection` Interface**:
    *   This is the root interface for many grouped data structures (arrays, lists, sets).
    *   **It explicitly does NOT include key-value structures (maps)**.
    *   Key methods include `add(E element)`, `iterator()`, `size()`, `isEmpty()`, `contains(Object obj)`, `containsAll(Collection<?> c)`, `equals(Object other)`, `addAll(Collection<? extends E> from)`, `remove(Object obj)`, `removeAll(Collection<?> c)`.
*   **`Iterator` Interface**:
    *   Obtained via `Collection.iterator()`.
    *   Methods: `next()`, `hasNext()`, `remove()`.
    *   Used to **loop through elements** of a collection.
    *   Java's **"for each" loop** (enhanced for loop) implicitly creates and uses an iterator to traverse collections, simplifying iteration syntax.
    *   **`Iterator.remove()`**: Removes the **element that was last accessed using `next()`**. It's important to note that calling `remove()` consecutively without an intervening `next()` will result in an error.
*   **`AbstractCollection` Class**:
    *   To ease the burden of implementing the extensive `Collection` interface, Java provides `AbstractCollection`, an **abstract class that implements `Collection`**.
    *   It offers **default implementations** for many of the `Collection` methods.
    *   Concrete collection classes can then extend `AbstractCollection`, only needing to implement `iterator()` (based on their internal representation) and potentially override other methods for optimized behavior.

### Concrete Collections (Sub-Interfaces and Implementations)

*   **Specialized Interfaces**: The Collections Framework further refines the `Collection` interface into more specific sub-interfaces based on characteristics like order, duplicate allowance, and access constraints.
*   **`List` Interface**:
    *   Represents an **ordered collection** (sequence).
    *   Elements can be accessed via an iterator or by **position (random access)**.
    *   Adds random access-specific methods: `add(int index, E element)`, `remove(int index)`, `get(int index)`, `set(int index, E element)`.
    *   **`ListIterator`**: Extends `Iterator` with additional capabilities for lists, such as `add(E element)` (to insert before the current index), `previous()`, and `hasPrevious()`.
    *   **`RandomAccess`**: A **tagging interface** (an empty interface used for classification) that indicates whether a `List` implementation supports efficient random access (e.g., `ArrayList` does, `LinkedList` typically does not efficiently). This allows algorithms to choose optimal strategies.
    *   **`AbstractList`**: Extends `AbstractCollection` and provides default implementations for `List` methods, optimizing for random access.
    *   **`AbstractSequentialList`**: A subclass of `AbstractList` for lists without efficient random access.
    *   **`LinkedList<E>`**: Extends `AbstractSequentialList`. It is efficient for adding and removing elements at arbitrary positions but **inefficient for random access** (e.g., `get(i)` requires traversing from the start).
    *   **`ArrayList<E>`**: Extends `AbstractList`. It is a flexible-size array that provides **efficient random access**.
*   **`Set` Interface**:
    *   Represents a **collection without duplicates**.
    *   Its signature is identical to `Collection`, but its behavior is constrained: `add()` returns `false` if the element already exists and doesn't update the set; `equals()` returns `true` if contents match, disregarding order.
    *   Set implementations are typically optimized for **efficient membership tests** (e.g., using hash functions or balanced search trees).
    *   Concrete Set implementations extend `AbstractSet` (which, in turn, extends `AbstractCollection`).
    *   **`HashSet`**: Implements a **hash table** as its underlying storage. It provides fast membership checking and is unordered; its iterator scans elements in an unspecified order.
    *   **`TreeSet`**: Uses a **tree representation** (e.g., a balanced search tree). It maintains a **sorted collection** of values, and its iterator visits elements in sorted order. Insertion is `O(log n)`.
*   **`Queue` Interface**:
    *   Represents an **ordered collection** primarily for removing elements from the front and inserting at the rear (FIFO behavior).
    *   Key methods: `add(E element)` (throws an error if the queue is full) and `remove()` (throws an error if the queue is empty).
    *   **"Gentler" versions**: `offer(E element)` (returns `false` if not possible) and `poll()` (returns `null` if not possible) provide a non-throwing alternative.
    *   **Inspection methods**: `element()` (throws exception) and `peek()` (returns `null`) allow inspecting the head without removing it.
    *   **`Deque` Interface**: Extends `Queue` to provide a **double-ended queue**, supporting operations at both ends (e.g., `addFirst()`, `addLast()`, `pollFirst()`, `pollLast()`, `getFirst()`, `getLast()`, `peekFirst()`, `peekLast()`).
    *   **`PriorityQueue` Interface**: Specifies that `remove()` returns the **highest priority item**.
    *   **Concrete Implementations**: `LinkedList` implements `Queue`, and `ArrayDeque` implements `Deque` using a circular array.

### Maps

*   **Definition**: The `Map` interface represents **key-value structures** (like dictionaries), distinguishing them from `Collection` interfaces that deal with grouped data.
*   **Type Parameters**: `Map` is parameterized by two types: `K` for keys and `V` for values.
*   **Core Methods**: `get(Object key)` (fetches value), `put(K key, V Value)` (updates/inserts value), `containsKey(Object key)`, `containsValue(Object value)`.
*   **Key Behavior**: Keys in a `Map` form a `Set`, meaning there's only one entry per key. `put(k, v)` will overwrite the old value if the key already exists and returns the previous value (or `null`).
*   **Updating a Map**:
    *   **`getOrDefault(Object key, V defaultValue)`**: Retrieves the value associated with `key`, or returns `defaultValue` if the key is not present. This is useful for initializing values (e.g., `scores.put(bat, scores.getOrDefault(bat, 0) + newscore)` for accumulating sums).
    *   **`putIfAbsent(K key, V value)`**: Inserts the key-value pair only if the key is not already present.
    *   **`merge(K key, V value, BiFunction remappingFunction)`**: If the key is not present, it initializes the value to `value`. If the key exists, it combines the current value with `value` using the provided `remappingFunction` (e.g., `Integer::sum`).
*   **Extracting Keys and Values (Views)**:
    *   `Set<K> keySet()`: Returns a `Set` view of the keys.
    *   `Collection<V> values()`: Returns a `Collection` view of the values.
    *   `Set<Map.Entry<K, V>> entrySet()`: Returns a `Set` view of key-value pairs, represented by `Map.Entry` objects.
    *   These "views" allow iteration over the map's contents (e.g., using a "for each" loop on `keySet()` or `entrySet()`).
*   **Concrete Implementations**:
    *   **`HashMap`**: Uses a **hash table** for storage. It offers fast operations but provides **no fixed order** for keys returned by `keySet()` or iterators.
    *   **`TreeMap`**: Uses a **balanced search tree**. This ensures that iterators over `keySet()` (and `entrySet()`) will process keys in **sorted order**.
    *   **`LinkedHashMap`**: Combines the benefits of a hash table with linked list functionality. It **remembers the order in which keys were inserted**, and its iterators (for `keySet()`, `values()`, `entrySet()`) will enumerate entries in insertion order. It can also be configured to use **access order**, moving a key-value pair to the end of the list upon `get()` or `put()`, useful for implementing LRU (Least Recently Used) caches or scheduling. `LinkedHashSet` provides similar functionality for sets.

### Dealing with Errors

*   **Error Types**: Code can encounter various errors: user input issues, device malfunctions, resource limitations (e.g., disk full), or programming mistakes (e.g., invalid array index, `NullPointerException`, `ArithmeticException`).
*   **Exception Handling**: Java provides a structured mechanism called **exception handling** for gracefully recovering from errors at runtime.
    *   When an error occurs, the code **throws (or raises) an exception**, which is an object encapsulating information about the error.
    *   A calling piece of code can then **catch the exception** to extract information, handle the error, and take corrective action, or it can re-throw (pass) the exception further up the call chain.
    *   The compiler assists by checking whether calling code has made provisions to handle certain declared exceptions.
*   **Java's Exception Hierarchy**: All exceptions in Java descend from the `Throwable` class. It has two main branches:
    *   **`Error`**: These represent rare, severe problems that are typically "not the programmer's fault" and are internal to the JVM (e.g., `OutOfMemoryError`, `StackOverflowError`). Corrective action is usually not possible, so the program typically notifies and terminates gracefully.
    *   **`Exception`**: These are generally recoverable problems. They are further divided into:
        *   **`RuntimeException` (Unchecked Exceptions)**: These are usually indicative of programming errors that "should have been caught by code" through proper validation (e.g., `ArrayIndexOutOfBoundsException`, `NullPointerException`, `IllegalArgumentException`). They are *unchecked* because the compiler does not force you to handle or declare them. You should generally fix the underlying coding error rather than catching these.
        *   **Checked Exceptions**: These are typically user-defined or specific to external conditions (e.g., `IOException`, `FileNotFoundException`, `EOFException`). They signify that assumptions made by the code have been violated. The **compiler enforces that these exceptions must either be handled** (e.g., with `try-catch`) **or explicitly declared** (`throws` clause) by any method that might throw them.

### Exceptions in Java (Mechanisms)

*   **`try-catch` Blocks**:
    *   Code that might generate an exception is placed inside a **`try` block**.
    *   If an exception occurs within the `try` block, the rest of the code in that block is skipped.
    *   The exception is then passed to one or more **`catch` blocks** that follow the `try` block.
    *   A `catch (ExceptionType e)` block will handle the exception if the thrown exception is of `ExceptionType` or a subtype.
    *   If no `catch` block matches, the exception is propagated up the call stack. An uncaught exception at the top level leads to a program crash.
    *   When using multiple `catch` blocks, they are tried in sequence. Therefore, catch blocks should be ordered from **more specific to less specific exception types** to ensure the appropriate handler is invoked (e.g., `FileNotFoundException` before `IOException`).
*   **Generating Exceptions**:
    *   To manually throw an exception, you create an instance of an `Exception` class and use the `throw` keyword (e.g., `throw new EOFException();` or `throw new EOFException(errormsg);` with a diagnostic message).
    *   Methods that can throw **checked exceptions must declare this in their method signature** using the `throws` keyword (e.g., `String readData(Scanner in) throws EOFException`). A method can declare multiple exception types or a more general supertype (e.g., `IOException` for any I/O related exceptions).
    *   If your code calls a method that declares a checked exception, you are **obligated to either handle it** (with `try-catch`) **or declare that your method also throws** that exception.
    *   You **do not need to advertise unchecked exceptions** (`Error`, `RuntimeException`) in the `throws` clause.
*   **Customized Exceptions**: You can define your own exception classes by extending `Exception` (for checked exceptions) or `RuntimeException` (for unchecked exceptions). These custom exceptions can include specific fields (e.g., `error_value`) to provide more detailed error information.
*   **Chaining Exceptions**: A `catch` block can process a caught exception and then throw a new, different exception. The `Throwable` class provides methods like `getCause()` and `initCause()` to **link the new exception to the original one**, preserving the history of what caused the error.
*   **`finally` Block**:
    *   The code within a `finally` block is **always executed**, regardless of whether the `try` block completed normally, or an exception was thrown and caught, or an exception was thrown and not caught.
    *   `finally` blocks are crucial for **cleaning up resources** (e.g., closing files, releasing network connections, deallocating memory) that might otherwise be left open if an exception prematurely interrupts the code's execution.

### Packages

*   **Organizational Unit**: In Java, a **package** serves as an organizational unit for classes and other types.
*   **`import` Statement**: The `import` keyword allows you to refer to classes within packages directly by their simple names, rather than their fully qualified names (e.g., `import java.math.BigDecimal` or `import java.math.*` to import all classes in that package). Note that `*` in an `import` statement is **not recursive**; it only imports classes directly within that package, not its subpackages.
*   **Creating and Naming Packages**:
    *   You can create your own package hierarchies.
    *   The **naming convention** for packages is similar to reverse Internet domain names (e.g., `in.ac.iitm.onlinedegree` for `onlinedegree.iitm.ac.in`).
    *   To include a class in a specific package, you add a `package` header at the top of the source file (e.g., `package in.ac.iitm.onlinedegree;`).
    *   By default, all classes in a directory without an explicit `package` declaration belong to the same anonymous package.
*   **Visibility Modifiers**:
    *   **`public`** and **`private`** define class-level and member-level visibility.
    *   **Default (no modifier)**: If a method or variable has no explicit visibility modifier, its visibility is **public within the package**. This means it's accessible by all other classes in the same package.
    *   **`protected`**: This modifier restricts visibility to within the class itself, classes in the same package, and all **subclasses** (even if they are in different packages).
    *   Generally, a subclass cannot expand the visibility of an overridden function from its superclass. However, a specific exception is that a `protected` method can be made `public` in a subclass.

### Assertions

*   **Purpose**: Assertions are used for **documenting and checking assumptions** within your code, particularly for conditions that are *expected* to be true and signify a fatal, unrecoverable error if they are false. They are primarily for **development and debugging**, not for handling anticipated runtime errors.
*   **Syntax**: An assertion takes the form `assert condition;` or `assert condition : message;`.
*   **Behavior on Failure**: If the `condition` in an `assert` statement evaluates to `false`, an **`AssertionError`** is thrown.
*   **Handling `AssertionError`**: `AssertionError` should **not be caught**. The intention is that such an error indicates a fundamental programming flaw, and the program should abort and print diagnostic information (like a stack trace) to aid in debugging. If you need to flag an error and take corrective action, **exceptions should be used instead**.
*   **Enabling and Disabling**: A key feature of assertions is that they can be **enabled or disabled at runtime without recompilation**.
    *   **Globally enable**: `java -enableassertions MyCode` or `java -ea MyCode`.
    *   **Selectively enable**: For a specific class (`java -ea:Myclass MyCode`) or a package (`java -ea:in.ac.iitm.onlinedegree MyCode`).
    *   **Disable**: Similarly, assertions can be disabled globally (`java -disableassertions MyCode`) or selectively (`java -da:MyClass MyCode`).
    *   There's also a separate switch for system classes (`java -enablesystemassertions MyCode` or `java -esa MyCode`).
*   **Deployment**: Assertions are intended to be turned on during **development and testing phases** but are typically **not checked at run time after deployment** to avoid performance overhead.

### Logging

*   **Purpose**: Logging provides a more **flexible and controlled** way to manage diagnostic messages generated by code, compared to simple `print` statements.
*   **Advantages over `print`**:
    *   Messages can be logged separately from standard output/error.
    *   Logs are typically arranged **hierarchically**, allowing fine-grained control over which messages are processed.
    *   You can choose the **level of logging needed**.
    *   Logs can be displayed in various formats, processed by other code (handlers), and filtered to remove uninteresting entries.
    *   Logging can be controlled by an **external configuration file**, allowing changes without recompiling code.
*   **Basic Usage**: The simplest way to log is by calling the `info()` method of the global logger (e.g., `Logger.getGlobal().info("Edit->Copy menu item selected")`). This typically outputs a formatted message including timestamp, class, method, and the message itself. You can suppress global logging using `Logger.getGlobal().setLevel(Level.OFF)`.
*   **Custom Loggers**: You can create custom loggers, often named hierarchically like package names (e.g., `Logger.getLogger("in.ac.iitm.onlinedegree")`). Properties set for a parent logger automatically apply to its children.
*   **Logging Levels**: There are **seven standard logging levels**: `SEVERE`, `WARNING`, `INFO`, `CONFIG`, `FINE`, `FINER`, `FINEST`.
    *   By default, the first three (`SEVERE`, `WARNING`, `INFO`) are logged.
    *   You can set a different level for a logger (e.g., `logger.setLevel(Level.FINE)` to include more detailed messages, or `Level.ALL` for all messages, `Level.OFF` to suppress all).

### Cloning

*   **Problem of Copying Objects**: Normal assignment in Java creates **multiple references to the same object**. This means that modifying the object via one reference will affect all other references pointing to it. To get two truly separate but identical objects, a distinct copy is needed.
*   **`Object.clone()`**: The `Object` class defines a `protected` method `clone()`.
    *   By default, `Object.clone()` performs a **bitwise copy**, also known as a **shallow copy**.
    *   It performs a shallow copy because `Object` itself does not have access to the private instance variables of a specific class to construct a fresh, deep copy.
*   **Shallow Copy Explained**: In a shallow copy, if an object contains references to other mutable objects (e.g., a `Date` object within an `Employee` object), only the **references themselves are copied**, not the nested objects. This means both the original and the cloned object will share the *same* nested mutable object. Consequently, if the nested object is modified through one of the references (e.g., calling `setbday()` on the cloned employee), the original object's nested reference will also show the change.
*   **Deep Copy**:
    *   To create a truly independent copy, a **deep copy** is required, which **recursively clones all nested mutable objects**.
    *   This is achieved by **overriding the `clone()` method** in the class. The overridden `clone()` method first calls `super.clone()` (to get the shallow copy of its own fields) and then explicitly clones any mutable instance variables that are references to other objects.
    *   `Object.clone()` returns an `Object`, so a cast is necessary (e.g., `(Employee) super.clone()`). Subclasses are permitted to change the return type of `clone()` to a more specific type (e.g., `Employee` instead of `Object`).
*   **Complications with Inheritance**: Deep copying becomes subtle with inheritance. If a subclass (e.g., `Manager` extending `Employee`) introduces new mutable instance variables, the `clone()` method inherited from the superclass (`Employee.clone()`) will **not be aware of and thus will not deep copy these new variables**, potentially leading to shallow copy issues in the subclass even if the superclass implements a deep copy.
*   **Java's Restrictions on `clone()`**: To highlight these complexities and force programmers to consider copying semantics carefully, Java imposes specific checks for using `clone()`.
    *   **`Cloneable` Interface**: A class must explicitly implement the **`Cloneable` marker interface** (an interface with no methods, purely for signaling capability) to indicate that it can be cloned. If `clone()` is called on an object whose class does not implement `Cloneable`, a `CloneNotSupportedException` is thrown.
    *   **Visibility**: The `clone()` method in `Object` is `protected`. To allow other classes to clone instances of your class, you must **override `clone()` and make it `public`** (Java allows expanding visibility from `protected` to `public`).
    *   **`CloneNotSupportedException`**: The `Object.clone()` method itself declares `throws CloneNotSupportedException`. Therefore, when you override `clone()`, you must either declare that your overridden method also throws this checked exception or handle it within your `clone()` implementation.

### Type Inference

*   **Type Declarations**: Traditionally, Java insists that all variables be **explicitly declared with their type information** in advance (e.g., `Employee e; Manager m;`). This allows the compiler to perform **static type checking** to ensure the program is well-typed (e.g., `e = m;` is allowed by subtyping).
*   **Type Inference (General Concept)**: An alternative approach where the compiler **derives type information from the context** of the variable's initialization or usage.
    *   It uses information from constants (e.g., `"Hello, world"` implies `String`).
    *   It then propagates type information based on already inferred types (e.g., if `s` is `String`, then `t = s + 5;` implies `t` is also `String`).
    *   The goal is to make typing judgments statically (at compile-time) while balancing flexibility with algorithmic tractability.
*   **Type Inference in Java**: Java allows **limited type inference**, specifically **only for local variables within functions**. It is **not applicable to instance variables** of a class.
    *   The **`var` keyword** is used to declare variables whose types will be inferred.
    *   A variable declared with `var` **must be initialized when declared**, as the type is inferred from its initial value (e.g., `var b = false;` infers `boolean`, `var s = "Hello, world";` infers `String`).
    *   For class instances, Java infers the **most specific (most constrained) type**. For example, `var e = new Manager(...);` will infer `Manager` as the type for `e`, even if `Manager` is a subclass of `Employee`. If you intend for `e` to be of the broader `Employee` type, you must declare it explicitly (`Employee e = new Manager(...);`).

### Higher Order Functions

*   **Concept**: Higher-order functions are functions that can either **take other functions as arguments** or **return functions as results**. This is a common paradigm in functional programming.
*   **Traditional Java Approach (before Lambdas)**: In object-oriented programming, passing functions was traditionally achieved by encapsulating the function within an object, usually through an **interface**.
    *   **Callbacks**: An object needing to be notified (e.g., a `Timerowner` with a `timerdone()` method) implements an interface, and an instance of that object is passed to the notifier (e.g., `Timer`).
    *   **Customizing Behavior**: For instance, `Arrays.sort` can be customized by providing an object that implements the `Comparator` interface, which defines a `compare(T o1, T o2)` method.
*   **Functional Interfaces**: These are interfaces that define a **single abstract method**. `Comparator` and `Timerowner` are examples of functional interfaces.
*   **Lambda Expressions**: Java now allows functions to be **passed directly in place of functional interfaces** using lambda expressions.
    *   **Syntax**: `(Parameters) -> Body`. The return value and type are often implicit and inferred by the compiler.
    *   **Usage**: Instead of creating a separate class that implements `Comparator`, you can pass a lambda directly to `Arrays.sort` (e.g., `Arrays.sort(strarr, (s1, s2) -> s1.length() - s2.length())`).
    *   **Type Inference**: Java can often infer the types of lambda parameters from the context where the lambda is used (e.g., `(s1, s2) ->` instead of `(String s1, String s2) ->`).
    *   The function that receives the lambda expression must still be defined to accept a functional interface type as its argument.
*   **Method References**:
    *   If a lambda expression's `Body` consists of a **single function call**, it can be concisely expressed as a **method reference**.
    *   **Forms**:
        *   `ClassName::StaticMethod` (e.g., `Integer::sum` for a static sum method).
        *   `ClassName::InstanceMethod` (where the first argument to the lambda becomes the receiver object).
        *   `object::InstanceMethod` (where `object` is a specific instance).
        *   `ClassName::new` (for constructors).

### Streams

*   **Concept**: Streams provide a powerful, declarative way to **process collections of data**. Instead of iterating explicitly, you define a sequence of operations to be applied to the elements.
*   **Alternative to Iterators**: While iterators provide a way to loop through a collection, streams offer a more functional approach where you chain operations to transform and reduce data.
*   **Benefits**:
    *   **Declarative Style**: You focus on *what* to compute (e.g., `filter` for length, then `count`), rather than *how* to iterate and manage state.
    *   **Parallelization**: Stream processing can be easily **parallelized** (e.g., `words.parallelStream()`) to leverage multi-core processors, improving performance for large datasets.
    *   **Lazy Evaluation**: Stream operations are often **lazy**. This means operations are only performed when a terminal operation is invoked, and elements are processed only as needed. This can be highly efficient for large or even infinite streams, as the entire stream doesn't need to be generated or processed upfront (e.g., finding the first 10 long words can stop after finding them).
*   **Stream Characteristics**:
    *   A stream **does not store its elements**; it's a view over data from an underlying collection or a generator function.
    *   Stream operations are **non-destructive**: they produce a new stream or a result without modifying the original source collection.
*   **Stream Processing Pipeline**: A typical stream operation follows a pattern:
    1.  **Create a Stream**: Obtain a stream from a data source.
    2.  **Intermediate Operations**: Apply zero or more operations that transform the stream into another stream (e.g., `filter`, `map`). These are lazy.
    3.  **Terminal Operation**: Apply a single operation that produces a result or a side-effect, thereby consuming the stream and initiating the processing.
*   **Creating Streams**:
    *   From a **Collection**: Call the `stream()` method on any `Collection` (e.g., `wordlist.stream()`).
    *   From an **Array**: Use the static method `Stream.of(array)` (e.g., `Stream.of(wordarr)`).
    *   **Generating Streams**:
        *   `Stream.generate(Supplier<T> s)`: Creates an **infinite stream** by repeatedly calling a `Supplier` (a function that produces values with no arguments) (e.g., `Stream.generate(() -> "Echo")`, `Stream.generate(Math::random)`).
        *   `Stream.iterate(T seed, UnaryOperator<T> f)`: Creates an **infinite sequential stream** where each element is generated by applying a function `f` to the previous element, starting with `seed` (e.g., `Stream.iterate(0, n -> n + 1)` for integers). Newer Java versions allow a predicate to terminate the stream (`Stream.iterate(0, n -> n < 100, n -> n+1)`).
*   **Intermediate Operations (Transformations)**:
    *   **`filter(Predicate<T> predicate)`**: Selects elements that match a given condition (e.g., `w -> w.length() > 10`).
    *   **`map(Function<T, R> mapper)`**: Applies a function to each element, transforming it into a new type or value (e.g., `s -> s.substring(0, 1)`).
    *   **`flatMap(Function<T, Stream<R>> mapper)`**: Used when the `mapper` function returns a stream for each element. `flatMap` then flattens these individual streams into a single, unified stream (e.g., `flatMap(s -> explode(s))` if `explode` returns a list of characters).
    *   **`limit(long maxSize)`**: Truncates the stream to contain at most `maxSize` elements, useful for making infinite streams finite.
    *   **`skip(long n)`**: Discards the first `n` elements of the stream.
    *   **`takeWhile(Predicate<T> predicate)`**: Returns elements from the start of the stream as long as the predicate is true, stopping at the first element for which it is false.
    *   **`dropWhile(Predicate<T> predicate)`**: Discards elements from the start of the stream as long as the predicate is true, returning a stream that contains all subsequent elements.
    *   Other transformations include `distinct()` (removes duplicates) and `sorted()`.
*   **Terminal Operations (Reduction)**:
    *   **`count()`**: Returns the number of elements in the stream.
    *   **`max(Comparator<T> comparator)` and `min(Comparator<T> comparator)`**: Find the largest or smallest element based on a provided `Comparator`.
    *   **`findFirst()`**: Returns an `Optional<T>` containing the first element of the stream, or an empty `Optional` if the stream is empty.
    *   **`Optional` Type**: Terminal operations like `max()`, `min()`, and `findFirst()` return an `Optional` type. This is a container object that may or may not contain a non-null value, explicitly addressing the possibility of an empty result from an empty stream, helping to avoid `NullPointerException`s.