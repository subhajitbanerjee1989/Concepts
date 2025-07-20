
### **Java 7 (Released July 2011)**

* **Language Features (Project Coin):**
    * **Strings in `switch` statements:** Allows `String` objects as the expression in a `switch` statement.
    * **Diamond Operator (`<>`) for Generics:** Type inference for generic instance creation, simplifying code. Example: `List<String> list = new ArrayList<>();`
    * **Try-with-Resources:** Automatic resource management for resources that implement `AutoCloseable`. Resources are guaranteed to be closed.
    * **Catching Multiple Exceptions:** A single `catch` block can handle multiple exception types using `|` (OR) operator.
    * **Numeric Literals with Underscores:** Underscores can be placed between digits in numeric literals for readability (e.g., `1_000_000`).
    * **Binary Literals:** Integers can be expressed in binary format using `0b` or `0B` prefix (e.g., `0b101010`).
* **Core Library Enhancements:**
    * **NIO.2 (New File System API):**
        * Introduced `java.nio.file` package: `Path`, `Paths`, `Files` classes for more robust and platform-independent file system operations (e.g., symbolic links, file attributes, directory traversals).
    * **Fork/Join Framework:** Added `java.util.concurrent.ForkJoinPool` for parallel task execution, based on the work-stealing algorithm.
    * **Concurrency Utilities:** Enhancements to existing `java.util.concurrent` classes.
    * **Small JDBC 4.1 Features:** Including `try-with-resources` support for `Connection`, `Statement`, `ResultSet`.
* **JVM Changes:**
    * Improved garbage collector (G1 GC was integrated and made more stable).
* **Deprecated/Removed:**
    * No major language features or core APIs were removed in Java 7, but some internal APIs were deprecated.

---

### **Java 8 (Released March 2014 - LTS Release)**

Java 8 was a revolutionary release, introducing foundational changes that enabled functional programming paradigms.

* **Language Features:**
    * **Lambda Expressions:**
        * **Concept:** Anonymous functions that provide a concise way to implement functional interfaces (interfaces with a single abstract method, known as SAM types).
        * **Syntax:** `(parameters) -> expression` or `(parameters) -> { statements; }`
    * **Method References:** Shorthand for simple lambda expressions that just call an existing method or constructor.
        * Types: `ClassName::staticMethod`, `object::instanceMethod`, `ClassName::instanceMethod`, `ClassName::new`.
    * **Default Methods in Interfaces:**
        * **Concept:** Interfaces can now have methods with concrete implementations, marked with the `default` keyword.
        * **Benefit:** Allows adding new methods to interfaces without breaking existing implementing classes, ensuring backward compatibility.
    * **Static Methods in Interfaces:** Interfaces can also contain `static` methods.
    * **Type Annotations:** Annotations can now be applied to types (e.g., `List<@NonNull String>`).
* **Core Library Enhancements:**
    * **Stream API (`java.util.stream`):**
        * **Concept:** A powerful API for processing sequences of elements from collections or other data sources in a functional, declarative, and often parallel manner.
        * **Operations:** Supports intermediate operations (e.g., `filter`, `map`, `sorted`) that return a new stream, and terminal operations (e.g., `forEach`, `collect`, `reduce`) that produce a result.
    * **Date and Time API (`java.time`):**
        * **Concept:** A completely new, immutable, and thread-safe API for handling dates, times, instants, and durations, addressing the shortcomings of `java.util.Date` and `java.util.Calendar`.
        * **Key Classes:** `LocalDate`, `LocalTime`, `LocalDateTime`, `ZonedDateTime`, `Instant`, `Duration`, `Period`.
    * **`Optional<T>` Class (`java.util.Optional`):**
        * **Concept:** A container object that may or may not contain a non-null value.
        * **Benefit:** Helps to explicitly model the absence of a value, reducing the likelihood of `NullPointerException`s.
    * **`CompletableFuture`:** Enhanced asynchronous programming with improved support for chaining and composing asynchronous operations.
    * **Concurrency Utilities:** `ConcurrentHashMap` was re-engineered for better performance.
    * **Base64 Encoding/Decoding:** Standardized Base64 support in `java.util.Base64`.
* **JVM Changes:**
    * **PermGen Space Removal:** The permanent generation (PermGen) memory area was removed and replaced by Metaspace, which uses native memory. This typically reduces `OutOfMemoryError` issues related to class metadata.
    * **HotSpot JVM Improvements:** Enhanced `java.lang.invoke` for performance.
* **Deprecated/Removed:**
    * **`sun.misc.Unsafe`:** While not removed, usage was highly discouraged and its future was uncertain (though still widely used internally and by some libraries).
    * **Nashorn JavaScript Engine:** Integrated a new JavaScript engine into the JVM (later deprecated and removed).

---

### **Java 9 (Released September 2017)**

Java 9's headline feature was the Module System, a massive architectural change.

* **Language Features:**
    * **JShell (REPL - Read-Eval-Print Loop):** An interactive tool for quickly experimenting with Java code snippets and APIs from the command line.
    * **Private Methods in Interfaces:** `private` and `private static` methods can now be defined in interfaces to support common helper logic for `default` and `static` interface methods.
    * **Try-with-Resources for Effectively Final Variables:** Resources used in a `try-with-resources` statement can now be *effectively final* local variables (not just newly declared variables).
* **Core Library Enhancements:**
    * **Java Platform Module System (JPMS - Project Jigsaw):**
        * **Concept:** Modularity at the platform and application level. Modules (`.jmod` files) explicitly declare their dependencies (`requires`) and exported packages (`exports`).
        * **Goals:** Strong encapsulation, reliable configuration, improved performance, and scalability.
        * **`module-info.java`:** The module descriptor file.
    * **Collection Factory Methods:**
        * **Concept:** Convenient static factory methods on `List`, `Set`, and `Map` interfaces for creating unmodifiable collections.
        * **Examples:** `List.of("A", "B");`, `Set.of("X", "Y");`, `Map.of("k1", "v1", "k2", "v2");`
    * **Stream API Enhancements:**
        * **`takeWhile()` and `dropWhile()`:** For conditionally taking/dropping elements from a stream based on a predicate.
        * **`ofNullable()`:** Creates a stream with a single element if the value is non-null, or an empty stream if null.
    * **`Optional` Class Enhancements:** `or()`, `ifPresentOrElse()`.
    * **Process API Improvements:** Enhanced API for controlling and managing operating system processes (`ProcessHandle`).
    * **CompletableFuture API Improvements:** Added new methods for scheduling delays and timeouts.
    * **Stack-Walking API:** A new API for efficiently walking stack frames (`StackWalker`).
* **JVM Changes:**
    * **G1 GC:** Became the default garbage collector.
    * **Compact Strings:** Optimized `String` storage by using a byte array for Latin-1 characters, reducing memory footprint.
    * **Multi-Release JARs:** Allows a single JAR file to contain different versions of class files for different Java releases.
* **Deprecated/Removed:**
    * **`finalize()` method:** Deprecated for removal (from `Object.class`).
    * **Applet API:** Deprecated for removal.
    * **JVM TI Heap Inspection:** Removed `HeapInspection` capabilities.
    * **`java.awt.peer` package:** Removed.

---

### **Java 10 (Released March 2018)**

* **Language Features:**
    * **Local-Variable Type Inference (`var`):**
        * **Concept:** Introduces `var` as a reserved type name for local variable declarations where the compiler can infer the actual type from the initializer.
        * **Benefit:** Reduces boilerplate and improves code readability.
        * **Constraints:** Only for local variables, must be initialized, cannot be used for fields, method parameters, or return types.
        * **Example:** `var message = "Hello, Java 10!";`
* **Core Library Enhancements:**
    * **`Collectors.toUnmodifiableList/Set/Map()`:** Added `toUnmodifiableList`, `toUnmodifiableSet`, and `toUnmodifiableMap` to `java.util.stream.Collectors`.
    * **`Optional.orElseThrow()`:** Method without arguments.
* **JVM Changes:**
    * **Application Class-Data Sharing (AppCDS):** Extends CDS to allow archiving classes from user JARs, improving startup performance and reducing memory footprint.
    * **Experimental JIT Compiler (Graal):** Introduced Graal as an experimental JIT compiler.
    * **Thread-Local Handshakes:** A way to execute a callback on threads without performing a global JVM safepoint.
* **Release Cadence:** Adopted a strict six-month release cadence for new Java versions, and a new versioning scheme.

---

### **Java 11 (Released September 2018 - LTS Release)**

* **Language Features:**
    * **HTTP Client (Standardized):**
        * **Concept:** The new HTTP Client API (`java.net.http`) introduced in Java 9 (incubator) is now standardized.
        * **Features:** Supports HTTP/1.1 and HTTP/2, as well as WebSockets. Provides a fluent, asynchronous API.
    * **Lambda Parameters for `var`:** You can now use `var` for formal parameters in implicitly typed lambda expressions.
        * **Example:** `(var s1, var s2) -> s1 + s2`
    * **Running Source Code Files Directly:** You can now execute a single Java source code file without explicit compilation using `java MyFile.java`.
* **Core Library Enhancements:**
    * **New String Methods:** `isBlank()`, `lines()`, `strip()`, `stripLeading()`, `stripTrailing()`, `repeat(int count)`.
    * **`Files.readString()` and `Files.writeString()`:** Simplified reading file content to a `String` and writing a `String` to a file.
    * **`Optional.isEmpty()`:** A convenience method that returns `true` if an `Optional` is empty, equivalent to `!isPresent()`.
* **JVM Changes:**
    * **ZGC (Z Garbage Collector):** A scalable, low-latency garbage collector (experimental in Java 11).
    * **Epsilon GC:** A no-op garbage collector for performance testing or very short-lived applications.
    * **Dynamic Class-File Constants:** New constant pool forms.
* **Deprecated/Removed:**
    * **Nashorn JavaScript Engine:** Deprecated for removal.
    * **Applet API:** Deprecated for removal.
    * **`finalize()` method:** Completely **removed** from `java.lang.Object`. Use `java.lang.ref.Cleaner` instead.
    * **Java EE and CORBA Modules:** Removed from the JDK. These APIs are now separate and managed by Jakarta EE.
    * **`sun.misc.Unsafe` methods:** Some methods restricted.

---

### **Java 12 (Released March 2019)**

* **Language Features:**
    * **Switch Expressions (Preview - Standardized in Java 14):**
        * **Concept:** Allows `switch` to be used as an **expression** (returning a value) in addition to being a statement.
        * **New Syntax:** Introduces `case L ->` for a simpler, less error-prone syntax that avoids fall-through.
* **Core Library Enhancements:**
    * **`Collectors.teeing()`:** A new `Collectors` method that allows two downstream collectors to process the same input and then merge their results using a provided `BiFunction`.
    * **String Transformations:** `indent()` and `transform()`.
* **JVM Changes:**
    * **Shenandoah GC (Experimental):** A low-pause-time garbage collector.
    * **Microbenchmark Suite (JMH):** Integrated a new microbenchmark suite into the JDK.

---

### **Java 13 (Released September 2019)**

* **Language Features:**
    * **Text Blocks (Preview - Standardized in Java 15):**
        * **Concept:** Multi-line string literals defined by triple quotes (`"""`). They avoid the need for explicit escape sequences for newlines and quotes and automatically manage indentation.
        * **Benefit:** Greatly improves readability for embedding HTML, XML, JSON, SQL, etc., in Java code.
    * **Switch Expressions (Second Preview - `yield` Keyword):**
        * **Concept:** Introduced the `yield` keyword to return a value from a `switch` block, allowing for multi-statement `case` blocks in `switch` expressions.
* **Core Library Enhancements:**
    * **`Files.readString()`/`writeString()` with `Charset`:** Overloaded methods to specify character encoding.

---

### **Java 14 (Released March 2020)**

* **Language Features:**
    * **Records (Preview - Standardized in Java 16):**
        * **Concept:** A new kind of class (`record`) designed for concisely declaring "plain data carriers."
        * **Auto-generated:** Compiler automatically generates compact constructor, accessor methods, `equals()`, `hashCode()`, and `toString()`.
        * **Immutability:** Records are implicitly `final` and their components are effectively `final`.
    * **Pattern Matching for `instanceof` (Preview - Standardized in Java 16):**
        * **Concept:** Simplifies type checking and casting by allowing the declaration of a pattern variable directly within the `instanceof` operator.
        * **Benefit:** Eliminates the need for an explicit cast after checking the type. Example: `if (obj instanceof String s)`
    * **Switch Expressions (Standard):** The feature introduced in preview is now standardized and permanent.
* **Core Library Enhancements:**
    * **Helpful NullPointerExceptions:** JVM provides more precise information in `NullPointerException` messages, indicating which variable or expression was null.
* **JVM Changes:**
    * **Non-Volatile Mapped Byte Buffers:** Allows `MappedByteBuffer` to be created in non-volatile memory.
* **Deprecated/Removed:**
    * **Nashorn JavaScript Engine:** Removed from the JDK.
    * **`pack200` and `unpack200` tools and APIs:** Deprecated for removal (due to decline in usage with new compression algorithms).

---

### **Java 15 (Released September 2020)**

* **Language Features:**
    * **Sealed Classes (Preview - Standardized in Java 17):**
        * **Concept:** Allows a class or interface to explicitly restrict which other classes or interfaces may extend or implement it.
        * **Keywords:** Uses `sealed` (for the class/interface) and `permits` (to list permitted subclasses/implementations).
        * **Benefit:** Enables more explicit and controlled class hierarchies, useful for domain modeling and exhaustive `switch` statements.
    * **Text Blocks (Standard):** The feature introduced in preview is now standardized and permanent.
    * **Records (Second Preview)** and **Pattern Matching for `instanceof` (Second Preview)**: Continued previews for these features.
* **JVM Changes:**
    * **ZGC (Z Garbage Collector):** Moved from experimental to production.
    * **Shenandoah GC:** Moved from experimental to production.
    * **Hidden Classes:** Introduced for frameworks that generate classes at runtime.
* **Deprecated/Removed:**
    * **RMI Activation:** Deprecated for removal.
    * **`pack200` and `unpack200` tools and APIs:** Removed.

---

### **Java 16 (Released March 2021)**

* **Language Features:**
    * **Records (Standard):** Records are now a permanent feature of the Java language.
    * **Pattern Matching for `instanceof` (Standard):** This feature is also now permanent.
    * **Sealed Classes (Second Preview):** Continued preview for this feature.
* **Core Library Enhancements:**
    * **Stream.toList():** A convenient new terminal operation for Streams that collects elements into an unmodifiable `List`.
    * **New `java.time` classes:** `ZoneOffset.ofHoursMinutes()`, `MonthDay.now(Clock)`.
* **JVM Changes:**
    * **Foreign Linker API (Incubator):** Initial incubator module for accessing native code.
    * **Vector API (Incubator):** Initial incubator module for expressing vector computations that reliably compile at runtime to optimal vector instructions on supported CPU architectures.
    * **Strongly Encapsulate JDK Internals by Default:** Made it harder to access internal JDK APIs.
    * **Enable C++14 Language Features:** Modernized the C++ code used within the JDK.

---

### **Java 17 (Released September 2021 - LTS Release)**

* **Language Features:**
    * **Sealed Classes (Standard):** This feature is now a permanent part of the Java language.
* **Core Library Enhancements:**
    * **New `RandomGenerator` API:** Provides new interfaces and implementations for pseudorandom number generators, offering more flexibility and better algorithms.
* **JVM Changes:**
    * **Restored Always-Strict Floating-Point Semantics:** The JVM now consistently enforces strict floating-point computations by default across all platforms. The `strictfp` keyword is still valid but no longer has practical effect unless specific JVM flags are used to revert.
    * **Context-Specific Deserialization Filters:** Improves security by allowing incoming serialization data to be filtered.
* **Deprecated/Removed:**
    * **Applet API:** Deprecated for removal in Java 9, now marked for final removal.
    * **Security Manager:** Deprecated for removal in a future release.
    * **RMI Activation:** Removed.

---

### **Java 18 (Released March 2022)**

* **Language Features:**
    * **Simple Web Server:** A minimal HTTP server provided as a command-line tool for serving static files, primarily for prototyping and testing.
* **Core Library Enhancements:**
    * **Charset for `Files.readString()`/`writeString()`:** Added the ability to specify a `Charset` when using these methods.
* **JVM Changes:**
    * **`finalize()` Method Removed:** The `finalize()` method was completely **removed** from `java.lang.Object`.
    * **Vector API (Second Incubator).**
    * **Foreign Function & Memory API (Incubator):** New incubator module (evolution of Foreign Linker API).

---

### **Java 19 (Released September 2022)**

* **Language Features:**
    * **Virtual Threads (Preview - Standardized in Java 21):**
        * **Concept:** Lightweight threads managed by the JVM (`java.lang.Thread.Builder`). They drastically reduce the overhead of concurrent programming by mapping many virtual threads to fewer platform (OS) threads.
        * **Benefit:** Enables writing highly concurrent applications using the familiar thread-per-request style without blocking OS threads, leading to higher throughput.
    * **Record Patterns (Preview - Standardized in Java 21):**
        * **Concept:** Extends pattern matching to allow destructuring of record values directly in `instanceof` expressions and `switch` statements/expressions.
    * **Pattern Matching for `switch` (Preview - Standardized in Java 21):**
        * **Concept:** Expands `switch` expressions and statements to allow patterns (including record patterns and type patterns) in `case` labels. Also allows `null` in `case` labels.
* **Core Library Enhancements:**
    * **Foreign Function & Memory API (Second Incubator).**
    * **Vector API (Third Incubator).**
* **JVM Changes:**
    * **Unnamed Patterns and Variables (Preview - Standardized in Java 22):** The `_` underscore character can be used to denote unnamed patterns and variables.

---

### **Java 20 (Released March 2023)**

* **Language Features:**
    * **Virtual Threads (Second Preview):** Continued refinement of virtual threads.
    * **Record Patterns (Second Preview):** Continued refinement of record patterns.
    * **Pattern Matching for `switch` (Second Preview):** Continued refinement of pattern matching for switch.
* **Core Library Enhancements:**
    * **Scoped Values (Preview):** A new mechanism to share immutable data within and across threads, intended as a safer, more performant alternative to thread-local variables.
    * **Foreign Function & Memory API (Third Incubator).**
    * **Vector API (Fourth Incubator).**

---

### **Java 21 (Released September 2023 - LTS Release)**

* **Language Features:**
    * **Virtual Threads (Standard):** The revolutionary lightweight concurrency feature is now permanent.
    * **Record Patterns (Standard):** Now a permanent feature, greatly enhancing pattern matching capabilities.
    * **Pattern Matching for `switch` (Standard):** This powerful feature is now permanent, allowing for highly expressive and safe type-based dispatch.
    * **Sequenced Collections:**
        * **Concept:** New interfaces (`SequencedCollection`, `SequencedSet`, `SequencedMap`) were introduced to represent collections with a defined encounter order.
        * **Benefit:** Provides uniform APIs for accessing the first/last elements, reversing the collection, and adding/removing elements at either end.
    * **String Templates (Preview - Second Preview in Java 22):**
        * **Concept:** A new feature for easier and more readable string interpolation and dynamic string construction. Example: `STR."Hello \{name}!"`
    * **Unnamed Patterns and Variables (Preview - Standardized in Java 22):** Using `_` for unnamed patterns (e.g., in record patterns or catch blocks) and unnamed variables.
    * **Unnamed Classes and Instance Main Methods (Preview - Second Preview in Java 22):** Aims to simplify the entry point for small programs, allowing a class to be declared without a name and a `main` method to be non-static.
* **Core Library Enhancements:**
    * **Key Encapsulation Mechanism API:** New API for cryptographic key encapsulation.
    * **Vector API (Fifth Incubator).**
    * **Foreign Function & Memory API (Fourth Incubator).**

---

### **Java 22 (Released March 2024)**

* **Language Features:**
    * **String Templates (Second Preview):** Continued refinement of string templates.
    * **Unnamed Variables and Patterns (Standard):** The `_` for unnamed variables and patterns is now a permanent feature.
    * **Unnamed Classes and Instance Main Methods (Second Preview):** Further development of the simplified program entry points.
* **Core Library Enhancements:**
    * **Foreign Function & Memory API (Standard):** Provides a robust and secure way for Java programs to interact with code and data outside the JVM, superseding JNI.
    * **Vector API (Sixth Incubator).**
* **Tooling:**
    * **Launch Multi-File Source-Code Programs:** Enhances the direct running of source code files (`java MyFile.java`) to handle programs spread across multiple files.
* **JVM Changes:**
    * **Scoped Values (Second Preview):** Continued refinement for structured, immutable data sharing.

---

### **Java 23 (Released September 2024 - Non-LTS)**

* **Language Features:**
    * **String Templates (Third Preview).**
    * **Implicitly Declared Classes and Instance Main Methods (Third Preview).**
    * **Region Pinning for G1 (JEP 455):** Improves latency by allowing objects in G1 regions to be pinned, enabling native downcalls to access arrays without needing to copy them.
* **Core Library Enhancements:**
    * **Vector API (Seventh Incubator).**
    * **Stream Gatherers (Preview - JEP 461):** A new `Stream.gather()` intermediate operation to transform elements in a stream, similar to a custom `collect()` but for intermediate operations.
* **JVM Changes:**
    * **Scoped Values (Third Preview).**
    * **Class-File API (Preview - JEP 457):** Provides an API for parsing, generating, and transforming Java class files.

---

### **Java 24 (Released March 2025 - Non-LTS)**

* **Language Features:**
    * **String Templates (Fourth Preview).**
    * **Implicitly Declared Classes and Instance Main Methods (Fourth Preview).**
    * **Record Patterns with Type Arguments (JEP 465):** Extends record patterns to allow type arguments directly, simplifying pattern matching for generic records.
    * **`super(args)` in `enum` constructors (JEP 459):** Allows `super` calls in enum constructors, enabling more flexible initialization patterns for enum constants with specialized behavior.
* **Core Library Enhancements:**
    * **Vector API (Eighth Incubator).**
    * **Stream Gatherers (Second Preview).**
    * **API for Multi-File Programs (JEP 469):** Enhancements to the API for launching multi-file source-code programs, formalizing the behavior.
* **JVM Changes:**
    * **Scoped Values (Standard - JEP 464):** Scoped values are likely to be standardized in Java 24, providing a permanent solution for efficient and safe data sharing across threads without `ThreadLocal` issues.
