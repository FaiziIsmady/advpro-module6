# Module 6 Reflection

**Name  : Muhammad Faizi Ismady Supardjo** <br>
**NPM   : 2306244955** <br>
**Class : A**

# Links
- [Commit 1 Reflection](##-Commit-1-Reflection)
- [Commit 2 Reflection](##-Commit-2-Reflection)
- [Commit 3 Reflection](##-Commit-3-Reflection)
- [Commit 4 Reflection](##-Commit-4-Reflection)

## Commit 1 Reflection
### Milestone 1: Single threaded web server
### Message “(1) Handle-connection, check response"

For the first milestone of this project, I implemented a simple single-threaded web server in Rust. The server listens on port 7878 and handles incoming HTTP requests. To run the project, I first start the server using `cargo run`, and then I can access it through a web browser at `http://127.0.0.1:7878`. Since the server does not yet send any response, the browser will not display anything, but in the console, we can see that a connection has been established. Additionally, the request details, including headers like `User-Agent`, `Host`, and `Accept`, are printed to the console.

To handle incoming requests, I introduced the `handle_connection` function. This function reads the request data from the TCP stream using a `BufReader`, which allows us to process the incoming request line by line. Each line is collected into a `Vec<String>` until an empty line is encountered, which marks the end of the request headers. By printing this request, we gain insight into how browsers communicate with web servers, including the structure of an HTTP request.

One key learning point from this implementation is that HTTP requests are made up of multiple lines, typically starting with a request method (such as `GET / HTTP/1.1`) followed by various headers. Additionally, I noticed that browsers may retry requests if they do not receive a response, leading to multiple "Connection established!" messages in the console. Another important detail is that we need to manually stop the server using Ctrl + C, as the program continuously listens for incoming requests. If not stopped properly, the port 7878 remains occupied, preventing a second execution of the program.

## Commit 2 Reflection
### Milestone 2: Returning HTML
### Message: “(2) Returning HTML"

In this milestone, I modified the `handle_connection` function to return an actual HTML response, allowing the browser to render a simple webpage. Instead of just printing the HTTP request to the console, our server now serves an HTML file named `hello.html`.  

**Key Changes and Learnings:**

2.1. **Reading HTTP Requests**  
   - I use `BufReader` to read the HTTP request and stop at the empty line that marks the end of the request headers.  

2.2. **Serving an HTML File**  
   - I load `hello.html` using `fs::read_to_string("hello.html").unwrap();`, ensuring the server responds with meaningful content.  

2.3. **Formatting a Proper HTTP Response**  
   - The response includes a status line (`HTTP/1.1 200 OK`), a `Content-Length` header, and the actual HTML content.  
   - This format ensures the browser correctly interprets and displays the webpage.  

2.4. **Handling File Paths**  
   - The `hello.html` file must be placed in the same directory as the executable for the server to locate it correctly.  

After running the server and navigating to `http://127.0.0.1:7878`, I can see the HTML content displayed in the browser. Below is a screenshot of the output: 

![Commit 2 screen capture](/assets/images/commit2.png)

## Commit 3 Reflection
### Milestone 3: Validating Request and Selectively Responding
### Message: “(3) Validating request and selectively responding"

Initially, my server served the same HTML file (`hello.html`) for any request. In this milestone, I modified it to handle different responses based on the requested path. If the request is for `/`, it returns `hello.html`. Otherwise, it serves a **404 Not Found** page (`404.html`).

**Implementation Updates**
- The server reads the request line from the client.
- It checks if the request is for `"GET / HTTP/1.1"`.  
  - If true, it responds with `hello.html` (status `200 OK`).
  - If false, it responds with `404.html` (status `404 Not Found`).

**Why Refactoring Was Needed?**
- Better Code Readability: The request validation logic is separate from response handling.
- Scalability:** It allows adding more pages easily.
- More Realistic Behavior: Servers should differentiate between valid and invalid requests.

**Updated `handle_connection` Function**
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };

    let contents = fs::read_to_string(filename).unwrap();
    let response = format!(
        "{status_line}\r\nContent-Length: {}\r\n\r\n{}",
        contents.len(),
        contents
    );

    stream.write_all(response.as_bytes()).unwrap();
}
```

**The "not found" page** `404.html`
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Oops!</title>
</head>
<body>
    <h1>Oops!</h1>
    <p>Sorry, I don't know what you're asking for.</p>
    <p>Rust is running from Faizi’s machine.</p>
</body>
</html>
```

**Here is the image:**

![Commit 3 screen capture](/assets/images/commit3.png)

## Commit 4 Reflection
### Milestone 4: Simulation slow response
### Message: “(4) Simulation of slow request"

My web server currently runs on a single thread, meaning it can handle only one request at a time. If a request takes a long time to process, all other requests are blocked until it is completed. To demonstrate this issue, I simulated a slow response.

To implement this, I modified the `handle_connection` function to introduce a delay when handling requests to `/sleep`.

```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    let (status_line, filename) = match request_line.as_str() {
        "GET / HTTP/1.1" => ("HTTP/1.1 200 OK", "hello.html"),
        "GET /sleep HTTP/1.1" => {
            thread::sleep(Duration::from_secs(10)); // Simulating slow response
            ("HTTP/1.1 200 OK", "hello.html")
        }
        _ => ("HTTP/1.1 404 NOT FOUND", "404.html"),
    };

    let contents = fs::read_to_string(filename).unwrap();
    let response = format!("{status_line}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
}
```

My server processes requests sequentially. When the `/sleep` request is received, all other requests must wait for 10 seconds before being processed.
This simulates a major problem in a real-world server (ex: slow API calls, complex query, inneficient code, etc) since it would cause delays for every user.




