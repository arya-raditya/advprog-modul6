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

