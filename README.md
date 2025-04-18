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

1. **Why do we use RwLock<> instead of Mutex<> for synchronizing the Vec<Notification>?** </br>

In this tutorial, we used RwLock<Vec<Notification>> to allow multiple readers or a single writer at a time. This is necessary because:

- Efficiency with Multiple Readers: RwLock allows multiple threads to read from Vec<Notification> concurrently, as long as no thread is writing. This improves performance in scenarios where reading occurs more frequently than writing.
- Single Writer at a Time: When a write operation is required (such as adding a new notification), RwLock ensures that only one writer can access the data at a time, preventing race conditions.
- Better Read Performance: Compared to Mutex<Vec<Notification>>, RwLock is more efficient when reads are much more common than writes, as it allows concurrent reads instead of forcing all access to be sequential.

We do not use Mutex<> in this case because:

- Mutex<> only allows one thread to access the data at a time, whether it is for reading or writing. This would cause unnecessary blocking, making read-heavy operations less efficient.
- Unlike RwLock, Mutex does not differentiate between read and write operations—every access requires exclusive ownership, which can reduce concurrency in read-heavy workloads.

2. **Why does Rust not allow mutating a static variable like Java?** </br>

In Rust, static variables are immutable by default, meaning they cannot be modified after initialization. However, in Java, static variables can be mutated freely using static methods. The reason Rust enforces this restriction is:

- Thread Safety: If Rust allowed direct mutation of static variables, multiple threads could modify them simultaneously, leading to data races. Rust’s ownership model and borrowing rules prevent these issues.
- Memory Safety: Allowing mutable static variables without synchronization could cause undefined behavior, as multiple parts of the program could read and write the variable without coordination.
- Explicit Synchronization: Instead of allowing unsafe mutations, Rust forces us to use synchronization primitives like RwLock<>, Mutex<>, or lazy_static! to ensure that mutations happen safely in a concurrent environment.

In contrast, Java relies on developers to use manual synchronization (like synchronized blocks) to prevent race conditions, whereas Rust enforces safety at compile-time.

#### Reflection Subscriber-2

1. Exploring Beyond the Tutorial (e.g., src/lib.rs)
Yes, I have explored parts of the code outside of the tutorial steps, including src/lib.rs. While the tutorial mainly focused on implementing the notification system’s handlers and services, I checked lib.rs to understand how the modules were structured and how dependencies were managed.

Key Learnings from Exploring lib.rs:

- It serves as an entry point for module organization in Rust projects.
- It helps expose functions, structs, and other modules to the rest of the application.
- Understanding how mod and use statements work in Rust improves overall project organization.

2. Observer Pattern and Scaling the System
The Observer pattern simplifies adding more subscribers because:

- Each subscriber (Receiver app instance) listens for notifications independently.
- The Publisher (Main app) doesn’t need to change when new subscribers are added.
- New Receivers can simply subscribe to notifications without modifying the core system.

However, spawning multiple instances of the Main app introduces complexities:

- Unlike Receivers, which only listen, the Main app actively publishes notifications.
- Multiple Main apps might cause duplicate messages or race conditions if not handled properly.
- A message queue or a distributed event system would be needed to synchronize multiple Main instances effectively.

While adding more Receivers is straightforward, handling multiple Main instances requires additional architectural considerations.

3. Writing Tests and Enhancing Postman Documentation

I successfully launched multiple instances of the Receiver app, each running on different ports (8001, 8002, and 8003). This was achieved by modifying the .env file to adjust the ROCKET_PORT and APP_INSTANCE_NAME variables. After configuring these settings, I started each instance separately using cargo run in different terminal windows.

To test the system, I also launched a single instance of the Main app. Using the Postman collection, I subscribed each Receiver instance to different product types. I then triggered the create Product, publish Product, and delete Product endpoints in the Main app. As expected, each Receiver instance successfully received notifications relevant to its subscription.

Additionally, I tested the system with multiple product categories to verify that notifications were routed to the correct subscribers. This confirmed that the Observer pattern effectively allows multiple Receiver instances to dynamically receive updates. Postman was particularly useful during this process, as it streamlined testing by making it easy to send requests and inspect responses.

