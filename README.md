Name    : Arya Raditya Kusuma

NPM     : 2306215816

Class    : B


# Weekly Assignment 6

## Commit 1 Reflection Notes
![Commit 1 screen capture](/assets/images/commit1.png) 
This commit implements a basic TCP server in Rust, following a tutorial to set up listening on `127.0.0.1:7878` using `TcpListener`. The `main` function, as outlined in the tutorial, accepts incoming `TcpStream` connections within a loop. The `handle_connection` function, also based on the tutorial's structure, uses a `BufReader` to read the HTTP request data line by line. The `.lines()` method, combined with `.map(|result| result.unwrap())`, is used for reading and error handling (though the reliance on `.unwrap()` for error handling is something I recognize needs improvement for production code). The `.take_while(|line| !line.is_empty())` method is used to stop reading when an empty line, the typical HTTP header terminator, is found. The collected request lines are then printed to the console. This initial implementation closely follows the tutorial's guidance, providing a functional but rudimentary server. It doesn't yet handle responses or more complex HTTP requests, and the error handling is basic. However, understanding this foundational code is a crucial step towards building a more complete and robust HTTP server. This exercise helped solidify my understanding of basic networking concepts in Rust.

## Commit 2 Reflection Notes
![Commit 2 screen capture](/assets/images/commit2.png) 
This commit upgrades the server to serve HTML content from a file named `hello.html`. The `main` function is mostly unchanged, but `handle_connection` now constructs and sends an HTTP response. After reading the request, the code reads the contents of `hello.html` using `fs::read_to_string`, sets a "HTTP/1.1 200 OK" status line, and calculates the content length.  It then formats a complete HTTP response string, including the status, `Content-Length` header, and the HTML content.  This response is sent back to the client using `stream.write_all(response.as_bytes())`. The `.unwrap()` method is still used, which means errors will crash the program.

## Commit 3 Reflection Notes
![Commit 3 screen capture](/assets/images/commit3.png) 
This commit introduces basic error handling by adding a `404.html` file and modifying the server to return a 404 Not Found response for unrecognized requests.  The `handle_connection` function now reads only the *first line* of the request using `buf_reader.lines().next().unwrap().unwrap()`.  This line, which should contain the request method, path, and HTTP version, is then checked.  If the `request_line` is exactly `"GET / HTTP/1.1"`, the server responds with the `hello.html` content and a 200 OK status, as before.  However, any other request will now trigger the `else` block.

In the `else` block, a 404 Not Found response is constructed. The `status_line` is set to `"HTTP/1.1 404 NOT FOUND"`, and the contents of `404.html` are read.  The response, including the 404 status and the `404.html` content, is then sent to the client.  This provides a more user-friendly response than simply crashing or hanging when an unknown request is received.  The `404.html` file itself contains a simple "Oops!" message.

## Commit 4 Reflection Notes
![Commit 4 screen capture](/assets/images/commit4.png) 
This commit introduces a new route, `/sleep`, designed to demonstrate how the server handles a situation where a request takes a significant amount of time to process. This new route simulates a long-running operation. When the server receives a request to `/sleep`, it deliberately pauses execution for a full ten seconds before sending any response.  After this delay, it sends the same content as the standard "hello" page.

The primary purpose of this addition is to highlight the behavior of a single-threaded server when faced with a blocking operation. The ten-second pause prevents the server from handling any other incoming requests during that time. If multiple clients try to connect simultaneously, and one requests `/sleep`, all other clients will experience a delay, even if they request a different, normally fast, page. This demonstrates a significant limitation of a single-threaded approach for handling concurrent requests. While acceptable for this simple example, a production server would require asynchronous programming or multithreading to avoid blocking the main thread and ensure responsiveness to all clients. This commit serves as a valuable lesson in understanding the need for concurrency in server applications.

## Commit 5 Reflection Notes
![Commit 5 screen capture](/assets/images/commit5.png) 
This commit introduces a thread pool to handle incoming connections concurrently, significantly improving the server's responsiveness.  Instead of processing each request in the main thread (which could lead to blocking, as seen with the `/sleep` route), a `ThreadPool` is created with a fixed number of worker threads.  The `ThreadPool` uses a channel (`mpsc`) to receive tasks (closures representing the `handle_connection` function) and distributes them to the worker threads.  Each `Worker` runs in an infinite loop, waiting for jobs from the channel and executing them.  Shared ownership of the channel's receiver is managed using `Arc` and `Mutex` to ensure thread safety. The `main` function now creates a `ThreadPool` and uses its `execute` method to pass a closure containing `handle_connection` for each incoming `TcpStream`. This allows multiple requests, including slow ones like `/sleep`, to be handled concurrently without blocking the main thread or other clients. While this improves responsiveness, proper error handling and graceful shutdown mechanisms are still needed for a production-ready server.

## Bonus Commit Reflection Notes
This commit introduces a `build` function as a superior alternative to the existing `new` function for constructing the `ThreadPool`. The core improvement lies in the error handling mechanism.  The original `new` function relied on `assert!` to validate the thread pool size.  While simple, this approach has a significant drawback: it causes the program to panic (crash) if an invalid size (like zero) is provided.  This is undesirable for production code, where graceful error handling is crucial.

The `build` function, in contrast, adopts a more robust and idiomatic Rust approach by returning a `Result<ThreadPool, PoolCreationError>`. This `Result` type explicitly signals the possibility of failure.  If the provided size is valid, `build` creates the `ThreadPool` and returns it wrapped in an `Ok` variant.  If the size is invalid, `build` returns an `Err` variant containing a custom `PoolCreationError` instance, which holds a descriptive error message. This custom error type provides more context than a generic panic message.

The `main` function is updated to demonstrate the correct usage of `build`.  It calls `ThreadPool::build` and uses a `match` statement to handle the `Result`.  If creation is successful, the `ThreadPool` is extracted from the `Ok` variant.  If an error occurs, the error message (obtained from custom error type) is printed to the console, and the program exits gracefully. This pattern of returning a `Result` and allowing the caller to handle potential errors is a cornerstone of robust Rust programming.  The `build` function, with its explicit error handling, is a significant improvement over the `new` function, promoting safer and more maintainable code, we also implemented Display trait to provide better error messages.