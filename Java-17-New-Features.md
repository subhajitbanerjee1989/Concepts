
# **Java 17 Concepts: Simple Breakdown**

Java 17 is an **LTS** (Long-Term Support) version.

  * **LTS means:** Stable. Gets updates for years. Good for big projects.
  * **Why use it?** Newer features, better performance, future-proof.

-----

## **1. Records (JEP 395)**

  * **What it is:** Super short way to make classes for **just holding data**.

  * **Why use it:**

      * **No boilerplate:** No need to write `getters`, `equals()`, `hashCode()`, `toString()`. Java does it for you.
      * **Clean DTOs:** Perfect for Data Transfer Objects.
      * **Immutable:** Data can't be changed after creation (good for safety).

  * **Keywords:** `data class`, `immutable`, `DTO`, `auto-generated`

  * **Snippet:**

    ```java
    // OLD WAY (Java 8/11): Tons of code for a simple data holder
    class ProductOld {
        private final String name;
        private final double price;
        // Constructor, getName(), getPrice(), equals(), hashCode(), toString()...
    }

    // NEW WAY (Java 17): Just one line!
    public record Product(String name, double price) {
        // Can add custom methods or validation here if needed
        public String getFormattedPrice() {
            return "$" + String.format("%.2f", price);
        }
    }

    // How to use:
    public class RecordExample {
        public static void main(String[] args) {
            Product book = new Product("Java Basics", 29.99);
            System.out.println(book.name());        // Access: book.name() instead of book.getName()
            System.out.println(book.price());
            System.out.println(book);               // Auto-generated toString: Product[name=Java Basics, price=29.99]
            System.out.println(book.getFormattedPrice()); // $29.99

            Product anotherBook = new Product("Java Basics", 29.99);
            System.out.println(book.equals(anotherBook)); // Auto-generated equals: true
        }
    }
    ```

-----

## **2. Sealed Classes (JEP 409)**

  * **What it is:** You can **control who can extend your class/interface**. It's like saying "only *these specific* classes can inherit from me."

  * **Why use it:**

      * **Fixed hierarchy:** Define a known, limited set of subtypes.
      * **Safer code:** Compiler knows all possible types, helps with `switch` statements (see next point\!).
      * **Better modeling:** Good for domain models where choices are fixed.

  * **Keywords:** `restrict inheritance`, `known subtypes`, `enum for types`, `control`

  * **Snippet:**

    ```java
    // `sealed` means only classes in `permits` list can extend
    public sealed interface Vehicle permits Car, Truck, Motorcycle {}

    // Permitted classes must specify if they are:
    // `final`: Cannot be extended further.
    // `sealed`: Can be extended, but lists its *own* permitted subtypes.
    // `non-sealed`: Can be extended by *any* class (breaks the seal).

    public final class Car implements Vehicle {}      // Car is final
    public non-sealed class Truck implements Vehicle {} // Truck can be extended by anyone
    public final class Motorcycle implements Vehicle {} // Motorcycle is final

    // Example of extending a non-sealed class
    class BigTruck extends Truck {} // OK, because Truck is `non-sealed`

    // NOT OK: You cannot extend Vehicle with a new class not in its `permits` list.
    // public class Boat implements Vehicle {} // COMPILE ERROR!

    public class SealedClassExample {
        public static void main(String[] args) {
            Vehicle myCar = new Car();
            Vehicle myTruck = new Truck();
            Vehicle myMotorcycle = new Motorcycle();
            Vehicle myBigTruck = new BigTruck();

            System.out.println(myCar.getClass().getSimpleName()); // Car
            System.out.println(myBigTruck.getClass().getSimpleName()); // BigTruck
        }
    }
    ```

-----

## **3. Pattern Matching for `instanceof` (JEP 394)**

  * **What it is:** Combines checking a type (`instanceof`) AND casting into one cleaner line.

  * **Why use it:**

      * **Less code:** No separate cast needed.
      * **Safer:** The new variable is only available if the type check passes.

  * **Keywords:** `type check`, `auto-cast`, `cleaner if`

  * **Snippet:**

    ```java
    public class PatternMatchingExample {

        public static void processShape(Object shape) {
            // OLD WAY (Java 8/11): check then cast
            if (shape instanceof String) {
                String s = (String) shape; // Need to cast
                System.out.println("Old: It's a string of length " + s.length());
            }

            System.out.println("---");

            // NEW WAY (Java 17): check AND cast together
            if (shape instanceof String s) { // `s` is the new String variable, automatically cast
                System.out.println("New: It's a string of length " + s.length());
            } else if (shape instanceof Integer i) { // `i` is the new Integer variable
                System.out.println("New: It's an integer with value " + i);
            } else {
                System.out.println("New: Unknown type: " + shape.getClass().getSimpleName());
            }
        }

        public static void main(String[] args) {
            processShape("Hello Java!");
            processShape(123);
            processShape(45.67);
        }
    }
    ```

-----

## **4. Text Blocks (JEP 378)**

  * **What it is:** Easy way to write **multi-line strings** (like SQL, JSON, HTML) without messy `\n` or `+`.

  * **Why use it:**

      * **Readability:** Code looks much cleaner.
      * **No escaping:** Fewer backslashes needed.
      * **Auto-formatting:** Handles indentation nicely.

  * **Keywords:** `multi-line string`, `"""`, `readability`, `no escaping`

  * **Snippet:**

    ```java
    public class TextBlockDemo {
        public static void main(String[] args) {
            // OLD WAY (Java 8/11): Hard to read, lots of \n
            String jsonOld = "{\n" +
                             "  \"name\": \"Alice\",\n" +
                             "  \"age\": 30\n" +
                             "}";
            System.out.println("Old JSON:\n" + jsonOld);

            System.out.println("---");

            // NEW WAY (Java 17): Much cleaner with triple quotes
            String jsonNew = """
                {
                  "name": "Bob",
                  "age": 25
                }
                """; // The closing """ can be on its own line or end of last line
            System.out.println("New JSON:\n" + jsonNew);

            // SQL example:
            String sqlQuery = """
                     SELECT id, name, email
                     FROM users
                     WHERE status = 'ACTIVE'
                       AND created_at > '2023-01-01'
                     ORDER BY name ASC;
                     """; // The compiler will remove common leading whitespace
            System.out.println("\nSQL Query:\n" + sqlQuery);
        }
    }
    ```

-----

## **5. Switch Expressions (JEP 361)**

  * **What it is:** Your `switch` statement can now **return a value** (be an expression) and uses a new `->` syntax.

  * **Why use it:**

      * **Concise:** Much shorter code.
      * **No fall-through:** The `->` syntax prevents accidental fall-through to the next case.
      * **Cleaner logic:** Can assign `switch` result directly to a variable.

  * **Keywords:** `switch as expression`, `arrow syntax`, `yield`, `no break`

  * **Snippet:**

    ```java
    public class SwitchExpressionDemo {

        public static String getDayCategory(int day) {
            // OLD WAY (Java 8/11): need `break;` and declare variable outside
            String categoryOld;
            switch (day) {
                case 1:
                case 7:
                    categoryOld = "Weekend";
                    break;
                case 2:
                case 3:
                case 4:
                case 5:
                case 6:
                    categoryOld = "Weekday";
                    break;
                default:
                    categoryOld = "Unknown";
                    break;
            }
            return categoryOld;
        }

        public static String getDayCategoryNew(int day) {
            // NEW WAY (Java 17): `switch` as an expression, `->` syntax
            return switch (day) {
                case 1, 7 -> "Weekend"; // No `break;` needed, it just returns
                case 2, 3, 4, 5, 6 -> "Weekday";
                default -> { // Can still use a block, use `yield` to return a value
                    System.out.println("Debug: Invalid day number provided: " + day);
                    yield "Unknown"; // `yield` is like `return` for the switch expression
                }
            };
        }

        public static void main(String[] args) {
            System.out.println("Day 1 old: " + getDayCategory(1));   // Weekend
            System.out.println("Day 4 old: " + getDayCategory(4));   // Weekday
            System.out.println("Day 8 old: " + getDayCategory(8));   // Unknown

            System.out.println("\nDay 1 new: " + getDayCategoryNew(1)); // Weekend
            System.out.println("Day 4 new: " + getDayCategoryNew(4)); // Weekday
            System.out.println("Day 8 new: " + getDayCategoryNew(8)); // Unknown (with debug print)
        }
    }
    ```
