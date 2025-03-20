# Module 6 Reflection

**Name  : Muhammad Faizi Ismady Supardjo** <br>
**NPM   : 2306244955** <br>
**Class : A**

# Links
- [Commit 1 Reflection](##-Commit-1-Reflection)
- [Commit 2 Reflection](##-Commit-2-Reflection)

## Commit 1 Reflection
### Milestone 1: Single threaded web server
### message “(1) Handle-connection, check response"

For the first milestone of this project, I implemented a simple single-threaded web server in Rust. The server listens on port 7878 and handles incoming HTTP requests. To run the project, I first start the server using `cargo run`, and then I can access it through a web browser at `http://127.0.0.1:7878`. Since the server does not yet send any response, the browser will not display anything, but in the console, we can see that a connection has been established. Additionally, the request details, including headers like `User-Agent`, `Host`, and `Accept`, are printed to the console.

To handle incoming requests, I introduced the `handle_connection` function. This function reads the request data from the TCP stream using a `BufReader`, which allows us to process the incoming request line by line. Each line is collected into a `Vec<String>` until an empty line is encountered, which marks the end of the request headers. By printing this request, we gain insight into how browsers communicate with web servers, including the structure of an HTTP request.

One key learning point from this implementation is that HTTP requests are made up of multiple lines, typically starting with a request method (such as `GET / HTTP/1.1`) followed by various headers. Additionally, I noticed that browsers may retry requests if they do not receive a response, leading to multiple "Connection established!" messages in the console. Another important detail is that we need to manually stop the server using Ctrl + C, as the program continuously listens for incoming requests. If not stopped properly, the port 7878 remains occupied, preventing a second execution of the program.

## Commit 2 Reflection
### Milestone 2: Returning HTML
### message: “(2) Returning HTML"

In this milestone, I modified the `handle_connection` function to return an actual HTML response, allowing the browser to render a simple webpage. Instead of just printing the HTTP request to the console, our server now serves an HTML file named `hello.html`.  

### Key Changes and Learnings:  

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