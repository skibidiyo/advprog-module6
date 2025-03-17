# Commit 1 Reflection

The code sets up a TCP listener on 127.0.0.1:7878, forwarding each incoming connection to the handle_connection function. Inside handle_connection, the TcpStream is wrapped in a BufReader, which reads incoming lines until it reaches an empty line indicating the end of the request headers. These header lines are collected into a vector and printed to the console, revealing typical HTTP request details. Because no response is sent back, a browser or other software initiating the connection will continue waiting until the connection times out.

# Commit 2 Reflection
![Commit 2 screen capture](assets/images/image.png)

After the modification, the handle_connection function returns an HTML response. The HTML content is read from the hello.html file using fs::read_to_string and stored in the contents variable as a String. In addition, the response body includes a status line and a Content-Length header, which are crucial for indicating the success or failure of the connection and for verifying the length of the response. Finally, the format! is used to combine the status line, Content-Length, and the HTML body into a single string, which is then sent over the TCP connection using stream.write_all().