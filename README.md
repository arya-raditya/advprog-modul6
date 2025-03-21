Name    : Arya Raditya Kusuma

NPM     : 2306215816

Class    : B


# Weekly Assignment 6

## Commit 1 Reflection Notes
This commit implements a basic TCP server in Rust, following a tutorial to set up listening on `127.0.0.1:7878` using `TcpListener`. The `main` function, as outlined in the tutorial, accepts incoming `TcpStream` connections within a loop. The `handle_connection` function, also based on the tutorial's structure, uses a `BufReader` to read the HTTP request data line by line. The `.lines()` method, combined with `.map(|result| result.unwrap())`, is used for reading and error handling (though the reliance on `.unwrap()` for error handling is something I recognize needs improvement for production code). The `.take_while(|line| !line.is_empty())` method is used to stop reading when an empty line, the typical HTTP header terminator, is found. The collected request lines are then printed to the console. This initial implementation closely follows the tutorial's guidance, providing a functional but rudimentary server. It doesn't yet handle responses or more complex HTTP requests, and the error handling is basic. However, understanding this foundational code is a crucial step towards building a more complete and robust HTTP server. This exercise helped solidify my understanding of basic networking concepts in Rust.

## Commit 2 Reflection Notes
![Commit 2 screen capture](/assets/images/commit2.png) 
This commit upgrades the server to serve HTML content from a file named `hello.html`. The `main` function is mostly unchanged, but `handle_connection` now constructs and sends an HTTP response. After reading the request, the code reads the contents of `hello.html` using `fs::read_to_string`, sets a "HTTP/1.1 200 OK" status line, and calculates the content length.  It then formats a complete HTTP response string, including the status, `Content-Length` header, and the HTML content.  This response is sent back to the client using `stream.write_all(response.as_bytes())`. The `.unwrap()` method is still used, which means errors will crash the program.