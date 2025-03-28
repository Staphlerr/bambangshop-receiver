# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. **Why Use `RwLock<>` Instead of `Mutex<>` for Synchronizing the Vec of Notifications?**
   
    In this tutorial, we use `RwLock<>` to synchronize access to the Vec of notifications because it allows multiple threads to read the data simultaneously while ensuring exclusive access for writes. This is particularly useful for our use case, where the list_all_as_string function frequently reads from the NOTIFICATIONS vector, and the add function occasionally writes to it. Using `RwLock<>` improves performance by avoiding unnecessary blocking during concurrent reads, which would occur if we used `Mutex<>`.

    If we had used `Mutex<>`, all threads would need to wait for exclusive access to the Vec. This would lead to contention and reduced efficiency, especially when multiple threads are only reading the notifications. By using `RwLock<>`, we strike a balance between thread safety and performance, ensuring that reads are non-blocking while still protecting the data during writes.


2. **Why Does Rust Not Allow Mutation of Static Variables via Static Functions Like Java?**
   
    In Rust, static variables are immutable by default to ensure memory safety and prevent data races in multi-threaded environments. Unlike Java, where static variables can be mutated via static methods, Rust enforces stricter rules to avoid undefined behavior caused by concurrent access. Allowing direct mutation of static variables without synchronization could lead to race conditions, where multiple threads modify the same data simultaneously, resulting in inconsistent or corrupted state.

    To safely mutate static variables in Rust, we use synchronization primitives like `RwLock<>` or `Mutex<>`. These ensure that only one thread can write at a time, and reads are properly synchronized. Additionally, Rust's ownership model requires explicit handling of mutable references, making it harder to accidentally introduce unsafe code. While this approach may seem restrictive compared to Java, it provides stronger guarantees about thread safety and prevents common concurrency bugs.

#### Reflection Subscriber-2

1. **Exploring Other Parts of the Code (e.g., src/lib.rs)**

    Yes, I explored src/lib.rs. From it, I learned how `lazy_static` is used to create global variables like `REQWEST_CLIENT` and `APP_CONFIG`, which are essential for managing shared resources across the application. The `AppConfig` struct demonstrates how environment variables (`dotenv`) and default configurations are combined using Rocket's Figment to provide flexible and centralized configuration management. Additionally, the compose_error_response function shows how to standardize error handling with JSON responses, making the API more consistent and user-friendly.


2. **How Observer Pattern Eases Adding Subscribers**

   The Observer Pattern makes it easy to add subscribers by decoupling publishers and subscribers. Each Receiver instance subscribes independently, and the Main app sends notifications without needing to know subscriber details. However, adding multiple Main app instances would complicate state management, requiring synchronization mechanisms like a shared database or distributed system.


3. **Creating Tests and Enhancing Postman Documentation**
   
    I did not create additional tests or enhance the Postman collection during this tutorial, as the focus was on implementing and testing the notification system manually. However, I recognize the value of automated tests and comprehensive documentation in larger projects. Writing unit tests for functions like subscribe, unsubscribe, and receive_notification would ensure their correctness and make future refactoring safer. Similarly, enhancing the Postman collection with detailed descriptions and test scripts could streamline API testing and improve collaboration within a team.

    For group projects or real-world applications, these features would be invaluable. Automated tests reduce manual effort and catch regressions early, while well-documented APIs help onboard new developers and ensure consistent usage. Exploring tools like Rocket’s testing framework and Postman’s scripting capabilities could further enhance my workflow and improve the quality of my work.