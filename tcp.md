### 🖥️ Server Code (`server.py`)
```python
import socket

def start_server():
    # Create a TCP/IP socket
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # Bind the socket to a local host and port
    host = '127.0.0.1'   # localhost
    port = 65432         # arbitrary non-privileged port
    server_socket.bind((host, port))

    # Listen for incoming connections
    server_socket.listen(1)
    print(f"Server listening on {host}:{port}...")

    while True:
        # Wait for a connection
        conn, addr = server_socket.accept()
        print(f"Connected by {addr}")

        # Receive data from client
        data = conn.recv(1024).decode()
        if not data:
            break
        print(f"Received from client: {data}")

        # Send response back to client
        response = f"Hello Client, I received your message: {data}"
        conn.sendall(response.encode())

        # Close connection
        conn.close()

if __name__ == "__main__":
    start_server()
```

---

### 💻 Client Code (`client.py`)
```python
import socket

def start_client():
    # Create a TCP/IP socket
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # Connect to server
    host = '127.0.0.1'   # server address
    port = 65432
    client_socket.connect((host, port))

    # Send message to server
    message = "Hello Server, this is Client!"
    client_socket.sendall(message.encode())

    # Receive response from server
    data = client_socket.recv(1024).decode()
    print(f"Received from server: {data}")

    # Close connection
    client_socket.close()

if __name__ == "__main__":
    start_client()
```

---

### ⚙️ How to Run
1. Save the server code as `server.py` and the client code as `client.py`.
2. Run the server first:
   ```bash
   python3 server.py
   ```
3. In another terminal, run the client:
   ```bash
   python3 client.py
   ```
4. You’ll see the client send a message and the server respond.

---

This is a **basic synchronous TCP client-server model**. You can extend it with:
- Multi-threading on the server to handle multiple clients simultaneously.
- Error handling and graceful shutdown.
- Using `select` or `asyncio` for non-blocking communication.
