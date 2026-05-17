# TCP

## 1. Key Code Patterns (Cheat Sheet)

### **TCP Server (Core Steps)**
1. **Create Socket**
   ```c
   server_fd = socket(AF_INET, SOCK_STREAM, 0);
   ```
2. **Address Setup**
   ```c
   server_addr.sin_family = AF_INET;
   server_addr.sin_addr.s_addr = INADDR_ANY;
   server_addr.sin_port = htons(PORT);
   ```
3. **Bind**
   ```c
   bind(server_fd, (struct sockaddr*)&server_addr, sizeof(server_addr));
   ```
4. **Listen**
   ```c
   listen(server_fd, 5);
   ```
5. **Accept**
   ```c
   client_fd = accept(server_fd, NULL, NULL);
   ```
6. **Read/Write**
   ```c
   read(client_fd, buffer, sizeof(buffer));
   send(client_fd, "message", length, 0);
   ```
7. **Close**
   ```c
   close(client_fd);
   close(server_fd);
   ```

### **TCP Client (Core Steps)**
1. **Create Socket**
   ```c
   sock = socket(AF_INET, SOCK_STREAM, 0);
   ```
2. **Define Server Address**
   ```c
   server_addr.sin_family = AF_INET;
   server_addr.sin_port = htons(PORT);
   inet_pton(AF_INET, "127.0.0.1", &server_addr.sin_addr);
   ```
3. **Connect**
   ```c
   connect(sock, (struct sockaddr*)&server_addr, sizeof(server_addr));
   ```
4. **Send/Receive**
   ```c
   send(sock, "message", length, 0);
   read(sock, buffer, sizeof(buffer));
   ```
5. **Close**
   ```c
   close(sock);
   ```

---

## 2. Flashcards (for Quick Recall)

### Card 1
**Q:** Which function creates a new socket in C?  
**A:** `socket()`  

---

### Card 2
**Q:** What does `bind()` do?  
**A:** Assigns a local address/port to the socket.

---

### Card 3
**Q:** Write code to make a server listen for connections.  
**A:**  
```c
listen(server_fd, 5);
```

---

### Card 4
**Q:** Which function allows a server to accept a new connection?  
**A:** `accept()`

---

### Card 5
**Q:** What’s the default protocol for SOCK_STREAM in C sockets?  
**A:** TCP

---

### Card 6
**Q:** UDP sockets use which type?  
**A:** `SOCK_DGRAM`

---

### Card 7
**Q:** Which function is used to send data in UDP?  
**A:** `sendto()`  

---

### Card 8
**Q:** What’s the main difference in flow between TCP and UDP server code?  
**A:** TCP uses `connect()` and `accept()`, UDP uses `sendto()` and `recvfrom()` with no connection setup.

---

### Card 9
**Q:** What is the purpose of `htons()`?  
**A:** Converts a host short (port) to network byte order.

---

## 3. Memory Drill

- Practice writing out all socket program steps **without looking**, then check for accuracy.
- Start with only the function calls, then fill in parameters/details.
- Make up your own mini-programs by changing ports or the order and see if they compile.

---

## 4. High-Yield Sections

- **Stop-and-Wait Protocol:** Loop sending, wait for ACK.  
  Sender loop: send, wait/receive ack, send next.
- **Distance Vector Routing:** Understand 2D arrays, update and print table.
- **FTP Client/Server:** Client sends filename; server reads & sends content line-by-line.

---

## 5. Visualization

- Draw out the flow: Server (waits) <-> Client (connects, sends/receives)
- UDP: No connection, direct send/receive.

---

## 6. Teach One Program

- Pick one example, close your notes, and explain every line out loud as if teaching a friend.

---
