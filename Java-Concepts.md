
-----

# **Java 7 (Released July 2011)**

Java 7, codenamed "Project Coin," brought several small but highly impactful language improvements that significantly reduced boilerplate code and improved readability. It also introduced foundational changes to file I/O and concurrency.

  * **Language Features (Project Coin):**

      * **Strings in `switch` statements (JEP 104):**
          * **New:** Allows using `String` objects as the expression in a `switch` statement. Previously, only primitive integer types (byte, short, char, int) and enums were allowed.
          * **Benefit:** Eliminates cumbersome `if-else if` chains when branching based on string values, making code cleaner and more readable.
          * **Behavior:** The `switch` internally uses `String.equals()` and `String.hashCode()`. It's robust against `null` (throws `NullPointerException`) and case-sensitive.
          * **Example:**
            ```java
            String command = "DELETE";
            switch (command) {
                case "CREATE": System.out.println("New record created."); break;
                case "DELETE": System.out.println("Record deleted."); break;
                default: System.out.println("Invalid command.");
            }
            ```
      * **Diamond Operator (`<>`) for Generics (JEP 107):**
          * **New:** Enhances type inference for generic instance creation. When instantiating a generic class, you can omit the explicit type arguments on the right-hand side if the compiler can infer them from the context (e.g., from the variable declaration).
          * **Benefit:** Reduces verbosity and improves code readability, especially with complex nested generic types.
          * **Example:**
            ```java
            List<String> names = new ArrayList<>(); // Compiler infers ArrayList<String>
            Map<Integer, List<String>> map = new HashMap<>(); // Compiler infers HashMap<Integer, List<String>>
            ```
      * **Try-with-Resources (JEP 103):**
          * **New:** Introduces a new form of the `try` statement for automatic resource management. Resources (any object implementing `java.lang.AutoCloseable`) declared within the `try` statement's parentheses are guaranteed to be closed automatically, even if exceptions occur.
          * **Benefit:** Drastically reduces boilerplate `finally` blocks for resource cleanup and helps prevent resource leaks.
          * **Behavior:** Resources are closed in the reverse order of their declaration. If an exception occurs in the `try` block and another in the `close()` method, the `try` block's exception is propagated, and the `close()` exception is suppressed (accessible via `Throwable.getSuppressed()`).
          * **Example:**
            ```java
            try (BufferedReader br = new BufferedReader(new FileReader("data.txt"));
                 FileWriter fw = new FileWriter("output.txt")) {
                String line;
                while ((line = br.readLine()) != null) {
                    fw.write(line.toUpperCase() + "\n");
                }
            } catch (IOException e) {
                System.err.println("Error processing file: " + e.getMessage());
            }
            ```
      * **Catching Multiple Exceptions (`Multi-catch`) (JEP 110):**
          * **New:** A single `catch` block can now handle multiple exception types using a single pipe (`|`) character to separate them.
          * **Benefit:** Reduces code duplication when handling similar logic for different, unrelated exception types. Improves readability.
          * **Constraint:** The exception types in a multi-catch block must not be in an inheritance hierarchy with each other (e.g., you can't catch `IOException | FileNotFoundException` because `FileNotFoundException` extends `IOException`).
          * **Example:**
            ```java
            try {
                // Code that might throw various exceptions
                new FileInputStream("nonexistent.txt");
                new java.sql.Connection("jdbc:wrong:url");
            } catch (FileNotFoundException | SQLException e) {
                System.err.println("A specific file or SQL error occurred: " + e.getMessage());
            } catch (IOException e) { // Broader catch for other IO issues
                System.err.println("General I/O error: " + e.getMessage());
            }
            ```
      * **Numeric Literals with Underscores (JEP 124):**
          * **New:** Underscores (`_`) can be placed between digits in numeric literals (integers and floating-point numbers).
          * **Benefit:** Improves readability for large numbers, similar to how commas are used in natural language. The underscores are ignored by the compiler.
          * **Constraints:** Cannot be placed at the beginning or end of a number, adjacent to a decimal point, or before an `F`/`L` suffix.
          * **Example:** `long population = 8_100_000_000L; double PI = 3.141_592_653;`
      * **Binary Literals (JEP 128):**
          * **New:** Integers can be expressed directly in binary format using the prefix `0b` or `0B`.
          * **Benefit:** Useful when working with bitmasks or low-level binary data.
          * **Example:** `int mask = 0b1011_0010; // Binary representation of 178`

  * **Core Library Enhancements:**

      * **NIO.2 (New File System API - JEP 162):**
          * **New:** Introduced the `java.nio.file` package, providing a robust and comprehensive API for file system operations.
          * **Key Classes:**
              * **`Path`:** Represents a path to a file or directory. Replaces `java.io.File` for many operations.
              * **`Paths`:** Utility class for creating `Path` instances.
              * **`Files`:** A static utility class with methods for file operations (copy, move, delete, create, read attributes, walk file tree, etc.).
          * **Features:** Improved handling of symbolic links, file attributes, directory iteration, and asynchronous I/O.
      * **Fork/Join Framework (JEP 166):**
          * **New:** Added `java.util.concurrent.ForkJoinPool`, a specialized `ExecutorService` for tasks that can be broken into smaller subtasks recursively (divide and conquer). It's designed for highly parallel computations and implements the work-stealing algorithm.
          * **Components:** `ForkJoinTask` (e.g., `RecursiveAction` for void tasks, `RecursiveTask` for tasks returning a value).
      * **Concurrency Utilities Enhancements:** Minor updates and bug fixes to existing classes in `java.util.concurrent`.
      * **Small JDBC 4.1 Features:** Included support for `try-with-resources` with JDBC resources (`Connection`, `Statement`, `ResultSet`).

  * **JVM Changes:**

      * **G1 Garbage Collector (JEP 173):** Garbage-First Garbage Collector, introduced experimentally in Java 6, was fully integrated and made stable, aiming to provide a more predictable pause-time garbage collection for large heaps.
      * **Invokedynamic (JEP 217):** A new bytecode instruction designed to support dynamic languages (like JavaScript, Ruby, Python) on the JVM more efficiently by deferring method invocation linkage to runtime.

  * **Deprecated/Removed:**

      * No major language features or core APIs were removed or deprecated for removal in Java 7, keeping compatibility very high.

-----

# **Java 8 (Released March 2014 - The Functional Revolution - LTS Release)**

Java 8 was arguably the most impactful release since Java 1.0, fundamentally transforming how Java developers write code by introducing functional programming constructs and a powerful new Date/Time API.

  * **Language Features:**

      * **Lambda Expressions (JEP 174):**
          * **Concept:** The cornerstone of functional programming in Java. They provide a concise syntax for expressing instances of **functional interfaces** (interfaces with exactly one abstract method, also known as SAM - Single Abstract Method - types).
          * **Syntax:** `(parameters) -> expression` or `(parameters) -> { statements; }`
          * **Benefits:** Enable more compact and readable code for callbacks, event handlers, and collections processing. Facilitate parallel processing by easily passing behavior.
          * **Functional Interfaces:** Examples include `Runnable`, `Callable`, `Comparator`, and new ones like `Predicate<T>`, `Function<T, R>`, `Consumer<T>`, `Supplier<T>`.
          * **Example:**
            ```java
            // Old way (anonymous inner class)
            Runnable oldRunnable = new Runnable() {
                public void run() { System.out.println("Hello from old Runnable!"); }
            };
            // New way (lambda expression)
            Runnable newRunnable = () -> System.out.println("Hello from new Runnable!");

            List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
            names.forEach(name -> System.out.println("Name: " + name));
            ```
      * **Method References (JEP 308):**
          * **Concept:** A shorthand for lambda expressions that simply call an existing method or constructor. They improve readability when the lambda body just forwards arguments to a method.
          * **Types:**
              * **Static Method:** `ClassName::staticMethod` (e.g., `Integer::parseInt`)
              * **Instance Method of a Particular Object:** `object::instanceMethod` (e.g., `System.out::println`)
              * **Instance Method of an Arbitrary Object of a Particular Type:** `ClassName::instanceMethod` (e.g., `String::length` for a `Comparator<String>`)
              * **Constructor:** `ClassName::new` (e.g., `ArrayList::new`)
          * **Example:**
            ```java
            List<String> cities = Arrays.asList("London", "Paris", "Tokyo");
            cities.forEach(System.out::println); // Method reference for s -> System.out.println(s)
            cities.sort(String::compareToIgnoreCase); // Method reference for (s1, s2) -> s1.compareToIgnoreCase(s2)
            ```
      * **Default Methods in Interfaces (JEP 177):**
          * **Concept:** Allows interfaces to have methods with concrete implementations, marked with the `default` keyword.
          * **Benefit:** Crucial for **backward compatibility**. It enabled adding new methods to existing interfaces (like `Collection.stream()` or `List.forEach()`) without breaking all existing implementing classes. Implementing classes can override default methods.
          * **Example:**
            ```java
            interface MyLogger {
                void log(String message); // Abstract method
                default void logInfo(String message) {
                    log("[INFO] " + message); // Calls abstract method
                }
            }
            ```
      * **Static Methods in Interfaces (JEP 178):**
          * **Concept:** Interfaces can now contain `static` methods. These methods are tied to the interface itself, not to any implementing object, and are called directly on the interface name.
          * **Benefit:** Useful for utility methods related to the interface, factory methods, or common logic that doesn't depend on an instance.
          * **Example:**
            ```java
            interface MathUtils {
                static int add(int a, int b) {
                    return a + b;
                }
            }
            int sum = MathUtils.add(5, 3); // Called directly on the interface
            ```
      * **Type Annotations (JEP 104):**
          * **Concept:** Extends annotation usage to include any type use, not just declarations. This means annotations can be applied to generic type arguments, array levels, casts, and `new` expressions.
          * **Example:** `List<@NonNull String> names; String @NotNull [] messages; (@TypeAnnotation MyClass) myObject;`
          * **Benefit:** Enables richer static analysis and type checking by tools, particularly useful for pluggable type systems.

  * **Core Library Enhancements:**

      * **Stream API (`java.util.stream` - JEP 160):**
          * **Concept:** A powerful API for processing sequences of elements from collections or other data sources in a functional, declarative, and often parallel manner. Streams are not data structures; they are sequences of elements that support aggregate operations.
          * **Key Characteristics:**
              * **Source:** Can come from collections, arrays, I/O channels, etc.
              * **Intermediate Operations:** Transform a stream into another stream (e.g., `filter()`, `map()`, `sorted()`, `distinct()`, `limit()`, `skip()`). They are *lazy*.
              * **Terminal Operations:** Produce a result or a side effect (e.g., `forEach()`, `collect()`, `reduce()`, `count()`, `min()`, `max()`, `anyMatch()`, `findFirst()`). They *trigger* pipeline execution.
              * **Immutability:** Operations produce a new stream; the original source is not modified.
              * **Parallelism:** Easily convertible to parallel streams using `parallelStream()`.
          * **`Collectors` Class:** Provides rich terminal operations for collecting stream elements into various data structures (e.g., `toList()`, `toSet()`, `toMap()`, `groupingBy()`, `partitioningBy()`).
          * **Example (Complex):**
            ```java
            Map<String, List<String>> employeesByDept = employees.stream()
                .filter(e -> e.getSalary() > 50000)
                .sorted(Comparator.comparing(Employee::getName))
                .collect(Collectors.groupingBy(Employee::getDepartment,
                                               Collectors.mapping(Employee::getName, Collectors.toList())));
            ```
      * **Date and Time API (`java.time` - JEP 150):**
          * **Concept:** A completely new, immutable, and thread-safe API for handling dates, times, instants, and durations, designed to address the many flaws and complexities of the old `java.util.Date` and `java.util.Calendar` classes. Inspired by Joda-Time.
          * **Key Classes:**
              * **`LocalDate`:** Date without time or timezone.
              * **`LocalTime`:** Time without date or timezone.
              * **`LocalDateTime`:** Date and time without timezone.
              * **`ZonedDateTime`:** Date, time, and timezone.
              * **`Instant`:** A point in time on the timeline (machine readable).
              * **`Duration`:** A time-based amount (e.g., "30 seconds").
              * **`Period`:** A date-based amount (e.g., "2 years, 3 months, 5 days").
              * **`DateTimeFormatter`:** For parsing and formatting dates/times.
          * **Benefits:** Clarity, immutability, thread-safety, clear separation of concerns (date vs. time vs. timezone).
          * **Example:**
            ```java
            LocalDate today = LocalDate.now();
            LocalTime now = LocalTime.of(14, 30);
            LocalDateTime eventTime = LocalDateTime.of(today.plusDays(7), now);
            ZonedDateTime zoneEvent = eventTime.atZone(ZoneId.of("America/New_York"));
            System.out.println("Event in NY: " + zoneEvent.format(DateTimeFormatter.ISO_ZONED_DATE_TIME));
            ```
      * **`Optional<T>` Class (`java.util.Optional` - JEP 252):**
          * **Concept:** A container object that may or may not contain a non-null value. It's designed to provide a clear way to represent the absence of a value, thereby helping to avoid `NullPointerException`s and making code intent clearer.
          * **Methods:** `isPresent()`, `get()` (use with caution\!), `orElse()`, `orElseGet()`, `orElseThrow()`, `map()`, `flatMap()`, `filter()`, `ifPresent()`.
          * **Example:**
            ```java
            Optional<String> name = Optional.ofNullable(getUserName());
            String displayName = name.orElse("Guest"); // Provides a default value if absent
            name.ifPresent(n -> System.out.println("Welcome, " + n)); // Executes action only if present
            ```
      * **`CompletableFuture`:** Enhanced asynchronous programming with richer API for chaining and composing asynchronous operations, building on `Future` and `Callable`.
      * **Concurrency Utilities:** `ConcurrentHashMap` was re-engineered for better performance under high concurrency, using a completely new internal design.
      * **Base64 Encoding/Decoding:** Standardized Base64 support added in `java.util.Base64` class.
      * **Parallel Arrays:** New methods in `java.util.Arrays` for parallel operations on arrays (e.g., `parallelSort()`).

  * **JVM Changes:**

      * **PermGen Space Removal:** The permanent generation (PermGen) memory area (used for class metadata and interned strings) was removed from the HotSpot JVM and replaced by **Metaspace**. Metaspace uses native memory, which dynamically resizes by default, often reducing `OutOfMemoryError`s related to class metadata limits.
      * **HotSpot JVM Improvements:** Continued optimizations, especially related to `invokedynamic` (introduced in Java 7) for performance of lambda expressions and method handles.
      * **Type Annotation Support in JVM:** The JVM and class file format were updated to support type annotations.

  * **Deprecated/Removed:**

      * **Nashorn JavaScript Engine:** Integrated as the default JavaScript engine (replacing Rhino), but later deprecated (Java 11) and removed (Java 15).
      * **`sun.misc.Unsafe`:** While not removed, its use was discouraged and its future was uncertain, with intentions to restrict access. Many internal usages were replaced with standard APIs.

-----

# **Java 9 (Released September 2017)**

Java 9's flagship feature was the Module System, a significant architectural change aimed at making Java applications more reliable, secure, and performant.

  * **Language Features:**

      * **JShell (REPL - Read-Eval-Print Loop) (JEP 222):**
          * **New Tool:** An interactive command-line tool (`jshell`) for quickly exploring Java code snippets, APIs, and language features without needing to compile and run a full application.
          * **Benefit:** Great for learning, prototyping, and testing small pieces of code.
          * **Example:**
            ```java
            $ jshell
            jshell> String name = "World";
            name ==> "World"
            jshell> System.out.println("Hello, " + name + "!");
            Hello, World!
            ```
      * **Private Methods in Interfaces (JEP 213):**
          * **New:** `private` and `private static` methods can now be defined in interfaces.
          * **Benefit:** Enables encapsulating common helper logic used by `default` or `static` interface methods, improving code organization and preventing utility methods from polluting the public API of the interface.
          * **Example:**
            ```java
            interface MyProcessor {
                default void processData() {
                    init();
                    // ... processing logic ...
                    cleanup();
                }
                private void init() { System.out.println("Initializing processor."); }
                private static void cleanup() { System.out.println("Cleaning up resources."); }
            }
            ```
      * **Try-with-Resources for Effectively Final Variables (JEP 213):**
          * **Modification:** Resources used in a `try-with-resources` statement can now be *effectively final* local variables (variables whose value is never changed after initialization) in addition to newly declared variables.
          * **Benefit:** Makes `try-with-resources` more flexible and concise when a resource is already available.
          * **Example:**
            ```java
            BufferedReader br = new BufferedReader(new FileReader("input.txt"));
            try (br) { // 'br' is effectively final
                System.out.println(br.readLine());
            } catch (IOException e) { e.printStackTrace(); }
            ```

  * **Core Library Enhancements:**

      * **Java Platform Module System (JPMS - Project Jigsaw) (JEP 200, 201, 220, 260, 261, 282, 305):**
          * **Concept:** The biggest architectural change. It introduces a modularity system at the Java platform and application level. Applications are composed of interconnected, self-describing modules.
          * **Goals:**
              * **Reliable Configuration:** Modules explicitly declare their dependencies, eliminating classpath hell.
              * **Strong Encapsulation:** Access to package internals is restricted; only explicitly `exported` packages are visible outside a module.
              * **Scalability:** Allows building smaller, custom runtimes (e.g., using `jlink`).
              * **Security:** Stronger encapsulation enhances security.
          * **Module Descriptor (`module-info.java`):** A file at the root of a module that defines its name and characteristics:
              * `requires`: Declares a dependency on another module.
              * `exports`: Specifies packages whose `public` and `protected` members are accessible to other modules.
              * `opens`: Specifies packages that are open for deep reflection (even for `private` members) at runtime to specific modules or all modules.
              * `uses`: Declares that the module consumes a service.
              * `provides ... with ...`: Declares that the module provides an implementation of a service.
          * **Example `module-info.java`:**
            ```java
            module com.example.app {
                requires java.sql; // Depends on the java.sql module
                requires com.example.service; // Depends on a custom service module
                exports com.example.app.api; // Exposes this package to other modules
                opens com.example.app.internal; // Allows reflection on this package
            }
            ```
      * **Collection Factory Methods (JEP 269):**
          * **New:** Convenient static factory methods on `List`, `Set`, and `Map` interfaces for creating unmodifiable collections.
          * **Benefit:** More concise and readable way to create small collections, avoiding boilerplate `add()` calls.
          * **Immutability:** The returned collections are immutable, meaning you cannot add, remove, or modify elements after creation (attempting to do so throws `UnsupportedOperationException`).
          * **Examples:**
            ```java
            List<String> names = List.of("Alice", "Bob", "Charlie");
            Set<Integer> numbers = Set.of(1, 2, 3);
            Map<String, Integer> ages = Map.of("Alice", 30, "Bob", 25);
            ```
      * **Stream API Enhancements:**
          * **`takeWhile(Predicate)` (JEP 266):** Returns a stream of elements from the beginning as long as the predicate is true, then stops.
          * **`dropWhile(Predicate)` (JEP 266):** Discards elements from the beginning as long as the predicate is true, then includes all remaining elements.
          * **`ofNullable(T)`:** Creates a stream with a single element if the value is non-null, or an empty stream if null. Useful for flat-mapping nullable values.
          * **`iterate(seed, hasNext, next)`:** An overloaded `iterate` method that allows specifying a `Predicate` to terminate the stream.
      * **`Optional` Class Enhancements:**
          * **`or(Supplier<Optional<T>>)`:** Returns another `Optional` if the current one is empty, otherwise returns the current `Optional`.
          * **`ifPresentOrElse(Consumer, Runnable)`:** Performs an action if a value is present, otherwise performs a different empty-action.
      * **Process API Improvements (JEP 102):** Enhanced API for controlling and managing operating system processes (`ProcessHandle` interface). Allows getting process IDs, parent/child processes, and information about the process.
      * **`CompletableFuture` API Improvements:** Added new methods for scheduling delays and timeouts, and better support for subclassing.
      * **Stack-Walking API (JEP 259):** A new API (`java.lang.StackWalker`) for efficiently walking stack frames, providing more control and performance than traditional `Throwable.getStackTrace()`.

  * **JVM Changes:**

      * **G1 GC:** Became the default garbage collector, replacing the Parallel GC.
      * **Compact Strings (JEP 254):** Optimized `String` storage. `String` objects that contain only Latin-1 characters are stored in a `byte` array instead of a `char` array (which uses 2 bytes per char), significantly reducing memory footprint for many applications.
      * **Multi-Release JARs (JEP 238):** Allows a single JAR file to contain different versions of class files for different Java major releases. This means a library can provide optimized or updated code for newer Java versions while remaining compatible with older runtimes.
      * **Ahead-of-Time (AOT) Compilation (Experimental):** Introduced experimental support for AOT compilation using `jaotc` tool, to improve startup time.
      * **Unified JVM Logging (JEP 258):** A common logging system for all JVM components.

  * **Deprecated/Removed:**

      * **`finalize()` method (JEP 255):** Deprecated for removal (from `Object.class`). Discouraged due to unpredictable behavior, performance overhead, and potential for resource leaks. Replaced by `java.lang.ref.Cleaner`.
      * **Applet API (JEP 289):** Deprecated for removal. Browser plugin architectures became obsolete.
      * **JVM TI Heap Inspection:** Removed `HeapInspection` capabilities in JVM Tool Interface.
      * **`java.awt.peer` package:** Removed.
      * **`sun.misc.Unsafe` methods:** Access to some methods was further restricted due to strong encapsulation from JPMS.

-----

# **Java 10 (Released March 2018)**

Java 10 was the first release under the new six-month cadence. Its most prominent feature was local-variable type inference.

  * **Language Features:**
      * **Local-Variable Type Inference (`var`) (JEP 286):**
          * **New:** Introduces `var` as a reserved type name for local variable declarations. The compiler infers the actual type from the initializer on the right-hand side.
          * **Benefit:** Reduces boilerplate code and improves readability, especially with complex generic types or long class names.
          * **Constraints:**
              * Only for **local variables** (inside methods, constructors, or initializers).
              * Must be **initialized** at the point of declaration.
              * Cannot be used for fields, method parameters, method return types, or `catch` parameters.
              * Cannot be initialized to `null` without an explicit cast (as `null` has no definite type).
              * Not a keyword, but a **reserved type name**.
          * **Example:**
            ```java
            var message = "Hello, Java 10!"; // Type inferred as String
            var numbers = new ArrayList<Integer>(); // Type inferred as ArrayList<Integer>
            var result = getCalculationResult(); // Type inferred from method return type
            ```
  * **Core Library Enhancements:**
      * **`Collectors.toUnmodifiableList/Set/Map()`:** Added `toUnmodifiableList`, `toUnmodifiableSet`, and `toUnmodifiableMap` static methods to `java.util.stream.Collectors`. These are convenient for collecting stream elements into immutable collections, similar to `List.of()`, `Set.of()`, `Map.of()`.
      * **`Optional.orElseThrow()`:** A no-argument version of `orElseThrow()` was added, making it more concise when you simply want to throw `NoSuchElementException` if the `Optional` is empty.
  * **JVM Changes:**
      * **Application Class-Data Sharing (AppCDS) (JEP 317):** Extends CDS (Class Data Sharing) to allow archiving classes from user JARs. This improves startup performance and reduces memory footprint by sharing common class data across multiple JVM instances.
      * **Experimental JIT Compiler (Graal) (JEP 317):** Introduced Graal as an experimental JIT compiler to allow for potential future performance improvements.
      * **Thread-Local Handshakes (JEP 312):** A way to execute a callback on threads without performing a global JVM safepoint. This improves JVM performance by making it less reliant on costly global safepoints for operations like stack scanning during garbage collection.
  * **Release Cadence & Versioning:**
      * **New Release Model (JEP 322):** Adopted a strict six-month release cadence for new Java versions (March and September).
      * **New Versioning Scheme:** Shifted to a time-based versioning scheme (e.g., JDK 10, JDK 11, etc.), making it easier to track releases.

-----

# **Java 11 (Released September 2018 - LTS Release)**

Java 11 was the first Long-Term Support (LTS) release under the new six-month cycle, focusing on stabilizing features and cleaning up deprecated APIs.

  * **Language Features:**

      * **HTTP Client (Standardized) (JEP 321):**
          * **Concept:** The new HTTP Client API (`java.net.http`) introduced in Java 9 as an incubator module is now standardized.
          * **Features:** Provides a modern, fluent API for sending HTTP requests and receiving responses. Supports HTTP/1.1 and HTTP/2, as well as WebSockets. Designed for both synchronous and asynchronous usage.
          * **Benefit:** Replaces the older, more cumbersome `HttpURLConnection` and provides a more feature-rich, performant, and user-friendly client.
          * **Example:**
            ```java
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest request = HttpRequest.newBuilder()
                                           .uri(URI.create("https://api.example.com/data"))
                                           .GET() // or .POST(HttpRequest.BodyPublishers.ofString("..."))
                                           .build();
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            System.out.println("Response Status: " + response.statusCode());
            System.out.println("Response Body: " + response.body());
            ```
      * **Lambda Parameters for `var` (JEP 323):**
          * **Modification:** You can now use `var` for formal parameters in implicitly typed lambda expressions. This allows you to apply annotations to lambda parameters (which wasn't possible before `var` in lambdas) and also to be explicit about `final` in lambdas without specifying the full type.
          * **Example:**
            ```java
            // Before Java 11, you couldn't annotate 's1'
            BiFunction<String, String, String> concat = (@NonNull var s1, @Nullable var s2) -> s1 + s2;
            ```
      * **Running Source Code Files Directly (JEP 330):**
          * **New Tooling:** You can now execute a single Java source code file without explicit compilation using the `java` launcher.
          * **Benefit:** Simplifies the process of running small programs, scripts, or examples, lowering the barrier to entry for new Java learners.
          * **Usage:** `java MyProgram.java` (compiles and runs in memory).

  * **Core Library Enhancements:**

      * **New String Methods:**
          * **`isBlank()`:** Returns `true` if the string is empty or contains only white space code points (as defined by `Character.isWhitespace`). Differs from `isEmpty()` (which checks length only) and `trim()` (which doesn't remove all whitespace types).
          * **`lines()`:** Returns a `Stream<String>` of lines extracted from the string, terminated by line terminators.
          * **`strip()`, `stripLeading()`, `stripTrailing()`:** More precise whitespace removal than `trim()`, based on Unicode whitespace characters. `trim()` only removes whitespace \<= U+0020.
          * **`repeat(int count)`:** Returns a string whose value is the concatenation of this string `count` times.
          * **Example:**
            ```java
            " Hello ".strip(); // "Hello"
            "   \n  Text  ".stripLeading(); // "Text  "
            "abc".repeat(3); // "abcabcabc"
            "Line1\nLine2".lines().forEach(System.out::println);
            ```
      * **`Files.readString()` and `Files.writeString()` (JEP 330):**
          * **New:** Convenience methods in `java.nio.file.Files` for easily reading file content to a `String` and writing a `String` to a file.
          * **Benefit:** Simplifies common file I/O operations, reducing boilerplate compared to traditional buffered readers/writers.
          * **Example:**
            ```java
            Path filePath = Path.of("sample.txt");
            String content = Files.readString(filePath, StandardCharsets.UTF_8);
            Files.writeString(filePath, "New content for the file.", StandardOpenOption.APPEND);
            ```
      * **`Optional.isEmpty()` (JEP 330):**
          * **New:** A convenience method that returns `true` if an `Optional` is empty (contains no value), equivalent to `!isPresent()`. Improves readability.

  * **JVM Changes:**

      * **ZGC (Z Garbage Collector) (JEP 333):** Introduced as an experimental, scalable, low-latency garbage collector designed for very large heaps (terabytes) with very low pause times (single-digit milliseconds).
      * **Epsilon GC (JEP 318):** Introduced as an experimental "no-op" garbage collector. It allocates memory but doesn't reclaim it. Useful for extreme performance testing, very short-lived applications, or understanding memory allocation behavior.
      * **Dynamic Class-File Constants (JEP 309):** New constant pool forms for `CONSTANT_Dynamic` to support future language and VM features.
      * **Aarch64 Port:** Official port of the JDK to Aarch64 architectures (ARM 64-bit).

  * **Deprecated/Removed:**

      * **Nashorn JavaScript Engine (JEP 335):** Deprecated for removal (and later removed in Java 15).
      * **Applet API (JEP 336):** Deprecated for removal in Java 9, now marked for final removal.
      * **`finalize()` method (JEP 334):** The `finalize()` method was completely **removed** from `java.lang.Object`. Developers should transition to `java.lang.ref.Cleaner` or `try-with-resources` for resource cleanup.
      * **Java EE and CORBA Modules:** Modules related to Java EE (e.g., JAX-B, JAX-WS) and CORBA were removed from the JDK (JEP 320, 332). These APIs are now separate and managed by Jakarta EE. This significantly reduced the JDK footprint.
      * **`sun.misc.Unsafe` methods:** Further restrictions on access to internal APIs due to stronger encapsulation from JPMS.

-----

# **Java 12 (Released March 2019)**

Java 12 was a non-LTS release that continued to introduce and refine language features, particularly `switch` expressions.

  * **Language Features:**
      * **Switch Expressions (Preview) (JEP 325):**
          * **Concept:** Allows `switch` to be used as an **expression** (returning a value) in addition to being a statement.
          * **New Syntax:** Introduces `case L ->` syntax for a simpler, less error-prone structure that executes only the code to the right of the arrow and does not fall through.
          * **Benefit:** Eliminates the need for `break` statements (and thus avoids accidental fall-through bugs) and allows `switch` to be used in assignment statements or as method return values.
          * **Example:**
            ```java
            DayOfWeek day = DayOfWeek.MONDAY;
            String dayType = switch (day) {
                case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> "Workday";
                case SATURDAY, SUNDAY -> "Weekend";
                // default is still required for completeness if not all values handled
            }; // Assigns "Workday" to dayType
            ```
  * **Core Library Enhancements:**
      * **`Collectors.teeing()` (JEP 316):**
          * **New:** A new `Collectors` method (`Collectors.teeing()`) that allows two downstream collectors to process the same input and then merge their results using a provided `BiFunction`.
          * **Benefit:** Useful for performing two different aggregations on a stream and then combining their results.
          * **Example:** Get the min and max values in a single stream pipeline.
            ```java
            double average = Stream.of(1, 2, 3, 4, 5).collect(
                Collectors.teeing(
                    Collectors.summingDouble(i -> i),
                    Collectors.counting(),
                    (sum, count) -> sum / count
                )
            ); // average = 3.0
            ```
      * **String Transformations:**
          * **`indent(int n)`:** Indents each line of the string by `n` spaces. If `n` is negative, it removes leading spaces up to `n` (or to the first non-whitespace character).
          * **`transform(Function)`:** Applies a function to the string and returns the result. Useful for method chaining where the next operation is not a `String` method.
          * **Example:**
            ```java
            "Hello\nWorld".indent(4); // Adds 4 spaces to each line
            "Hello".transform(s -> s + " World").toUpperCase(); // "HELLO WORLD"
            ```
  * **JVM Changes:**
      * **Shenandoah GC (Experimental) (JEP 346):** A new, experimental ultra-low-pause-time garbage collector, designed for large heaps where pauses must be consistently short.
      * **G1 Enhancements (JEP 344):** Improved mixed collection for G1, aborting collection if too much time is spent.
      * **JVM Constants API (JEP 334):** An API to describe the nominal descriptions of run-time constants that are loadable from the constant pool.

-----

# **Java 13 (Released September 2019)**

  * **Language Features:**

      * **Text Blocks (Preview) (JEP 355):**
          * **Concept:** Multi-line string literals defined by triple quotes (`"""`). They avoid the need for explicit escape sequences for newlines, quotes, and backslashes, and automatically manage indentation.
          * **Benefit:** Greatly improves readability for embedding HTML, XML, JSON, SQL, or any multi-line text directly within Java code.
          * **Rules:** The closing `"""` determines the base indentation.
          * **Example:**
            ```java
            String json = """
                          {
                            "name": "Alice",
                            "age": 30
                          }
                          """; // Compiler automatically handles indentation from closing """
            ```
      * **Switch Expressions (Second Preview - `yield` Keyword) (JEP 354):**
          * **Concept:** Introduced the `yield` keyword to explicitly produce a value from a `switch` block (when the `case` involves multiple statements and needs a code block `{}`).
          * **Behavior:** `yield` is used instead of `return` in a `switch` expression's `case` block to indicate the value to be returned by the `switch` expression.
          * **Example:**
            ```java
            int numLetters = switch (day) {
                case MONDAY, FRIDAY, SUNDAY -> 6;
                case TUESDAY                -> 7;
                case THURSDAY, SATURDAY     -> 8;
                case WEDNESDAY              -> {
                    System.out.println("Long day!"); // Can have multiple statements
                    yield 9; // explicitly yields a value
                }
            };
            ```

  * **Core Library Enhancements:**

      * **Reimplement the Legacy Socket API (JEP 353):** Reimplemented the underlying implementation of the `java.net.Socket` and `java.net.ServerSocket` APIs to be simpler and easier to maintain, using modern NIO.2 mechanisms.
      * **Dynamic CDS Archiving (JEP 350):** Allows for dynamic archiving of classes at the end of a Java application's execution. This makes it easier to use CDS for custom application classes.

  * **JVM Changes:**

      * **ZGC Uncommit Unused Memory (JEP 351):** Enhanced ZGC to return unused heap memory to the operating system, making it more efficient for applications with fluctuating memory needs.

-----

# **Java 14 (Released March 2020)**

Java 14 brought significant progress on data modeling with Records and enhanced pattern matching.

  * **Language Features:**
      * **Records (Preview - Standardized in Java 16) (JEP 359):**
          * **Concept:** A new kind of class (`record`) designed for concisely declaring "plain data carriers." Records are immutable data classes.
          * **Auto-generated:** The compiler automatically generates:
              * A **canonical constructor** (all-args constructor).
              * **Accessor methods** for each component (e.g., `x()` for `int x`).
              * **`equals()`** and **`hashCode()`** methods that compare all components.
              * A meaningful **`toString()`** method.
          * **Benefit:** Drastically reduces boilerplate code for classes primarily used to hold data, improving readability and maintainability.
          * **Characteristics:** Records are implicitly `final` and cannot extend other classes (but can implement interfaces). Their components are also effectively `final`.
          * **Example:**
            ```java
            record Point(int x, int y) {} // That's it!
            Point p1 = new Point(10, 20);
            System.out.println(p1.x()); // Accessor
            System.out.println(p1); // toString() -> "Point[x=10, y=20]"
            ```
      * **Pattern Matching for `instanceof` (Preview - Standardized in Java 16) (JEP 305):**
          * **Concept:** Simplifies type checking and casting by allowing the declaration of a pattern variable directly within the `instanceof` operator.
          * **Benefit:** Eliminates the need for an explicit cast after checking the type, reducing boilerplate and potential `ClassCastException`s. The pattern variable is in scope only if the `instanceof` check is true.
          * **Example:**
            ```java
            Object obj = "Hello Java 14!";
            if (obj instanceof String s) { // 's' is the pattern variable, automatically cast to String
                System.out.println("String length: " + s.length());
            } else if (obj instanceof Integer i) {
                System.out.println("Integer value: " + i);
            }
            ```
      * **Switch Expressions (Standard):** The feature introduced as a preview in Java 12 and refined in Java 13 is now a permanent part of the Java language.
  * **Core Library Enhancements:**
      * **Helpful NullPointerExceptions (JEP 358):**
          * **Modification:** The JVM now provides more precise information in `NullPointerException` messages, indicating which variable, method, or array access expression was null.
          * **Benefit:** Significantly speeds up debugging for NPEs.
          * **Example (old vs. new):**
              * **Old:** `java.lang.NullPointerException`
              * **New:** `java.lang.NullPointerException: Cannot invoke "String.length()" because "name" is null`
  * **JVM Changes:**
      * **ZGC on macOS and Windows (JEP 377):** ZGC support extended to these platforms.
      * **Non-Volatile Mapped Byte Buffers (JEP 352):** Allows `MappedByteBuffer` to be created in non-volatile memory (NVM), useful for persistent memory programming.
      * **Shenandoah GC (Production Ready):** Moved from experimental to production.
  * **Deprecated/Removed:**
      * **Nashorn JavaScript Engine (JEP 372):** Formally **removed** from the JDK.
      * **`pack200` and `unpack200` tools and APIs (JEP 367):** Formally **removed**. These tools were for highly compressed JAR files.

-----

# **Java 15 (Released September 2020)**

Java 15 continued the evolution of language features with another preview of sealed classes.

  * **Language Features:**
      * **Sealed Classes (Preview - Standardized in Java 17) (JEP 360):**
          * **Concept:** Allows a class or interface to explicitly restrict which other classes or interfaces may extend or implement it.
          * **Keywords:** Uses `sealed` (for the class/interface) and `permits` (to list permitted direct subclasses/implementations).
          * **Benefit:** Enables more explicit and controlled class hierarchies, useful for domain modeling, ensuring exhaustiveness in `switch` statements (when combined with pattern matching), and limiting accidental extension.
          * **Constraint:** Permitted subclasses must be `final`, `sealed`, or `non-sealed`.
          * **Example:**
            ```java
            public abstract sealed class Shape permits Circle, Square, Triangle { ... }
            public final class Circle extends Shape { ... }
            public non-sealed class Square extends Shape { ... } // Can be extended by any class
            public sealed class Triangle extends Shape permits EquilateralTriangle { ... }
            ```
      * **Records (Second Preview)** and **Pattern Matching for `instanceof` (Second Preview)**: Continued previews for these features.
  * **Core Library Enhancements:**
      * **Hidden Classes (JEP 371):**
          * **Concept:** Introduces "hidden classes" which are non-discoverable classes that cannot be directly linked against by the bytecode of other classes.
          * **Benefit:** Primarily for frameworks that generate classes at runtime (e.g., dynamic proxies, code generation libraries) to isolate them and improve the lifecycle management of dynamically generated classes.
  * **JVM Changes:**
      * **ZGC (Z Garbage Collector) (JEP 377):** Moved from experimental to production.
      * **Shenandoah GC (JEP 379):** Moved from experimental to production.
      * **Disabled and Deprecate Biased Locking (JEP 374):** Biased locking, a JVM optimization for uncontended locks, was disabled by default due to maintenance costs and limited performance benefits in modern workloads. Deprecated for removal.

-----

# **Java 16 (Released March 2021)**

Java 16 brought several preview features to standard status, making Records and Pattern Matching for `instanceof` permanent additions.

  * **Language Features:**
      * **Records (Standard) (JEP 395):** Records are now a permanent feature of the Java language.
      * **Pattern Matching for `instanceof` (Standard) (JEP 394):** This feature is also now permanent.
      * **Sealed Classes (Second Preview) (JEP 397):** Continued preview for this feature.
  * **Core Library Enhancements:**
      * **Stream.toList() (JEP 392):**
          * **New:** A convenient new terminal operation for Streams that collects elements into an unmodifiable `List`.
          * **Benefit:** More concise than `collect(Collectors.toList())` when an unmodifiable list is desired, and often more performant.
          * **Example:** `List<String> upperNames = names.stream().map(String::toUpperCase).toList();`
      * **Unix-Domain Socket Channels (JEP 380):** Added support for Unix-domain (AF\_UNIX) sockets, which are used for inter-process communication on the same host, often more efficient than TCP/IP loopback connections.
      * **Elastic Metaspace (JEP 387):** Improved the management of Metaspace memory, allowing it to return unused memory to the operating system more promptly.
  * **JVM Changes:**
      * **Foreign Linker API (Incubator) (JEP 389):** Initial incubator module (part of Project Panama) for a new, safer, and more efficient way to interact with native code outside the JVM, superseding JNI.
      * **Vector API (Incubator) (JEP 338):** Initial incubator module (part of Project Panama) for expressing vector computations that reliably compile at runtime to optimal vector instructions on supported CPU architectures.
      * **Strongly Encapsulate JDK Internals by Default (JEP 396):** Changed the default behavior to strongly encapsulate internal elements of the JDK, making it harder to access internal APIs by default (only `jdk.internal.reflect` and a few others are open by default). This prepares for tighter encapsulation in future releases.
      * **Enable C++14 Language Features:** Modernized the C++ code used within the JDK to C++14 standard.

-----

# **Java 17 (Released September 2021 - LTS Release)**

Java 17 is a current Long-Term Support (LTS) release, marking a period of stability and bringing more preview features to standard status.

  * **Language Features:**
      * **Sealed Classes (Standard) (JEP 409):** This feature is now a permanent part of the Java language.
  * **Core Library Enhancements:**
      * **New `RandomGenerator` API (JEP 356):**
          * **Concept:** Provides a new interface (`java.util.random.RandomGenerator`) and implementations for pseudorandom number generators (PRNGs).
          * **Benefit:** Offers a more uniform API for PRNGs, making it easier to select and use different algorithms (e.g., LGM, Xoroshiro, PCG), and supports stream-based generation.
      * **`Stream.toList()` (JEP 392):** Officially recommended way to collect into an unmodifiable List.
  * **JVM Changes:**
      * **Restored Always-Strict Floating-Point Semantics (JEP 306):**
          * **Concept:** The JVM now consistently enforces strict floating-point computations (`strictfp`) by default across all platforms.
          * **Impact:** The `strictfp` keyword is still valid but no longer has a practical effect on the default JVM behavior (unless specific JVM flags are used to revert). This ensures consistent floating-point results across different hardware architectures.
      * **Context-Specific Deserialization Filters (JEP 415):** Improves security by allowing incoming serialization data to be filtered using JVM-wide or context-specific filters, mitigating deserialization attacks.
  * **Deprecated/Removed:**
      * **Applet API:** Fully **removed**.
      * **Security Manager (JEP 411):** Deprecated for removal in a future release. It was deemed an outdated security model.
      * **RMI Activation (JEP 407):** Formally **removed**.

-----

# **Java 18 (Released March 2022)**

Java 18 was a minor non-LTS release, focusing on maintenance and smaller quality-of-life improvements.

  * **Language Features:**
      * **Simple Web Server (JEP 408):**
          * **New Tool:** A minimal HTTP server provided as a command-line tool (`jwebserver`).
          * **Benefit:** Primarily for serving static files, rapid prototyping, and testing. It's not intended for production use.
          * **Usage:** `jwebserver -p 8000` (starts server on port 8000, serving current directory).
  * **Core Library Enhancements:**
      * **Charset for `Files.readString()`/`writeString()` (JEP 400):** Overloaded methods to specify a `Charset` when reading/writing file content to/from a `String`, providing more control over character encoding.
  * **JVM Changes:**
      * **`finalize()` Method Removed (JEP 410):** The `finalize()` method was completely **removed** from `java.lang.Object`. Its deprecation status (since Java 9) culminated in full removal. This enforces the shift to `Cleaner` for resource cleanup.
      * **Foreign Function & Memory API (Incubator - JEP 412):** A second incubator for Project Panama, continuing development of a safe and efficient foreign-function interface for interacting with native code and data outside the JVM.
      * **Vector API (Second Incubator - JEP 417).**

-----

# **Java 19 (Released September 2022)**

Java 19 was a significant non-LTS release, bringing the first previews of Project Loom's Virtual Threads and powerful Pattern Matching enhancements.

  * **Language Features:**
      * **Virtual Threads (Preview - Standardized in Java 21) (JEP 425):**
          * **Concept:** Lightweight threads managed by the JVM (part of Project Loom). They map many virtual threads to fewer underlying platform (OS) threads.
          * **Benefit:** Drastically reduces the overhead of concurrent programming, making it feasible to use a "thread-per-request" style even with millions of concurrent operations without exhausting OS resources. Improves application throughput and simplifies writing concurrent code.
          * **Usage:** Created using `Thread.ofVirtual()` or `Executors.newVirtualThreadPerTaskExecutor()`.
          * **Impact:** Revolutionizes concurrency by effectively making blocking operations cheap, reducing the need for complex asynchronous frameworks.
      * **Record Patterns (Preview - Standardized in Java 21) (JEP 405):**
          * **Concept:** Extends pattern matching to allow destructuring of record values directly in `instanceof` expressions and `switch` statements/expressions.
          * **Benefit:** Enables more concise and expressive code when extracting components from record objects, especially useful in nested data structures.
          * **Example:**
            ```java
            record Point(int x, int y) {}
            record Circle(Point center, int radius) {}

            Object shape = new Circle(new Point(10, 20), 5);
            if (shape instanceof Circle(Point center, int radius)) {
                System.out.println("Circle at (" + center.x() + ", " + center.y() + ") with radius " + radius);
            }
            ```
      * **Pattern Matching for `switch` (Preview - Standardized in Java 21) (JEP 406):**
          * **Concept:** Expands `switch` expressions and statements to allow patterns (including record patterns and type patterns) in `case` labels. Also allows `null` in `case` labels.
          * **Benefit:** Makes `switch` a much more powerful and flexible tool for type-based branching, eliminating chained `if-else if` `instanceof` checks. Ensures exhaustiveness for sealed types.
          * **Example:**
            ```java
            sealed interface Shape permits Circle, Square {}
            record Circle(Point center, int radius) implements Shape {}
            record Square(Point topLeft, int side) implements Shape {}

            Object obj = new Circle(new Point(0,0), 10);
            String description = switch (obj) {
                case Circle c -> "This is a circle with radius " + c.radius();
                case Square s -> "This is a square with side " + s.side();
                case null     -> "The object is null"; // New: handling null directly
                default       -> "Unknown shape"; // Required for exhaustiveness if not all types covered
            };
            ```
  * **Core Library Enhancements:**
      * **Foreign Function & Memory API (Second Incubator - JEP 424):** Continues development of the FFM API for interacting with native code and data.
      * **Vector API (Third Incubator - JEP 426).**

-----

# **Java 20 (Released March 2023)**

Java 20 was a non-LTS release primarily focused on refining the major preview features introduced in Java 19 and earlier.

  * **Language Features:**
      * **Virtual Threads (Second Preview) (JEP 436):** Continued refinement of virtual threads based on feedback.
      * **Record Patterns (Second Preview) (JEP 432):** Continued refinement of record patterns, including support for inference of type arguments.
      * **Pattern Matching for `switch` (Second Preview) (JEP 433):** Continued refinement of pattern matching for `switch`, including simplified syntax for `sealed` types.
  * **Core Library Enhancements:**
      * **Scoped Values (Preview) (JEP 429):**
          * **Concept:** A new mechanism to share immutable data within and across threads.
          * **Benefit:** Intended as a safer, more performant alternative to `ThreadLocal` variables, especially in the context of virtual threads, by ensuring values are passed down the call stack and are immutable.
      * **Foreign Function & Memory API (Third Incubator - JEP 434).**
      * **Vector API (Fourth Incubator - JEP 438).**

-----

# **Java 21 (Released September 2023 - LTS Release)**

Java 21 is a current Long-Term Support (LTS) release, bringing many of the highly anticipated preview features to standard status, solidifying major advancements in concurrency and language expression.

  * **Language Features:**

      * **Virtual Threads (Standard) (JEP 444):** The groundbreaking lightweight concurrency feature from Project Loom is now a permanent part of Java. Developers can confidently use virtual threads for highly scalable, blocking-style concurrent programming.
      * **Record Patterns (Standard) (JEP 440):** Now a permanent feature of the language, allowing for more powerful and expressive destructuring of record values.
      * **Pattern Matching for `switch` (Standard) (JEP 441):** This powerful feature is now permanent, allowing for highly expressive and safe type-based dispatch in `switch` expressions and statements. This also includes the ability to handle `null` cases explicitly.
      * **Sequenced Collections (JEP 431):**
          * **Concept:** New interfaces (`SequencedCollection`, `SequencedSet`, `SequencedMap`) were introduced to represent collections with a defined encounter order.
          * **Benefit:** Provides a uniform API for accessing the first/last elements, reversing the collection, and adding/removing elements at either end across various collection types (e.g., `List`, `Deque`, `LinkedHashSet`, `LinkedHashMap`).
          * **Example (for `SequencedCollection`):**
            ```java
            SequencedCollection<String> list = new LinkedList<>(List.of("A", "B", "C"));
            list.addFirst("Z"); // ["Z", "A", "B", "C"]
            list.addLast("X");  // ["Z", "A", "B", "C", "X"]
            System.out.println(list.getFirst()); // "Z"
            System.out.println(list.getLast());  // "X"
            list.reversed().forEach(System.out::println); // X, C, B, A, Z
            ```
      * **String Templates (Preview) (JEP 430):**
          * **Concept:** A new feature for easier and more readable string interpolation and dynamic string construction.
          * **Syntax:** Uses a template processor (e.g., `STR`) followed by a template literal with embedded expressions (`\{expression}`).
          * **Benefit:** Offers safer, more readable, and more expressive string formatting compared to concatenation or `String.format()`. Processors can validate and escape interpolated values.
          * **Example:**
            ```java
            String name = "World";
            String greeting = STR."Hello \{name}! Today is \{LocalDate.now()}"; // Evaluates to "Hello World! Today is 2025-07-21"
            ```
      * **Unnamed Patterns and Variables (Preview) (JEP 443):**
          * **Concept:** Allows the use of the underscore (`_`) for unnamed patterns (e.g., in record patterns or `catch` blocks where a variable is not needed) and unnamed variables.
          * **Benefit:** Improves code readability by explicitly indicating unused variables or pattern components, reducing compiler warnings and clarifying intent.
          * **Example:**
            ```java
            // Unnamed pattern in a record destructuring
            if (obj instanceof Point(int x, _)) { // Only 'x' component is needed
                System.out.println("Point's X-coordinate: " + x);
            }
            // Unnamed catch variable
            try { new FileReader("no.txt"); } catch (IOException _) { // Exception object not used
                System.err.println("File not found error occurred.");
            }
            ```
      * **Unnamed Classes and Instance Main Methods (Preview) (JEP 445):**
          * **Concept:** Aims to simplify the process of writing and running small Java programs. Allows a class to be declared without an explicit name and a `main` method to be an instance method (non-static) within that unnamed class.
          * **Benefit:** Lowers the barrier to entry for new Java learners by reducing ceremonial boilerplate, making Java more approachable for simple scripts and introductory programs.
          * **Example:**
            ```java
            // In a file named MyScript.java
            void main() { // No class declaration, no static keyword
                System.out.println("Hello from an instance main method!");
            }
            // Run directly: java MyScript.java
            ```

  * **Core Library Enhancements:**

      * **Key Encapsulation Mechanism API (JEP 439):** New API (`java.security.interfaces.XECKey`) for cryptographic key encapsulation mechanisms (KEMs), used for securely exchanging symmetric keys.
      * **Vector API (Fifth Incubator - JEP 448).**
      * **Foreign Function & Memory API (Fourth Incubator - JEP 442).**

  * **JVM Changes:**

      * **Generational ZGC (JEP 439):** Enhanced ZGC to support generational garbage collection, improving performance and reducing memory overhead.

-----

# **Java 22 (Released March 2024)**

Java 22, a non-LTS release, continued the progression of several key language features and solidified the Foreign Function & Memory API.

  * **Language Features:**
      * **String Templates (Second Preview) (JEP 459):** Continued refinement of string templates based on feedback, including more precise syntax for embedding expressions.
      * **Unnamed Variables and Patterns (Standard) (JEP 456):** The `_` for unnamed variables and patterns is now a permanent feature of the Java language.
      * **Unnamed Classes and Instance Main Methods (Second Preview) (JEP 463):** Further development of the simplified program entry points.
  * **Core Library Enhancements:**
      * **Foreign Function & Memory API (Standard) (JEP 454):**
          * **Concept:** Provides a robust, secure, and efficient way for Java programs to interoperate with native code and data outside the JVM. It effectively replaces and significantly improves upon JNI (Java Native Interface).
          * **Benefit:** Enables direct calls to native libraries, safe access to off-heap memory, and a more streamlined way to integrate with platform-specific code. Crucial for high-performance computing and system-level interactions.
      * **Vector API (Sixth Incubator - JEP 460).**
  * **Tooling:**
      * **Launch Multi-File Source-Code Programs (JEP 458):** Enhances the direct running of source code files (`java MyFile.java`) to handle programs that are spread across multiple files within the same directory or a module path.
  * **JVM Changes:**
      * **Scoped Values (Second Preview) (JEP 462):** Continued refinement for structured, immutable data sharing, building towards a more robust alternative to `ThreadLocal`.

-----

# **Java 23 (Released September 2024)**

Java 23, a non-LTS release, pushed forward on more language and API previews, laying groundwork for future LTS releases.

  * **Language Features:**

      * **String Templates (Third Preview) (JEP 466):** Continued refinement of string templates, incorporating more advanced features and feedback.
      * **Implicitly Declared Classes and Instance Main Methods (Third Preview) (JEP 463):** Further refinement of this feature for simplified program entry points, approaching finalization.
      * **Region Pinning for G1 (JEP 455):**
          * **JVM Enhancement:** Allows specific regions of the G1 garbage collector's heap to be "pinned" during native downcalls (e.g., via FFM API).
          * **Benefit:** Prevents GC from moving objects in those regions, which avoids the need to copy arrays to off-heap memory before passing them to native code, improving performance and reducing overhead for FFI.

  * **Core Library Enhancements:**

      * **Vector API (Seventh Incubator - JEP 470).**
      * **Stream Gatherers (Preview - JEP 461):**
          * **Concept:** A new `Stream.gather()` intermediate operation that enables custom, stateful intermediate operations on streams.
          * **Benefit:** Allows developers to implement complex stream transformations that are not possible with existing `map`, `filter`, `flatMap`, etc., such as grouping, windowing, or reordering elements within a stream pipeline.
      * **Class-File API (Preview - JEP 457):**
          * **Concept:** Provides a new API for parsing, generating, and transforming Java class files.
          * **Benefit:** Offers a standard, robust, and modern alternative to bytecode manipulation libraries (like ASM, ByteBuddy) for tools, frameworks, and compilers.

  * **JVM Changes:**

      * **Scoped Values (Third Preview) (JEP 474).** Continued refinement.

-----

# **Java 24 (Released March 2025)**

Java 24, the latest stable release (as of July 2025), continues the significant advancements in pattern matching, language ergonomics, and concurrency.

  * **Language Features:**
      * **String Templates (Fourth Preview) (JEP 473):** Continues to evolve, potentially very close to standardization.
      * **Implicitly Declared Classes and Instance Main Methods (Fourth Preview) (JEP 475):** Further refinement, likely the last preview before standardization in Java 25.
      * **Record Patterns with Type Arguments (JEP 465):**
          * **Enhancement:** Extends record patterns to allow explicit type arguments when destructuring generic records.
          * **Benefit:** Simplifies pattern matching for generic records, making the code clearer and more precise.
          * **Example:**
            ```java
            record Box<T>(T content) {}
            Object myBox = new Box<>("Hello");
            if (myBox instanceof Box<String>(var value)) { // Type argument explicitly given
                System.out.println("String content: " + value);
            }
            ```
      * **`super(args)` in `enum` constructors (JEP 459):**
          * **New:** Allows `super` calls in enum constructors, enabling more flexible and powerful initialization patterns for enum constants that have specialized behavior or need to pass arguments up to a common base class.
          * **Benefit:** Provides more control over enum constant construction, especially when dealing with complex enum hierarchies or shared logic.
  * **Core Library Enhancements:**
      * **Vector API (Eighth Incubator - JEP 480).**
      * **Stream Gatherers (Second Preview - JEP 476).**
      * **API for Multi-File Programs (JEP 469):** Enhancements to the API for launching multi-file source-code programs, formalizing and improving `java MyProgram.java` for multi-file projects.
  * **JVM Changes:**
      * **Scoped Values (Standard - JEP 464):** **Scoped Values are standardized\!** This feature provides a permanent and robust solution for efficiently and safely sharing immutable data across threads, especially beneficial with Virtual Threads, without the drawbacks of `ThreadLocal`.
