# Java Cheatsheet

Comprehensive end-to-end reference for Java from JVM internals, ClassLoader, memory regions, and JIT compilation to modern Java 17-21+ features (Records, Sealed Classes, Virtual Threads), Spring Boot 3 architecture, Collections, Streams, and concurrency.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 JVM Architecture, JIT Compilation, and Memory Layout](#1-jvm-architecture-jit-compilation-and-memory-layout)
- [2 Build Systems, Toolchains, and Project Structure](#2-build-systems-toolchains-and-project-structure)
- [3 Language Basics, Data Types, and Memory Behavior](#3-language-basics-data-types-and-memory-behavior)
- [4 Strings, String Pool, and Text Manipulation](#4-strings-string-pool-and-text-manipulation)
- [5 Control Flow and Modern Pattern Matching](#5-control-flow-and-modern-pattern-matching)
- [6 Object-Oriented Programming (OOP) and Access Modifiers](#6-object-oriented-programming-oop-and-access-modifiers)
- [7 Interfaces, Sealed Classes, and Records](#7-interfaces-sealed-classes-and-records)
- [8 Generics and Type Erasure](#8-generics-and-type-erasure)
- [9 Java Collections Framework (JCF) Exhaustive Guide](#9-java-collections-framework-jcf-exhaustive-guide)
- [10 Functional Programming, Lambdas, and Streams API](#10-functional-programming-lambdas-and-streams-api)
- [11 Optional and Null Safety](#11-optional-and-null-safety)
- [12 Exception Handling Architecture](#12-exception-handling-architecture)
- [13 Concurrency, Thread Synchronization, and JMM](#13-concurrency-thread-synchronization-and-jmm)
- [14 Virtual Threads and Project Loom (Java 21+)](#14-virtual-threads-and-project-loom-java-21)
- [15 Annotations and Reflection API](#15-annotations-and-reflection-api)
- [16 Production Web Backends (Spring Boot 3.x Architecture)](#16-production-web-backends-spring-boot-3x-architecture)
- [17 File I/O, Serialization, and Network HTTP Client](#17-file-io-serialization-and-network-http-client)
- [18 Testing, Benchmarking, and Tooling](#18-testing-benchmarking-and-tooling)
- [19 High-Yield Interview Questions and Reality Check](#19-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in Java interviews and enterprise systems engineering:
1. **JVM Memory Layout (Heap, Stack, Metaspace) & Garbage Collectors (G1, ZGC)**
2. **`HashMap` Internal Architecture (Buckets, Hash Collisions, Treeification to Red-Black Tree)**
3. **`equals()` and `hashCode()` Contract & Integer Cache (-128 to 127)**
4. **String Constant Pool, Immutability & `String` vs `StringBuilder` vs `StringBuffer`**
5. **Java Memory Model (JMM), `volatile` Keyword vs `synchronized`**
6. **Virtual Threads (Project Loom - Java 21) vs OS Platform Threads**
7. **Generics Type Erasure & PECS Rule (`Producer Extends, Consumer Super`)**
8. **Modern Java Features: Records (Java 16), Sealed Classes (Java 17), Pattern Matching (Java 21)**
9. **Streams API Pipelines, Collectors & Lazy Evaluation**
10. **Spring Boot 3.x Layered Architecture, Dependency Injection & `@Transactional`**

---

## 1 JVM Architecture, JIT Compilation, and Memory Layout

### JVM Subsystems Overview
```
Java Source (.java) ──► [javac] ──► Bytecode (.class)
                                            │
┌───────────────────────────────────────────▼────────────────────────────────────────┐
│                               JVM RUNTIME ENGINE                                   │
│                                                                                    │
│ 1. ClassLoader Subsystem (Loading ──► Linking ──► Initialization)                  │
│                                                                                    │
│ 2. Runtime Data Areas:                                                             │
│    ├── Heap Memory (Young Gen [Eden, S0, S1] + Old Gen [Tenured]) [Shared]         │
│    ├── Metaspace (Native memory: Class metadata, Static variables) [Shared]        │
│    ├── JVM Stack (Stack Frames per Thread: Local Vars, Operand Stack) [Per Thread] │
│    ├── Program Counter (PC) Registers [Per Thread]                                 │
│    └── Native Method Stacks [Per Thread]                                           │
│                                                                                    │
│ 3. Execution Engine:                                                               │
│    ├── Interpreter (Executes bytecode line-by-line)                                │
│    ├── JIT Compiler (Tiered Compilation: C1 Client + C2 Server Optimizing)         │
│    └── Garbage Collector (Serial, Parallel, G1GC, ZGC, Shenandoah)                │
└────────────────────────────────────────────────────────────────────────────────────┘
```

### JVM Memory Regions Breakdown
| Memory Region | What it Stores | Scope | Managed By |
| :--- | :--- | :--- | :--- |
| **Heap** | All Class Instances, Objects, Arrays, String Pool (Java 7+) | Shared across all threads | **Garbage Collector** |
| **Stack** | Primitive local variables, reference pointers, call frames | Per Thread | Automatic on frame pop |
| **Metaspace** | Class definitions, method bytecodes, runtime constant pool | Shared (Native RAM) | OS / JVM ClassUnloading |
| **PC Register** | Address of currently executing JVM instruction | Per Thread | CPU / Execution Engine |

### Garbage Collection Generations in HotSpot JVM
- **Young Generation**:
  - **Eden Space**: Newly allocated objects land here.
  - **Survivor Spaces (`S0` / `S1` or From/To)**: Objects that survive Minor GC are copied back and forth with incremented age tenure.
- **Old (Tenured) Generation**: Objects surviving age threshold (default `15` in HotSpot) promoted to Old Gen. Collected via Major / Full GC.
- **Modern Collectors**:
  - **G1 GC (Default)**: Divides heap into equal regions; prioritizes regions with the most garbage.
  - **ZGC / Shenandoah**: Ultra-low latency collectors (< 1ms pause time) supporting multi-terabyte heaps concurrently.

---

## 2 Build Systems, Toolchains, and Project Structure

### Maven `pom.xml` Standard
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.company</groupId>
    <artifactId>core-service</artifactId>
    <version>1.0.0</version>

    <properties>
        <java.version>21</java.version>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <spring.boot.version>3.2.3</spring.boot.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
```

### Essential CLI Commands
```bash
# Compile and package
mvn clean package
mvn test

# Run executable JAR
java -jar -Xms512m -Xmx2048m -XX:+UseG1GC target/core-service-1.0.0.jar

# Inspect bytecode disassembly
javap -c -v target/classes/com/company/Application.class
```

---

## 3 Language Basics, Data Types, and Memory Behavior

### The 8 Primitive Types
| Primitive | Size | Range / Values | Default Value | Wrapper Class |
| :--- | :--- | :--- | :--- | :--- |
| `byte` | 1 byte (8-bit) | -128 to 127 | `0` | `Byte` |
| `short` | 2 bytes (16-bit) | -32,768 to 32,767 | `0` | `Short` |
| `int` | 4 bytes (32-bit) | -2^31 to 2^31 - 1 | `0` | `Integer` |
| `long` | 8 bytes (64-bit) | -2^63 to 2^63 - 1 (`100L`) | `0L` | `Long` |
| `float` | 4 bytes (32-bit) | IEEE-754 Single (`3.14f`) | `0.0f` | `Float` |
| `double` | 8 bytes (64-bit) | IEEE-754 Double (`3.14d`) | `0.0d` | `Double` |
| `boolean` | 1 bit / 1 byte | `true` or `false` | `false` | `Boolean` |
| `char` | 2 bytes (16-bit) | Unicode `\u0000` to `\uffff` | `\u0000` | `Character` |

### Autoboxing & The Integer Cache (-128 to 127)
Java caches `Integer` wrapper objects for values between `-128` and `127`.

```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b); // true (Referencing same cached object)

Integer x = 128;
Integer y = 128;
System.out.println(x == y);      // false (Different heap object instances!)
System.out.println(x.equals(y)); // true (Value equality)
```

### Local Variable Type Inference (`var` - Java 10+)
```java
var message = "Hello, Java 21";           // Inferred as String
var userMap = new HashMap<Long, String>(); // Inferred as HashMap<Long, String>
// Note: 'var' cannot be used for class fields, method parameters, or return types!
```

---

## 4 Strings, String Pool, and Text Manipulation

### `String` vs `StringBuilder` vs `StringBuffer`
| Class | Immutability | Thread Safety | Speed | Common Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **`String`** | **Immutable** | Thread-Safe | Slower on heavy concatenations | Identifiers, DTO keys, map keys |
| **`StringBuilder`** | **Mutable** | **Not Thread-Safe** | **Fastest** (No sync locks) | Single-threaded string construction / loops |
| **`StringBuffer`** | **Mutable** | Thread-Safe (`synchronized`) | Slower | Legacy multi-threaded concatenation |

### String Constant Pool & Memory Layout
```java
// 1. Literal: Placed in String Constant Pool (Heap)
String s1 = "Java";
String s2 = "Java";
System.out.println(s1 == s2); // true (Shared pool reference)

// 2. Explicit new keyword: Allocates fresh heap object outside pool
String s3 = new String("Java");
System.out.println(s1 == s3);         // false
System.out.println(s1 == s3.intern()); // true (.intern() forces reference to pool)
```

### Text Blocks (Java 15+)
```java
String query = """
    SELECT u.id, u.username, u.email
    FROM users u
    WHERE u.active = true
    ORDER BY u.created_at DESC
    """;
```

---

## 5 Control Flow and Modern Pattern Matching

### Modern `switch` Expressions (Java 14+)
```java
public String getDayType(DayOfWeek day) {
    return switch (day) {
        case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> "Weekday";
        case SATURDAY, SUNDAY -> {
            System.out.println("Enjoy the weekend!");
            yield "Weekend"; // yield returns value from block
        }
    };
}
```

### Pattern Matching for `instanceof` (Java 16+)
```java
// Old way: if (obj instanceof String) { String s = (String) obj; ... }
// Modern way: Type check and variable binding in one step
if (obj instanceof String s && s.length() > 5) {
    System.out.println(s.toUpperCase());
}
```

### Pattern Matching for `switch` & Record Deconstruction (Java 21+)
```java
public record Point(int x, int y) {}
public record Circle(Point center, int radius) {}

public void describeShape(Object shape) {
    switch (shape) {
        case Point(int x, int y) when x == y ->
            System.out.println("Point on diagonal: " + x);
        case Point(int x, int y) ->
            System.out.println("Point at: " + x + ", " + y);
        case Circle(Point(int x, int y), int r) ->
            System.out.println("Circle centered at (" + x + "," + y + ") with radius " + r);
        case null ->
            System.out.println("Null shape");
        default ->
            System.out.println("Unknown object: " + shape);
    }
}
```

---

## 6 Object-Oriented Programming (OOP) and Access Modifiers

### Access Modifiers Matrix
| Modifier | Same Class | Same Package | Subclass (Different Pkg) | World (Everywhere) |
| :--- | :--- | :--- | :--- | :--- |
| `private` | **Yes** | No | No | No |
| *(default / package-private)* | **Yes** | **Yes** | No | No |
| `protected` | **Yes** | **Yes** | **Yes** | No |
| `public` | **Yes** | **Yes** | **Yes** | **Yes** |

### The 4 Pillars in Java
```java
// 1. Abstraction & Encapsulation
public abstract class BaseAccount {
    private double balance; // Encapsulated private field

    public BaseAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public double getBalance() { return balance; }

    protected void updateBalance(double amount) {
        this.balance += amount;
    }

    public abstract void applyMonthlyFee(); // Abstract method contract
}

// 2. Inheritance & Polymorphism
public class SavingsAccount extends BaseAccount {
    private final double interestRate;

    public SavingsAccount(double initialBalance, double interestRate) {
        super(initialBalance); // Constructor chaining
        this.interestRate = interestRate;
    }

    @Override
    public void applyMonthlyFee() {
        updateBalance(getBalance() * (interestRate / 100));
    }
}
```

---

## 7 Interfaces, Sealed Classes, and Records

### Modern Interface Features
```java
public interface PaymentGateway {
    // 1. Standard Abstract Method
    boolean processPayment(double amount);

    // 2. Default Method (Java 8 - provides default implementation)
    default void logTransaction(String txId) {
        logHelper("Transaction logged: " + txId);
    }

    // 3. Static Utility Method (Java 8)
    static PaymentGateway createDefault() {
        return new StripeGateway();
    }

    // 4. Private Helper Method (Java 9)
    private void logHelper(String message) {
        System.out.println("[AUDIT] " + message);
    }
}
```

### Sealed Classes & Interfaces (Java 17+)
Restricts which subclasses can extend/implement the type, enabling exhaustive compile-time checks.

```java
public sealed interface HttpResponse permits SuccessResponse, ErrorResponse, RedirectResponse {}

public final class SuccessResponse implements HttpResponse { public String data = "OK"; }
public final class ErrorResponse implements HttpResponse { public int errorCode = 500; }
public non-sealed class RedirectResponse implements HttpResponse {} // Allows further subclassing
```

### Records (Java 16+ - Immutable Data Carriers)
Automatically creates `final` fields, canonical constructor, getters (`id()`, `username()`), `equals()`, `hashCode()`, and `toString()`.

```java
public record UserDto(
    Long id,
    String username,
    String email
) {
    // Compact constructor for validation
    public UserDto {
        if (username == null || username.isBlank()) {
            throw new IllegalArgumentException("Username cannot be blank");
        }
    }
}
```

---

## 8 Generics and Type Erasure

### The PECS Principle (Producer Extends, Consumer Super)
- **`? extends T` (Upper Bound / Covariance)**: Use when you only **READ (produce)** data from a collection.
- **`? super T` (Lower Bound / Contravariance)**: Use when you only **WRITE (consume)** data into a collection.

```java
import java.util.List;

public class CollectionsHelper {
    // Producer Extends: Reading numbers
    public static double sumOfList(List<? extends Number> list) {
        double sum = 0.0;
        for (Number n : list) {
            sum += n.doubleValue(); // Safe to read as Number
        }
        // list.add(10); // COMPILE ERROR: Cannot write into ? extends Number
        return sum;
    }

    // Consumer Super: Writing integers
    public static void addIntegers(List<? super Integer> list) {
        list.add(1); // Safe to write Integer
        list.add(2);
        // Number n = list.get(0); // Only safe to read as Object
    }
}
```

### Type Erasure & Invariance
- Generic type parameters (`List<String>`) are erased at compile time to raw types (`List`) with synthetic casts.
- Arrays are **covariant** and reified (`String[]` is assignable to `Object[]`, throwing runtime `ArrayStoreException`).
- Generics are **invariant** (`List<String>` is NOT assignable to `List<Object>`), preventing runtime crashes.

---

## 9 Java Collections Framework (JCF) Exhaustive Guide

### Hierarchy Tree
```
                    Iterable<E>
                        │
                  Collection<E>
         ┌──────────────┼──────────────┐
       List<E>        Set<E>        Queue<E> / Deque<E>
         │              │              │
    ArrayList       HashSet        ArrayDeque
    LinkedList      TreeSet        PriorityQueue
                    LinkedHashSet
```
*(Note: `Map<K, V>` is an independent hierarchy: `HashMap`, `LinkedHashMap`, `TreeMap`, `ConcurrentHashMap`)*.

### `HashMap` Internal Mechanics
- **Internal Storage**: Array of buckets (`Node<K, V>[] table`), default initial capacity `16`, load factor `0.75`.
- **Index Calculation**: `index = (n - 1) & hash(key)`.
- **Hash Collision Handling**:
  1. Buckets store elements as singly-linked lists.
  2. **Treeification (Java 8+)**: When bucket linked list length reaches threshold **`TREEIFY_THRESHOLD = 8`** and total table capacity is at least `64`, the bucket converts to a **Red-Black Tree** (improving search from `O(N)` to `O(log N)`).
  3. **Untreeification**: Shrinks back to linked list if tree node count drops to `6`.

```java
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;

// 1. Immutable List / Set / Map factory (Java 9+)
List<String> immutableList = List.of("A", "B", "C"); // Throws UnsupportedOperationException on add()
Map<String, Integer> map = Map.of("Alice", 90, "Bob", 85);

// 2. High-Performance Thread-Safe ConcurrentHashMap
ConcurrentHashMap<String, Long> counterMap = new ConcurrentHashMap<>();
counterMap.compute("views", (k, v) -> v == null ? 1L : v + 1L);
```

---

## 10 Functional Programming, Lambdas, and Streams API

### Core Functional Interfaces (`java.util.function`)
| Interface | Signature | Description | Method |
| :--- | :--- | :--- | :--- |
| `Predicate<T>` | `T -> boolean` | Evaluates boolean condition | `.test(t)` |
| `Function<T, R>` | `T -> R` | Transforms value | `.apply(t)` |
| `Consumer<T>` | `T -> void` | Consumes value with side effects | `.accept(t)` |
| `Supplier<T>` | `() -> T` | Factory producing values | `.get()` |
| `UnaryOperator<T>` | `T -> T` | Transforms value of same type | `.apply(t)` |
| `BiFunction<T, U, R>`| `(T, U) -> R` | Takes 2 inputs, returns 1 output | `.apply(t, u)` |

### Streams API Pipeline
Streams are **lazy**; intermediate operations execute only when a terminal operation is called.

```java
import java.util.*;
import java.util.stream.Collectors;

public record Order(Long id, String customer, double amount, String status) {}

public class StreamProcessor {
    public static void main(String[] args) {
        List<Order> orders = List.of(
            new Order(1L, "Alice", 250.0, "COMPLETED"),
            new Order(2L, "Bob", 50.0, "PENDING"),
            new Order(3L, "Alice", 150.0, "COMPLETED"),
            new Order(4L, "Charlie", 300.0, "COMPLETED")
        );

        // 1. Filter, Map, Reduce pipeline
        double totalCompleted = orders.stream()
            .filter(o -> "COMPLETED".equals(o.status()))
            .mapToDouble(Order::amount)
            .sum(); // 700.0

        // 2. GroupingBy Collector (Map of Customer -> Total Spent)
        Map<String, Double> spendByCustomer = orders.stream()
            .filter(o -> "COMPLETED".equals(o.status()))
            .collect(Collectors.groupingBy(
                Order::customer,
                Collectors.summingDouble(Order::amount)
            ));
        // {"Alice": 400.0, "Charlie": 300.0}
    }
}
```

---

## 11 Optional and Null Safety

```java
import java.util.Optional;

public class UserService {
    public String getUserCity(User user) {
        return Optional.ofNullable(user)
            .map(User::getAddress)
            .map(Address::getCity)
            .filter(city -> !city.isBlank())
            .orElse("Unknown City");
    }

    public User getOrThrow(Long id) {
        return findById(id)
            .orElseThrow(() -> new NoSuchElementException("User not found: " + id));
    }
}
```
> [!WARNING]
> **Anti-Patterns**: Never use `Optional` as a class field, constructor parameter, or inside Collections (`List<Optional<T>>`). Use it strictly as a method return type.

---

## 12 Exception Handling Architecture

### `Throwable` Hierarchy
```
                       Throwable
                           │
             ┌─────────────┴─────────────┐
           Error                      Exception
     (JVM / OutOfMemory)                 │
                               ┌─────────┴─────────┐
                        Checked Exceptions    RuntimeException
                        (IOException, etc.)   (Unchecked: NullPointer,
                                               IllegalArgument)
```

### Try-With-Resources (Automatic Resource Management)
Classes implementing `AutoCloseable` are closed automatically, even on exceptions.

```java
import java.io.*;

public void copyFile(String src, String dest) throws IOException {
    try (var in = new FileInputStream(src);
         var out = new FileOutputStream(dest)) {
        in.transferTo(out); // Zero-copy stream transfer
    } // in and out automatically closed here in reverse declaration order
}
```

---

## 13 Concurrency, Thread Synchronization, and JMM

### Java Memory Model (JMM) & `volatile`
- **CPU Cache Coherency**: Threads maintain local CPU register/cache copies of variables.
- **`volatile` Keyword Guarantees**:
  1. **Visibility**: Writes to a `volatile` variable are flushed immediately to main memory, visible to all threads.
  2. **Ordering**: Prevents instruction reordering around reads/writes (Generates Memory Barriers).
  3. *(Note: `volatile` does NOT guarantee atomicity for operations like `count++`)*.

```java
// Thread-Safe Singleton with Double-Checked Locking
public class DatabaseConnectionPool {
    private static volatile DatabaseConnectionPool instance;

    private DatabaseConnectionPool() {}

    public static DatabaseConnectionPool getInstance() {
        if (instance == null) {
            synchronized (DatabaseConnectionPool.class) {
                if (instance == null) {
                    instance = new DatabaseConnectionPool();
                }
            }
        }
        return instance;
    }
}
```

### Concurrency Utilities (`java.util.concurrent`)
```java
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicLong;

// 1. ThreadPoolExecutor
ExecutorService executor = Executors.newFixedThreadPool(4);
Future<String> futureResult = executor.submit(() -> {
    Thread.sleep(100);
    return "Calculated Payload";
});
String result = futureResult.get(1, TimeUnit.SECONDS);

// 2. Lock-free Atomic Variables (CAS - Compare-And-Swap)
AtomicLong atomicCounter = new AtomicLong(0);
atomicCounter.incrementAndGet(); // Thread-safe lock-free increment
```

---

## 14 Virtual Threads and Project Loom (Java 21+)

### Platform Threads vs Virtual Threads
| Feature | Platform Thread (`OS Thread`) | Virtual Thread (`Thread.ofVirtual()`) |
| :--- | :--- | :--- |
| **Creation Cost** | Heavy (1-2 MB stack per thread) | Ultra-lightweight (Few bytes on Heap) |
| **Scale Limit** | ~few thousand before OS out-of-memory | **Millions** of concurrent threads |
| **I/O Blocking** | Blocks underlying OS thread | Unmounts carrier thread automatically! |
| **Pool Requirement** | **Must be pooled** (`ExecutorService`) | **Never pool** (Create on-demand per task) |

```java
import java.util.concurrent.Executors;

public class VirtualThreadServer {
    public static void main(String[] args) {
        // High-throughput per-task executor handling 100,000 concurrent tasks
        try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
            for (int i = 0; i < 100_000; i++) {
                final int taskId = i;
                executor.submit(() -> {
                    Thread.sleep(1000); // Parks virtual thread without blocking OS carrier
                    return taskId;
                });
            }
        } // Auto-waits for all tasks to complete!
    }
}
```

---

## 15 Annotations and Reflection API

### Custom Runtime Annotation
```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME) // Available at runtime for reflection
@Target(ElementType.METHOD)          // Can be placed on methods
public @interface AuditLog {
    String action();
    int severity() default 1;
}

public class AccountService {
    @AuditLog(action = "TRANSFER_FUNDS", severity = 3)
    public void transfer(String from, String to, double amount) {
        System.out.println("Transferring $" + amount);
    }
}
```

---

## 16 Production Web Backends (Spring Boot 3.x Architecture)

### Spring Boot 3 Layered Architecture
```
src/main/java/com/app/
├── Application.java               # @SpringBootApplication entry point
├── controller/                    # @RestController (HTTP Endpoints)
│   └── UserController.java
├── service/                       # @Service (Business logic & @Transactional)
│   └── UserService.java
├── repository/                    # @Repository (Spring Data JPA interfaces)
│   └── UserRepository.java
├── entity/                        # @Entity (Hibernate database mappings)
│   └── UserEntity.java
└── dto/                           # Records for API payloads with Jakarta validation
    └── CreateUserRequest.java
```

### Complete Spring Boot 3 REST Endpoint
```java
package com.app.controller;

import jakarta.validation.Valid;
import jakarta.validation.constraints.*;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

public record CreateUserRequest(
    @NotBlank(message = "Username is required")
    @Size(min = 3, max = 20)
    String username,

    @Email(message = "Invalid email format")
    @NotBlank
    String email
) {}

@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    private final UserService userService;

    // Constructor Injection (Best Practice)
    public UserController(UserService userService) {
        this.userService = userService;
    }

    @PostMapping
    public ResponseEntity<UserResponse> createUser(@Valid @RequestBody CreateUserRequest request) {
        UserResponse response = userService.registerUser(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
}
```

---

## 17 File I/O, Serialization, and Network HTTP Client

### Modern File I/O (`java.nio.file.Path` & `Files`)
```java
import java.nio.file.*;
import java.io.IOException;

Path path = Path.of("data/app.log");
Files.createDirectories(path.getParent());

// Write & Read lines
Files.writeString(path, "INFO: System started\n", StandardOpenOption.CREATE, StandardOpenOption.APPEND);
String content = Files.readString(path);
```

### Modern `HttpClient` (Java 11+ Async HTTP/2)
```java
import java.net.URI;
import java.net.http.*;
import java.time.Duration;

public class ApiClient {
    private static final HttpClient client = HttpClient.newBuilder()
        .version(HttpClient.Version.HTTP_2)
        .connectTimeout(Duration.ofSeconds(5))
        .build();

    public static void fetchAsync(String url) {
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(url))
            .GET()
            .build();

        client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
            .thenApply(HttpResponse::body)
            .thenAccept(System.out::println)
            .join();
    }
}
```

---

## 18 Testing, Benchmarking, and Tooling

### Unit Testing with JUnit 5 & Mockito
```java
import org.junit.jupiter.api.*;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;
import org.mockito.*;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    @DisplayName("Should return user when ID exists")
    void testFindUserById() {
        when(userRepository.findById(1L)).thenReturn(Optional.of(new UserEntity(1L, "Aman")));

        UserDto user = userService.getUserById(1L);
        assertNotNull(user);
        assertEquals("Aman", user.username());
        verify(userRepository, times(1)).findById(1L);
    }

    @ParameterizedTest
    @ValueSource(strings = {"", " ", "  "})
    void testBlankUsernameThrows(String candidate) {
        assertThrows(IllegalArgumentException.class, () -> new UserDto(1L, candidate, "test@mail.com"));
    }
}
```

---

## 19 High-Yield Interview Questions and Reality Check

### 1. What is the contract between `equals()` and `hashCode()`?
> **Answer**:
> - If two objects are equal according to `equals(Object)`, they **MUST** produce the exact same integer from `hashCode()`.
> - If two objects have the same `hashCode()`, they are **NOT necessarily equal** (Hash collision).
> - If you override `equals()`, you **MUST always override `hashCode()`**, otherwise `HashMap` and `HashSet` will fail to retrieve stored keys.

### 2. How does `HashMap` work internally and how was it optimized in Java 8?
> **Answer**: `HashMap` uses an array of buckets (`Node<K, V>[] table`). A key's hash is mapped to an index using `(n - 1) & hash`. Collisions are chained as linked lists. In Java 8, when a bucket exceeds `TREEIFY_THRESHOLD = 8` and table capacity $\ge 64$, the linked list converts to a **Red-Black Tree**, reducing worst-case lookup from $O(N)$ to $O(\log N)$.

### 3. What is the difference between Checked and Unchecked Exceptions?
> **Answer**: Checked exceptions inherit directly from `Exception` (excluding `RuntimeException`) and represent recoverable failures that the compiler **forces** you to handle via `try-catch` or `throws` (e.g., `IOException`, `SQLException`). Unchecked exceptions inherit from `RuntimeException` and represent programming/logic errors (e.g., `NullPointerException`, `IllegalArgumentException`), which do not require mandatory handling.

### 4. What is the difference between Platform Threads and Virtual Threads?
> **Answer**: Platform threads are 1-to-1 wrappers around operating system kernel threads, consuming ~1MB of memory each and limited to a few thousand. Virtual threads (Project Loom, Java 21) are managed entirely by the JVM on the heap (few bytes). When a virtual thread blocks on I/O, the JVM unmounts it from its OS carrier thread, allowing millions of concurrent tasks with zero carrier thread blocking.

### 5. What is the PECS rule in Java Generics?
> **Answer**: PECS stands for **Producer Extends, Consumer Super**:
> - Use `? extends T` when you only read from a collection (it produces items of type `T`).
> - Use `? super T` when you only write to a collection (it consumes items of type `T`).

---

### Reality Check & Best Practices
- Never use raw types (e.g., `List` instead of `List<String>`).
- Always use `try-with-resources` for `AutoCloseable` objects (Streams, DB Connections).
- Avoid `synchronized` in virtual threads if holding locks over I/O (to prevent thread pinning; use `ReentrantLock` instead).
- Use `Records` for immutable data transfer objects and `@RestController` JSON bodies.
- Never use `new String("text")` or `new Integer(100)` (deprecated/wasteful).
