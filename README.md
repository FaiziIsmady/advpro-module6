# Module 6 Reflection

Name  : Muhammad Faizi Ismady Supardjo
NPM   : 2306244955
Class : A

## Commit 1 Reflection
### “(1) Handle-connection, check response"

For the first milestone of this project, we implemented a simple single-threaded web server in Rust. The server listens on port 7878 and handles incoming HTTP requests. To run the project, we first start the server using `cargo run`, and then we can access it through a web browser at `http://127.0.0.1:7878`. Since the server does not yet send any response, the browser will not display anything, but in the console, we can see that a connection has been established. Additionally, the request details, including headers like `User-Agent`, `Host`, and `Accept`, are printed to the console.

To handle incoming requests, we introduced the `handle_connection` function. This function reads the request data from the TCP stream using a `BufReader`, which allows us to process the incoming request line by line. Each line is collected into a `Vec<String>` until an empty line is encountered, which marks the end of the request headers. By printing this request, we gain insight into how browsers communicate with web servers, including the structure of an HTTP request.

One key learning point from this implementation is that HTTP requests are made up of multiple lines, typically starting with a request method (such as `GET / HTTP/1.1`) followed by various headers. Additionally, we noticed that browsers may retry requests if they do not receive a response, leading to multiple "Connection established!" messages in the console. Another important detail is that we need to manually stop the server using Ctrl + C, as the program continuously listens for incoming requests. If not stopped properly, the port 7878 remains occupied, preventing a second execution of the program.