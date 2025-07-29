
# **Spring Boot 3.x Core Concepts: Building Modern Applications**

Spring Boot 3.x, built on Spring Framework 6 and requiring **Java 17+**, is the current recommended major version for developing robust, production-ready, and cloud-native Java applications. It fully embraces **Jakarta EE 9+** (meaning `javax` APIs are replaced by `jakarta`). This guide covers the essential concepts you'll use daily to build applications.

-----

## **1. Core Components & Application Structure**

At its heart, a Spring Boot application is a regular Java application with a `main` method. The magic largely comes from auto-configuration and sensible defaults.

  * **`@SpringBootApplication`**:

      * This is a convenience annotation that combines:
          * `@SpringBootConfiguration`: Designates the class as a configuration class for Spring Boot itself.
          * `@EnableAutoConfiguration`: Automatically configures your Spring application based on the JARs on its classpath.
          * `@ComponentScan`: Enables component scanning on the package where the annotated class resides and its sub-packages, allowing Spring to automatically discover components (e.g., `@Component`, `@Service`, `@Repository`, `@Controller`).
      * **Best Practice:** Place your main application class (annotated with `@SpringBootApplication`) in the root package of your project. This ensures that component scanning correctly finds all your application components.

  * **Main Application Class & `SpringApplication.run()`**:

      * The entry point of your Spring Boot application.

      * The `SpringApplication.run()` method creates and initializes the Spring `ApplicationContext`, performs auto-configuration, starts embedded servers, and runs any `CommandLineRunner` or `ApplicationRunner` beans.

      * **Snippet (Basic Application Structure):**

        ```java
        // src/main/java/com/example/myapp/MyApplication.java
        package com.example.myapp;

        import org.springframework.boot.SpringApplication;
        import org.springframework.boot.autoconfigure.SpringBootApplication;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.RestController;

        @SpringBootApplication // Combines @Configuration, @EnableAutoConfiguration, @ComponentScan
        public class MyApplication {

            public static void main(String[] args) {
                SpringApplication.run(MyApplication.class, args);
            }

            // Example of a simple REST endpoint within the main app class (for demo)
            @RestController
            static class HomeController {
                @GetMapping("/")
                public String home() {
                    return "Hello from Spring Boot 3.x!";
                }
            }
        }
        ```

  * **Dependency Injection (DI) and Inversion of Control (IoC)**:

      * **IoC Container:** Spring's core principle. The container manages the lifecycle of your application's objects (known as "beans") and their dependencies. Instead of your code creating objects, Spring creates them and "injects" them where needed.
      * **`@Autowired`**: The primary annotation for DI. Spring automatically finds a suitable bean (by type, or by name if multiple candidates exist) and injects it.
      * **Constructor Injection (Recommended Best Practice)**:
          * Makes dependencies explicit and immutable. Ensures that the object is always in a valid state after construction. Also helps in easier unit testing.
          * **Snippet:**
            ```java
            // src/main/java/com/example/myapp/service/MyService.java
            package com.example.myapp.service;

            import com.example.myapp.repository.MyRepository;
            import org.springframework.stereotype.Service;

            @Service // Indicates this is a service component
            public class MyService {

                private final MyRepository myRepository; // Dependency

                // Constructor Injection: Spring will automatically inject MyRepository
                public MyService(MyRepository myRepository) {
                    this.myRepository = myRepository;
                }

                public String performServiceLogic() {
                    return "Service logic performed with: " + myRepository.getData();
                }
            }
            ```
      * **Common Stereotype Annotations**:
          * `@Component`: A generic stereotype for any Spring-managed component.
          * `@Service`: Business logic layer.
          * `@Repository`: Data access layer, often used with Spring Data JPA for data persistence exceptions.
          * `@Controller`: Traditional Spring MVC controllers (for rendering views).
          * `@RestController`: Combines `@Controller` and `@ResponseBody`, ideal for building RESTful APIs.

-----

## **2. Building RESTful Web Services**

Spring Boot, with Spring Web MVC (or WebFlux), makes building REST APIs incredibly efficient. Remember, for Spring Boot 3.x, all `javax.servlet` imports become `jakarta.servlet`.

  * **`@RestController`**:

      * Annotates a class to indicate that it's a REST controller. All methods inside this class implicitly return JSON/XML (due to `@ResponseBody`).

  * **`@RequestMapping`**:

      * Maps web requests onto specific handler methods. Can be used at class-level (base path) or method-level (specific endpoint).
      * More specific variants for HTTP methods: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`.

  * **Path Variables (`@PathVariable`)**:

      * Extracts values from the URI path.

  * **Request Parameters (`@RequestParam`)**:

      * Extracts values from the query string.

  * **Request Body (`@RequestBody`)**:

      * Maps the HTTP request body (e.g., JSON) to a Java object. Requires a JSON processing library like Jackson (included in `spring-boot-starter-web`).

  * **Response Body (`@ResponseBody`)**:

      * Indicates that the return value of a method should be bound to the web response body. (Implicit with `@RestController`).

  * **HTTP Status Codes**:

      * Use `ResponseEntity` to have full control over the response, including HTTP status codes, headers, and body.

  * **Snippet (REST Controller Example):**

    ```java
    // src/main/java/com/example/myapp/controller/ProductController.java
    package com.example.myapp.controller;

    import com.example.myapp.model.Product; // Assuming a Product record/class exists
    import com.example.myapp.service.ProductService;
    import org.springframework.http.HttpStatus;
    import org.springframework.http.ResponseEntity;
    import org.springframework.web.bind.annotation.*;
    import jakarta.validation.Valid; // Jakarta EE import for validation

    import java.util.List;
    import java.util.Optional;

    @RestController
    @RequestMapping("/api/v1/products") // Base path for all endpoints in this controller
    public class ProductController {

        private final ProductService productService;

        public ProductController(ProductService productService) {
            this.productService = productService;
        }

        // GET all products: GET /api/v1/products
        @GetMapping
        public List<Product> getAllProducts() {
            return productService.findAll();
        }

        // GET product by ID: GET /api/v1/products/{id}
        @GetMapping("/{id}")
        public ResponseEntity<Product> getProductById(@PathVariable Long id) {
            Optional<Product> product = productService.findById(id);
            return product.map(ResponseEntity::ok) // If found, return 200 OK
                          .orElseGet(() -> ResponseEntity.notFound().build()); // Else 404 Not Found
        }

        // CREATE new product: POST /api/v1/products
        @PostMapping
        @ResponseStatus(HttpStatus.CREATED) // Set HTTP 201 Created status
        public Product createProduct(@Valid @RequestBody Product product) { // @Valid for validation
            return productService.save(product);
        }

        // UPDATE existing product: PUT /api/v1/products/{id}
        @PutMapping("/{id}")
        public ResponseEntity<Product> updateProduct(@PathVariable Long id, @Valid @RequestBody Product productDetails) {
            Product updatedProduct = productService.update(id, productDetails);
            return ResponseEntity.ok(updatedProduct);
        }

        // DELETE product: DELETE /api/v1/products/{id}
        @DeleteMapping("/{id}")
        @ResponseStatus(HttpStatus.NO_CONTENT) // Set HTTP 204 No Content status
        public void deleteProduct(@PathVariable Long id) {
            productService.deleteById(id);
        }
    }
    ```

  * **Error Handling (`@ControllerAdvice`, `@ExceptionHandler`)**:

      * Provides a centralized way to handle exceptions across multiple controllers.

      * **`@ControllerAdvice`**: Marks a class that provides global exception handling, model attributes, or data binding for controllers.

      * **`@ExceptionHandler`**: Annotation to handle specific exceptions in handler methods.

      * **Snippet (Global Exception Handler):**

        ```java
        // src/main/java/com/example/myapp/exception/GlobalExceptionHandler.java
        package com.example.myapp.exception;

        import org.springframework.http.HttpStatus;
        import org.springframework.http.ResponseEntity;
        import org.springframework.web.bind.MethodArgumentNotValidException;
        import org.springframework.web.bind.annotation.ControllerAdvice;
        import org.springframework.web.bind.annotation.ExceptionHandler;

        import java.time.LocalDateTime;
        import java.util.LinkedHashMap;
        import java.util.Map;
        import java.util.stream.Collectors;

        @ControllerAdvice
        public class GlobalExceptionHandler {

            @ExceptionHandler(ProductNotFoundException.class) // Custom exception
            public ResponseEntity<Object> handleProductNotFoundException(ProductNotFoundException ex) {
                Map<String, Object> body = new LinkedHashMap<>();
                body.put("timestamp", LocalDateTime.now());
                body.put("message", ex.getMessage());
                return new ResponseEntity<>(body, HttpStatus.NOT_FOUND);
            }

            @ExceptionHandler(MethodArgumentNotValidException.class)
            public ResponseEntity<Object> handleValidationExceptions(MethodArgumentNotValidException ex) {
                Map<String, Object> body = new LinkedHashMap<>();
                body.put("timestamp", LocalDateTime.now());
                body.put("status", HttpStatus.BAD_REQUEST.value());

                // Collect all validation errors
                String errors = ex.getBindingResult()
                                  .getFieldErrors()
                                  .stream()
                                  .map(error -> error.getField() + ": " + error.getDefaultMessage())
                                  .collect(Collectors.joining(", "));
                body.put("errors", errors);

                return new ResponseEntity<>(body, HttpStatus.BAD_REQUEST);
            }

            // You can add more @ExceptionHandler methods for other types of exceptions
        }

        // Define a simple custom exception
        // src/main/java/com/example/myapp/exception/ProductNotFoundException.java
        package com.example.myapp.exception;

        import org.springframework.web.bind.annotation.ResponseStatus;
        import org.springframework.http.HttpStatus;

        @ResponseStatus(HttpStatus.NOT_FOUND) // Can also apply status directly
        public class ProductNotFoundException extends RuntimeException {
            public ProductNotFoundException(String message) {
                super(message);
            }
        }
        ```

  * **Validation (`@Valid`)**:

      * Uses Jakarta Validation (JSR 380 - Bean Validation) to validate input objects.

      * Add validation annotations (e.g., `@NotBlank`, `@Size`, `@Min`, `@Max`) to your DTOs/Entities.

      * Requires `spring-boot-starter-validation` dependency.

      * **Snippet (Product Model with Validation):**

        ```java
        // src/main/java/com/example/myapp/model/Product.java
        package com.example.myapp.model;

        import jakarta.validation.constraints.NotBlank;
        import jakarta.validation.constraints.DecimalMin;
        import jakarta.validation.constraints.Size;
        import java.math.BigDecimal; // Using BigDecimal for currency

        public record Product(
            Long id,
            @NotBlank(message = "Product name is required")
            @Size(min = 2, max = 100, message = "Name must be between 2 and 100 characters")
            String name,
            @DecimalMin(value = "0.01", message = "Price must be greater than 0")
            BigDecimal price
        ) {}
        ```

-----

## **3. Data Access with Spring Data JPA & Hibernate**

Spring Data JPA simplifies data access by significantly reducing boilerplate code for common CRUD (Create, Read, Update, Delete) operations. It builds on Jakarta Persistence API (JPA) and Hibernate (the default JPA implementation).

  * **JPA & ORM Overview**:

      * **JPA (Jakarta Persistence API):** A Java specification for managing relational data with Java objects. It defines a standard API for ORM (Object-Relational Mapping).
      * **Hibernate:** The most popular open-source JPA implementation. It takes your Java objects and maps them to database tables, handling SQL generation and execution.
      * **ORM:** A programming technique for converting data between incompatible type systems using object-oriented programming languages.

  * **Entities (`@jakarta.persistence.Entity`)**:

      * Plain Old Java Objects (POJOs) that represent tables in your database.

      * **`@Entity`**: Marks a class as a JPA entity.

      * **`@Table(name = "...")`**: (Optional) Specifies the database table name if different from the class name.

      * **`@Id`**: Marks the primary key field.

      * **`@GeneratedValue`**: Configures how the primary key is generated (e.g., `GenerationType.IDENTITY` for auto-increment).

      * **`@Column(name = "...")`**: (Optional) Maps a field to a database column if names differ.

      * **`@Transient`**: Marks a field that should *not* be persisted to the database.

      * **Snippet (Product Entity):**

        ```java
        // src/main/java/com/example/myapp/entity/Product.java
        package com.example.myapp.entity;

        import jakarta.persistence.*; // Important: use jakarta.persistence
        import java.math.BigDecimal;

        @Entity
        @Table(name = "products") // Maps to 'products' table in DB
        public class Product {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY) // Auto-increment ID
            private Long id;

            @Column(nullable = false, length = 100) // Not null, max length 100
            private String name;

            @Column(nullable = false, precision = 10, scale = 2) // Total 10 digits, 2 after decimal
            private BigDecimal price;

            // Constructors (default constructor required by JPA)
            public Product() {}

            public Product(String name, BigDecimal price) {
                this.name = name;
                this.price = price;
            }

            // Getters and Setters (Lombok can reduce boilerplate here)
            public Long getId() { return id; }
            public void setId(Long id) { this.id = id; }
            public String getName() { return name; }
            public void setName(String name) { this.name = name; }
            public BigDecimal getPrice() { return price; }
            public void setPrice(BigDecimal price) { this.price = price; }

            @Override
            public String toString() {
                return "Product{" + "id=" + id + ", name='" + name + '\'' + ", price=" + price + '}';
            }
        }
        ```

  * **Relationships (`@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`)**:

      * **`@OneToOne`**: One entity instance is associated with one other entity instance.
      * **`@OneToMany`**: One entity instance is associated with multiple other entity instances. Typically mapped with `@ManyToOne` on the other side.
      * **`@ManyToOne`**: Many entity instances are associated with one other entity instance. Often the owning side of the relationship.
      * **`@ManyToMany`**: Many entity instances are associated with many other entity instances. Usually requires a join table.
      * **Fetch Types (`FetchType.LAZY`, `FetchType.EAGER`)**:
          * `LAZY`: Data is loaded only when it's explicitly accessed. (Recommended for performance).
          * `EAGER`: Data is loaded immediately with the parent entity.

  * **Spring Data JPA Repositories**:

      * Interfaces that extend `JpaRepository` (or `CrudRepository`, `PagingAndSortingRepository`). Spring Data JPA automatically provides implementations at runtime.

      * `JpaRepository<T, ID>`: Provides comprehensive CRUD, sorting, and pagination functionalities.

      * **Benefits:** Reduces boilerplate, standardizes data access, promotes clean architecture.

      * **Snippet (Product Repository):**

        ```java
        // src/main/java/com/example/myapp/repository/ProductRepository.java
        package com.example.myapp.repository;

        import com.example.myapp.entity.Product;
        import org.springframework.data.jpa.repository.JpaRepository;
        import org.springframework.stereotype.Repository;

        import java.util.List;

        @Repository // Indicates this is a repository component
        public interface ProductRepository extends JpaRepository<Product, Long> {

            // Custom Query Method (Spring Data JPA automatically implements this based on method name)
            List<Product> findByNameContainingIgnoreCase(String name);

            // Custom Query with @Query annotation (JPQL or native SQL)
            // Example: Find products cheaper than a given price
            @jakarta.persistence.Query("SELECT p FROM Product p WHERE p.price < :maxPrice")
            List<Product> findProductsCheaperThan(@jakarta.persistence.Param("maxPrice") BigDecimal maxPrice);
            // Note: jakarta.persistence.Query and jakarta.persistence.Param are used for clarity,
            // but in Spring Data JPA, you often use org.springframework.data.jpa.repository.Query
            // which also supports the named parameters.
            // For Spring Data JPA specifically, you'd typically use:
            // @org.springframework.data.jpa.repository.Query("SELECT p FROM Product p WHERE p.price < :maxPrice")
            // List<Product> findProductsCheaperThan(@org.springframework.data.repository.query.Param("maxPrice") BigDecimal maxPrice);
        }
        ```

  * **Transaction Management (`@Transactional`)**:

      * Ensures data integrity by treating a sequence of operations as a single atomic unit. If any operation fails, the entire transaction is rolled back.

      * Applied at method or class level. Spring automatically manages transaction boundaries.

      * **`@Transactional`**: Commits the transaction if the method completes successfully, rolls back if an unchecked exception occurs.

      * **`readOnly = true`**: For read-only operations, can improve performance.

      * **Snippet (Service with Transactional):**

        ```java
        // src/main/java/com/example/myapp/service/ProductService.java
        package com.example.myapp.service;

        import com.example.myapp.entity.Product;
        import com.example.myapp.exception.ProductNotFoundException;
        import com.example.myapp.repository.ProductRepository;
        import org.springframework.stereotype.Service;
        import org.springframework.transaction.annotation.Transactional; // Spring's @Transactional

        import java.math.BigDecimal;
        import java.util.List;
        import java.util.Optional;

        @Service
        public class ProductService {

            private final ProductRepository productRepository;

            public ProductService(ProductRepository productRepository) {
                this.productRepository = productRepository;
            }

            @Transactional(readOnly = true) // This method only reads data
            public List<Product> findAll() {
                return productRepository.findAll();
            }

            @Transactional(readOnly = true)
            public Optional<Product> findById(Long id) {
                return productRepository.findById(id);
            }

            @Transactional // This method writes data
            public Product save(Product product) {
                return productRepository.save(product);
            }

            @Transactional
            public Product update(Long id, Product productDetails) {
                Product product = productRepository.findById(id)
                                .orElseThrow(() -> new ProductNotFoundException("Product not found with id: " + id));
                product.setName(productDetails.getName());
                product.setPrice(productDetails.getPrice());
                return productRepository.save(product);
            }

            @Transactional
            public void deleteById(Long id) {
                if (!productRepository.existsById(id)) {
                    throw new ProductNotFoundException("Product not found with id: " + id);
                }
                productRepository.deleteById(id);
            }

            @Transactional(readOnly = true)
            public List<Product> searchByName(String name) {
                return productRepository.findByNameContainingIgnoreCase(name);
            }

            @Transactional(readOnly = true)
            public List<Product> findCheapProducts(BigDecimal maxPrice) {
                return productRepository.findProductsCheaperThan(maxPrice);
            }
        }
        ```

  * **Database Configuration (`application.yml`)**:

      * Spring Boot provides auto-configuration for various databases. You simply need to add the driver dependency and configure the connection properties.
      * **H2 Database (In-memory, for development/testing):**
        ```xml
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        ```
        ```yaml
        # application.yml for H2
        spring:
          datasource:
            url: jdbc:h2:mem:testdb # In-memory H2 database
            driver-class-name: org.h2.Driver
            username: sa
            password:
          jpa:
            hibernate:
              ddl-auto: update # 'update' creates/updates schema; 'none' for production
            show-sql: true # Log SQL queries to console
            properties:
              hibernate.format_sql: true # Format logged SQL
          h2:
            console:
              enabled: true # Enable H2 console (http://localhost:8080/h2-console)
              path: /h2-console
        ```
      * **PostgreSQL Example:**
        ```xml
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        ```
        ```yaml
        # application.yml for PostgreSQL
        spring:
          datasource:
            url: jdbc:postgresql://localhost:5432/your_database_name
            username: your_username
            password: your_password
          jpa:
            hibernate:
              ddl-auto: validate # 'validate' checks schema, but doesn't create/update
            show-sql: true
            properties:
              hibernate.format_sql: true
        ```

-----

## **4. Configuration & Profiles**

Externalizing configuration is a key strength of Spring Boot, allowing the same application binary to run in different environments without recompilation.

  * **`application.properties` / `application.yml`**:

      * The primary locations for external configuration. YAML is often preferred for its hierarchical and readable structure.
      * **Precedence:** Properties loaded from various sources in a specific order. Command-line arguments \> Environment Variables \> `application-{profile}.yml` \> `application.yml` \> defaults.
      * **Snippet (Advanced YAML example):**
        ```yaml
        # application.yml
        app:
          name: My Awesome Product API
          version: 1.0.0
          security:
            admin-roles: ADMIN, SUPER_ADMIN
          features:
            product-search-enabled: true
            discount-service-url: http://localhost:9000/discounts # Default URL

        server:
          port: 8080

        logging:
          level:
            com.example.myapp: INFO # Specific package logging level
            org.springframework.web: DEBUG # Spring web logging for development

        --- # YAML document separator for profiles

        spring:
          config:
            activate:
              on-profile: dev # Profile for development environment

        server:
          port: 8081 # Dev server on different port

        spring:
          datasource:
            url: jdbc:h2:mem:devdb
            username: sa
            password:
          jpa:
            hibernate:
              ddl-auto: create-drop # Create and drop schema on each run for dev

        app:
          features:
            discount-service-url: http://dev-discount-service:9000/discounts

        ---

        spring:
          config:
            activate:
              on-profile: prod # Profile for production environment

        server:
          port: 443 # Default HTTPS port

        spring:
          datasource:
            url: jdbc:postgresql://prod-db.example.com:5432/prod_app_db
            username: prod_user
            password: ${DB_PASSWORD} # Best practice: use environment variables for sensitive data
          jpa:
            hibernate:
              ddl-auto: none # Never auto-alter schema in production

        app:
          features:
            discount-service-url: https://prod-discount-service.example.com/discounts
            product-search-enabled: false # Disable search feature in prod for example
        ```

  * **`@ConfigurationProperties`**:

      * Provides type-safe configuration by binding properties from `application.yml` (or `.properties`) to a Java POJO.

      * **Benefits:** Compile-time checking, IDE auto-completion, easy validation.

      * **Snippet (Custom Configuration Properties):**

        ```java
        // src/main/java/com/example/myapp/config/AppProperties.java
        package com.example.myapp.config;

        import org.springframework.boot.context.properties.ConfigurationProperties;
        import org.springframework.context.annotation.Configuration;
        import jakarta.validation.constraints.NotBlank;
        import jakarta.validation.constraints.NotNull;
        import java.util.List;
        import java.net.URL; // For URL type binding

        @Configuration
        @ConfigurationProperties(prefix = "app") // Binds properties starting with 'app.'
        // Note: For records or immutable classes, in Spring Boot 3, @ConstructorBinding is often inferred,
        // but can be explicitly added if multiple constructors or complex scenarios.
        public class AppProperties {

            @NotBlank
            private String name;
            private String version;
            private Security security = new Security(); // Nested object
            private Features features = new Features();

            // Getters and Setters (or use Lombok @Data)
            public String getName() { return name; }
            public void setName(String name) { this.name = name; }
            public String getVersion() { return version; }
            public void setVersion(String version) { this.version = version; }
            public Security getSecurity() { return security; }
            public void setSecurity(Security security) { this.security = security; }
            public Features getFeatures() { return features; }
            public void setFeatures(Features features) { this.features = features; }

            public static class Security {
                private List<String> adminRoles; // List binding
                public List<String> getAdminRoles() { return adminRoles; }
                public void setAdminRoles(List<String> adminRoles) { this.adminRoles = adminRoles; }
            }

            public static class Features {
                @NotNull
                private Boolean productSearchEnabled; // Boolean binding
                private URL discountServiceUrl; // URL type binding

                public Boolean getProductSearchEnabled() { return productSearchEnabled; }
                public void setProductSearchEnabled(Boolean productSearchEnabled) { this.productSearchEnabled = productSearchEnabled; }
                public URL getDiscountServiceUrl() { return discountServiceUrl; }
                public void setDiscountServiceUrl(URL discountServiceUrl) { this.discountServiceUrl = discountServiceUrl; }
            }
        }
        ```

        ```java
        // Example of using AppProperties in a service
        package com.example.myapp.service;

        import com.example.myapp.config.AppProperties;
        import org.springframework.stereotype.Service;

        @Service
        public class MyConfigService {

            private final AppProperties appProperties;

            public MyConfigService(AppProperties appProperties) {
                this.appProperties = appProperties;
            }

            public void displayConfig() {
                System.out.println("App Name: " + appProperties.getName());
                System.out.println("App Version: " + appProperties.getVersion());
                System.out.println("Admin Roles: " + appProperties.getSecurity().getAdminRoles());
                System.out.println("Product Search Enabled: " + appProperties.getFeatures().getProductSearchEnabled());
                System.out.println("Discount Service URL: " + appProperties.getFeatures().getDiscountServiceUrl());
            }
        }
        ```

-----

## **5. Testing Spring Boot Applications**

Spring Boot provides excellent testing support with dedicated annotations and utilities to write effective unit, integration, and slice tests.

  * **`@SpringBootTest`**:

      * Loads the full Spring `ApplicationContext`. Ideal for integration tests where you need to test the entire application stack.

      * `webEnvironment` attribute:

          * `MOCK` (default): Provides a mock servlet environment (useful with `MockMvc`).
          * `RANDOM_PORT`: Starts a real server on a random port (useful with `TestRestTemplate` or `WebTestClient`).

      * **Snippet (Full Integration Test):**

        ```java
        // src/test/java/com/example/myapp/controller/ProductControllerIntegrationTest.java
        package com.example.myapp.controller;

        import com.example.myapp.MyApplication;
        import com.example.myapp.entity.Product;
        import com.example.myapp.repository.ProductRepository;
        import org.junit.jupiter.api.BeforeEach;
        import org.junit.jupiter.api.Test;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
        import org.springframework.boot.test.context.SpringBootTest;
        import org.springframework.http.MediaType;
        import org.springframework.test.context.ActiveProfiles;
        import org.springframework.test.web.servlet.MockMvc;
        import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
        import org.springframework.transaction.annotation.Transactional; // For rolling back tests

        import com.fasterxml.jackson.databind.ObjectMapper; // For JSON conversion
        import java.math.BigDecimal;

        import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
        import static org.hamcrest.Matchers.*;

        @SpringBootTest(classes = MyApplication.class, webEnvironment = SpringBootTest.WebEnvironment.MOCK)
        @AutoConfigureMockMvc // Configures MockMvc
        @ActiveProfiles("test") // Activates a 'test' profile for specific test configs (e.g., H2 DB)
        @Transactional // Rolls back transaction after each test method
        public class ProductControllerIntegrationTest {

            @Autowired
            private MockMvc mockMvc; // Used to simulate HTTP requests

            @Autowired
            private ObjectMapper objectMapper; // To convert Java objects to JSON and vice-versa

            @Autowired
            private ProductRepository productRepository;

            private Product existingProduct;

            @BeforeEach
            void setUp() {
                // Clear and set up fresh data for each test to ensure isolation
                productRepository.deleteAll();
                existingProduct = productRepository.save(new Product("Test Product", BigDecimal.valueOf(10.00)));
            }

            @Test
            void testGetAllProducts() throws Exception {
                mockMvc.perform(MockMvcRequestBuilders.get("/api/v1/products")
                                .contentType(MediaType.APPLICATION_JSON))
                        .andExpect(status().isOk())
                        .andExpect(jsonPath("$", hasSize(greaterThanOrEqualTo(1))))
                        .andExpect(jsonPath("$[0].name", is("Test Product")));
            }

            @Test
            void testCreateProduct() throws Exception {
                Product newProduct = new Product("New Gadget", BigDecimal.valueOf(99.99));
                mockMvc.perform(MockMvcRequestBuilders.post("/api/v1/products")
                                .contentType(MediaType.APPLICATION_JSON)
                                .content(objectMapper.writeValueAsString(newProduct)))
                        .andExpect(status().isCreated())
                        .andExpect(jsonPath("$.name", is("New Gadget")));
            }

            @Test
            void testGetProductByIdNotFound() throws Exception {
                mockMvc.perform(MockMvcRequestBuilders.get("/api/v1/products/999")
                                .contentType(MediaType.APPLICATION_JSON))
                        .andExpect(status().isNotFound());
            }
        }
        ```

  * **`@WebMvcTest` (for Controllers)**:

      * Focuses on testing the web layer (controllers). It auto-configures Spring MVC components (like `DispatcherServlet`, `Controller`, `Filter`, `WebMvcConfigurer`) but does **not** load other components (e.g., `@Service`, `@Repository`).

      * Typically used with `@MockBean` to mock service/repository layers.

      * **Snippet (Controller Unit/Slice Test):**

        ```java
        // src/test/java/com/example/myapp/controller/ProductControllerSliceTest.java
        package com.example.myapp.controller;

        import com.example.myapp.entity.Product;
        import com.example.myapp.service.ProductService;
        import com.fasterxml.jackson.databind.ObjectMapper;
        import org.junit.jupiter.api.Test;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
        import org.springframework.boot.test.mock.mockito.MockBean;
        import org.springframework.http.MediaType;
        import org.springframework.test.web.servlet.MockMvc;

        import java.math.BigDecimal;
        import java.util.Optional;

        import static org.mockito.ArgumentMatchers.any;
        import static org.mockito.ArgumentMatchers.anyLong;
        import static org.mockito.Mockito.when;
        import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
        import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

        @WebMvcTest(ProductController.class) // Focus only on ProductController
        public class ProductControllerSliceTest {

            @Autowired
            private MockMvc mockMvc;

            @Autowired
            private ObjectMapper objectMapper;

            @MockBean // Mocks the ProductService, so it won't hit the real service/DB
            private ProductService productService;

            @Test
            void testGetProductById() throws Exception {
                Product mockProduct = new Product(1L, "Laptop", BigDecimal.valueOf(1200.00));
                when(productService.findById(anyLong())).thenReturn(Optional.of(mockProduct));

                mockMvc.perform(get("/api/v1/products/1")
                                .contentType(MediaType.APPLICATION_JSON))
                        .andExpect(status().isOk())
                        .andExpect(jsonPath("$.id").value(1L))
                        .andExpect(jsonPath("$.name").value("Laptop"));
            }

            @Test
            void testCreateProductInvalidInput() throws Exception {
                // Product with blank name, triggering @NotBlank validation
                Product invalidProduct = new Product(null, "", BigDecimal.valueOf(10.00));

                mockMvc.perform(post("/api/v1/products")
                                .contentType(MediaType.APPLICATION_JSON)
                                .content(objectMapper.writeValueAsString(invalidProduct)))
                        .andExpect(status().isBadRequest())
                        .andExpect(jsonPath("$.errors", containsString("Product name is required")));
            }
        }
        ```

  * **`@DataJpaTest` (for Repositories)**:

      * Focuses on testing JPA components. It configures an in-memory database (like H2), Spring Data JPA repositories, and the JPA entity manager.

      * **Does not** load the full application context or web layer.

      * Used with `TestEntityManager` to set up and query test data.

      * **Snippet (Repository Test):**

        ```java
        // src/test/java/com/example/myapp/repository/ProductRepositoryTest.java
        package com.example.myapp.repository;

        import com.example.myapp.entity.Product;
        import org.junit.jupiter.api.Test;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
        import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;

        import java.math.BigDecimal;
        import java.util.List;
        import java.util.Optional;

        import static org.assertj.core.api.Assertions.assertThat;

        @DataJpaTest // Configures H2, JPA, and repositories
        public class ProductRepositoryTest {

            @Autowired
            private TestEntityManager entityManager; // For direct interaction with persistence context for test data setup

            @Autowired
            private ProductRepository productRepository;

            @Test
            void testFindByProductName() {
                // Setup test data
                Product product1 = new Product("Smartphone", BigDecimal.valueOf(500.00));
                entityManager.persist(product1);
                Product product2 = new Product("Smartwatch", BigDecimal.valueOf(250.00));
                entityManager.persist(product2);
                entityManager.flush(); // Flush changes to DB

                // Test the repository method
                List<Product> foundProducts = productRepository.findByNameContainingIgnoreCase("smart");

                // Assertions
                assertThat(foundProducts).hasSize(2);
                assertThat(foundProducts).extracting(Product::getName)
                                         .containsExactlyInAnyOrder("Smartphone", "Smartwatch");
            }

            @Test
            void testSaveProduct() {
                Product newProduct = new Product("Tablet", BigDecimal.valueOf(300.00));
                Product savedProduct = productRepository.save(newProduct);

                assertThat(savedProduct).isNotNull();
                assertThat(savedProduct.getId()).isNotNull();
                assertThat(savedProduct.getName()).isEqualTo("Tablet");

                // Verify by finding it from the database
                Optional<Product> found = productRepository.findById(savedProduct.getId());
                assertThat(found).isPresent();
                assertThat(found.get().getName()).isEqualTo("Tablet");
            }
        }
        ```

-----

## **6. Spring Security (Basic Concepts)**

Spring Security is a powerful and highly customizable authentication and access-control framework. In Spring Boot, it's easily integrated.

  * **Dependency (`spring-boot-starter-security`)**: Adding this automatically enables basic security.
  * **Auto-Configuration**: By default, it sets up form-based login and basic HTTP authentication for all endpoints, along with a default user (`user`) and a randomly generated password (printed to console on startup).
  * **Customizing Security**:
      * Extend `WebSecurityConfigurerAdapter` (Spring Boot 2.x) or use a `SecurityFilterChain` bean (Spring Boot 3.x).

      * **Method-Level Security (`@EnableMethodSecurity`, `@PreAuthorize`, `@PostAuthorize`)**: Apply security rules directly to service methods.

      * **Snippet (Basic Custom Security Configuration - Spring Boot 3.x style):**

        ```java
        // src/main/java/com/example/myapp/config/SecurityConfig.java
        package com.example.myapp.config;

        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;
        import org.springframework.security.config.annotation.web.builders.HttpSecurity;
        import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
        import org.springframework.security.core.userdetails.User;
        import org.springframework.security.core.userdetails.UserDetails;
        import org.springframework.security.core.userdetails.UserDetailsService;
        import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
        import org.springframework.security.crypto.password.PasswordEncoder;
        import org.springframework.security.provisioning.InMemoryUserDetailsManager;
        import org.springframework.security.web.SecurityFilterChain;

        @Configuration
        @EnableWebSecurity // Enables Spring Security's web security support
        @EnableMethodSecurity // Enables method-level security (e.g., @PreAuthorize)
        public class SecurityConfig {

            // Define custom security filter chain for HTTP requests
            @Bean
            public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
                http
                    .csrf(csrf -> csrf.disable()) // Disable CSRF for simpler REST APIs (consider for production)
                    .authorizeHttpRequests(authorize -> authorize
                        .requestMatchers("/api/v1/products/**").hasRole("USER") // Require USER role for /api/v1/products
                        .requestMatchers("/actuator/**").permitAll() // Allow public access to actuator health
                        .anyRequest().authenticated() // All other requests require authentication
                    )
                    .httpBasic(org.springframework.security.config.Customizer.withDefaults()) // Enable HTTP Basic Auth
                    .formLogin(org.springframework.security.config.Customizer.withDefaults()); // Enable form-based login

                return http.build();
            }

            // In-memory user store for demonstration (NOT for production)
            @Bean
            public UserDetailsService userDetailsService(PasswordEncoder passwordEncoder) {
                UserDetails user = User.withUsername("user")
                        .password(passwordEncoder.encode("password"))
                        .roles("USER")
                        .build();
                UserDetails admin = User.withUsername("admin")
                        .password(passwordEncoder.encode("adminpass"))
                        .roles("ADMIN", "USER") // Admin has both roles
                        .build();
                return new InMemoryUserDetailsManager(user, admin);
            }

            // Password Encoder (essential for storing passwords securely)
            @Bean
            public PasswordEncoder passwordEncoder() {
                return new BCryptPasswordEncoder();
            }
        }
        ```

      * **Snippet (Method-Level Security):**

        ```java
        // In ProductService or similar service
        // ...
        import org.springframework.security.access.prepost.PreAuthorize;

        @Service
        public class ProductService {
            // ... constructor and other methods ...

            @PreAuthorize("hasRole('ADMIN')") // Only users with 'ADMIN' role can delete
            @Transactional
            public void deleteById(Long id) {
                if (!productRepository.existsById(id)) {
                    throw new ProductNotFoundException("Product not found with id: " + id);
                }
                productRepository.deleteById(id);
            }

            @PreAuthorize("hasAnyRole('ADMIN', 'USER')") // Both ADMIN and USER can find all
            @Transactional(readOnly = true)
            public List<Product> findAll() {
                return productRepository.findAll();
            }
        }
        ```

-----

## **7. Actuator (Monitoring & Management)**

Spring Boot Actuator provides production-ready features to monitor and manage your application. In Spring Boot 3.x, `/actuator/health` is the only endpoint exposed by default for security.

  * **Dependency (`spring-boot-starter-actuator`)**:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    ```

  * **Key Endpoints (Default URL: `http://localhost:8080/actuator/`):**

      * `/actuator`: Discover all exposed endpoints.
      * `/actuator/health`: Shows application health information (UP/DOWN, detailed status with `management.endpoint.health.show-details=always`).
      * `/actuator/info`: Displays arbitrary application information.
      * `/actuator/metrics`: Shows metrics about JVM, garbage collection, web requests, etc. Can drill down (e.g., `/actuator/metrics/jvm.memory.used`).
      * `/actuator/env`: Displays environment properties.
      * `/actuator/beans`: Lists all Spring beans.
      * `/actuator/mappings`: Shows all `@RequestMapping` paths.
      * `/actuator/loggers`: View and modify logging levels at runtime.
      * `/actuator/threaddump`: Performs a thread dump.
      * `/actuator/heapdump`: Downloads a JVM heap dump (useful for memory analysis, often restricted).
      * `/actuator/prometheus`: Exposes metrics in a format consumable by Prometheus (requires `micrometer-registry-prometheus` dependency).

  * **Configuration (`application.yml`)**:

    ```yaml
    management:
      endpoints:
        web:
          exposure:
            include: "health,info,metrics" # Expose specific endpoints
            # include: "*" # Expose all endpoints (NOT recommended for production without security)
          base-path: /mgmt # Change default base path from /actuator to /mgmt
      endpoint:
        health:
          show-details: always # Show full details for /actuator/health
        info:
          enabled: true # Enable info endpoint (it's disabled by default in SB3.x)
        env:
          show-values: never # Prevent sensitive info from showing
          # show-values: always # Show all values (use with extreme caution in prod)
    info: # Custom info properties, accessible via /actuator/info
      app:
        name: ${spring.application.name}
        description: Product Management API
        version: @project.version@ # Populated from Maven/Gradle pom.xml/build.gradle
        contact: support@example.com
    ```

      * **Illustration (Actuator Workflow):**
        ```
        +-----------------------+
        | Spring Boot App       |
        | (Running in Prod)     |
        |                       |
        | +-------------------+ |
        | | Actuator Endpoints| |
        | | - /actuator/health| | <--- Monitoring Tools (Prometheus, Grafana)
        | | - /actuator/info  | | <--- DevOps/Operators (Curl, Browser)
        | | - /actuator/metrics| | <--- Alerts, Dashboards
        | +-------------------+ |
        +-----------------------+
                |
                | (Secure Access - Firewall/Spring Security)
                V
        External Monitoring System / Human Operator
        ```

-----

## **8. Logging**

Spring Boot uses Logback by default, providing robust and flexible logging.

  * **Default Behavior**: Logs to console. Provides sensible defaults based on the `spring-boot-starter-logging` dependency (which pulls in Logback).
  * **Configuring Levels (`application.yml`)**:
      * Set logging levels for different packages or the root logger.
      * **Levels:** `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`, `OFF`.
      * **Snippet:**
        ```yaml
        logging:
          level:
            root: INFO # Default level for all loggers
            com.example.myapp: DEBUG # Turn on DEBUG for your application package
            org.springframework.web: INFO # Spring web framework logging
            org.hibernate.SQL: DEBUG # Show SQL queries from Hibernate
            org.hibernate.type.descriptor.sql.BasicBinder: TRACE # Show SQL parameters
          file:
            name: logs/myapp.log # Log to a file (relative path)
            max-size: 10MB # Max file size before rolling
            max-history: 7 # Keep 7 rolled files
          pattern:
            console: "%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"
            file: "%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"
        ```
  * **Using Loggers in Code**:
      * Use `org.slf4j.Logger` and `org.slf4j.LoggerFactory`.
      * **Snippet:**
        ```java
        // In any class where you need logging
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import org.springframework.stereotype.Service;

        @Service
        public class SomeService {

            private static final Logger log = LoggerFactory.getLogger(SomeService.class);

            public void doSomething() {
                log.trace("A TRACE message");
                log.debug("A DEBUG message");
                log.info("An INFO message");
                log.warn("A WARN message");
                log.error("An ERROR message");
            }
        }
        ```
