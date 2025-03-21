# Commit 1 Reflection

The code sets up a TCP listener on 127.0.0.1:7878, which continuously listens for incoming connections. Each time a connection is received, it is forwarded to the handle_connection function for processing. Inside handle_connection, the incoming TcpStream is wrapped in a BufReader, making it easier to read the data line by line. The BufReader then reads the incoming lines until it encounters an empty line, which indicates the end of the request headers. These header lines are collected into a vector and printed to the console, displaying the typical details of an HTTP request such as the method, path, and various headers. Because the code does not send any response back, the browser or any other software that initiated the connection will remain in a waiting state.

# Commit 2 Reflection
![Commit 2 screen capture](assets/images/commit2.png)

After the modification, the handle_connection function not only receives the request but also returns an HTML response. The HTML content is read from the hello.html file using fs::read_to_string. This content is stored in the contents variable as a String. In addition, the response body includes a status line and a Content-Length header. These headers are crucial for indicating the success or failure of the connection via HTTP status codes and for verifying the length of the response. Finally, the format! macro is used to combine the status line, Content-Length, and the HTML body into a single string, which is then sent over the TCP connection using stream.write_all().


# Commit 3 Reflection
![Commit 3 screen capture](assets/images/commit3.png)

The handle_connection function can filter incoming requests. If the request targets the root path, the server returns hello.html. Otherwise, the server responds with 404.html.

### Before Refactoring
![Commit 3 Before](assets/images/before.png)

### After Refactoring
![Commit 3 After](assets/images/after.png)

Refactoring reduces repetition and makes the code more maintainable. Before the refactoring, the if/else blocks each define and construct similar responses, resulting in duplicated logic for reading a file, calculating its length, and formatting the response. By using the (status_line, filename) tuple, we move the response-building steps outside of the conditional, reducing duplication and simplifying changes.

# Commit 4 Reflection
In the updated code, an additional route (GET /sleep HTTP/1.1) is handled by introducing a 10-second delay before returning the response. When the browser accesses the /sleep route, it takes longer to load because the thread sleeps for 10 seconds. This delay simulates a slow response scenario. Since the server processes requests synchronously, a slow route can delay the handling of other requests. If many users connect simultaneously, these delays can compound and affect overall responsiveness. This behavior highlights the importance of considering concurrency or asynchronous processing in a production environment.

# Commit 5 Reflection
A thread pool is a group of pre-initialized threads that are ready to handle tasks concurrently. When a new task arrives, one of these threads is assigned to execute the job, while the others stay available for incoming tasks. Once the thread finishes its work, it returns to the pool for future tasks. In this commit, the thread pool is implemented using a ThreadPool struct that manages a set number of Worker instances and a Job type that represents the tasks. Communication is done via message passing, with the ThreadPool acting as the sender and each Worker as the receiver of tasks.

The code defines a ThreadPool structure that holds a fixed number of worker threads and a sender for dispatching tasks through an mpsc channel. Each worker is represented by a Worker struct that spawns a thread, which continuously waits for and executes jobs from a shared, mutex-protected receiver. A job is defined as a boxed closure that implements FnOnce() + Send + 'static, allowing it to be safely executed in a thread, and the main function dispatches tasks like handling TCP connections using the execute method. This design enables the server to process multiple requests concurrently, ensuring that slow tasks do not block the entire server.

# Commit Bonus Reflection
I added a build() function to the ThreadPool implementation as an alternative to the new() constructor. This change was made to explore different naming conventions and gain insights into API design in Rust. Functionally, build() performs the same operations as new(), as it creates a channel, wraps it in a Mutex and Arc, and initializes the worker threads. Using a name like build() can be beneficial when the creation process may eventually require more complex configuration. It can also evolve into a builder pattern, which is common in many Rust libraries and frameworks. Although both methods currently perform the same actions, build() can be more expressive for developers by suggesting a more elaborate setup, whereas new() is typically used for straightforward object creation.



