Name    : Arya Raditya Kusuma

NPM     : 2306215816

Class    : B


# Weekly Assignment 6

## Commit 1
This commit implements a basic TCP server in Rust, following a tutorial to set up listening on `127.0.0.1:7878` using `TcpListener`. The `main` function, as outlined in the tutorial, accepts incoming `TcpStream` connections within a loop. The `handle_connection` function, also based on the tutorial's structure, uses a `BufReader` to read the HTTP request data line by line. The `.lines()` method, combined with `.map(|result| result.unwrap())`, is used for reading and error handling (though the reliance on `.unwrap()` for error handling is something I recognize needs improvement for production code). The `.take_while(|line| !line.is_empty())` method is used to stop reading when an empty line, the typical HTTP header terminator, is found. The collected request lines are then printed to the console. This initial implementation closely follows the tutorial's guidance, providing a functional but rudimentary server. It doesn't yet handle responses or more complex HTTP requests, and the error handling is basic. However, understanding this foundational code is a crucial step towards building a more complete and robust HTTP server. This exercise helped solidify my understanding of basic networking concepts in Rust.