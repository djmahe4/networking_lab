### 🖥️ Server (`server_stop_wait.py`)
```python
import socket
import time
import random

def start_server():
    host = '127.0.0.1'
    port = 65432

    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.bind((host, port))
    server_socket.listen(1)
    print(f"Server listening on {host}:{port}...")

    conn, addr = server_socket.accept()
    print(f"Connected by {addr}")

    while True:
        data = conn.recv(1024).decode()
        if not data:
            break

        print(f"Received frame: {data}")

        # Simulate ACK loss (randomly drop ACKs to test retransmission)
        if random.choice([True, False]):
            ack = f"ACK for {data}"
            conn.sendall(ack.encode())
            print(f"Sent: {ack}")
        else:
            print(f"ACK for {data} lost!")

        time.sleep(1)

    conn.close()

if __name__ == "__main__":
    start_server()
```

---

### 💻 Client (`client_stop_wait.py`)
```python
import socket
import time

def start_client():
    host = '127.0.0.1'
    port = 65432

    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client_socket.connect((host, port))

    frames = ["Frame1", "Frame2", "Frame3", "Frame4"]

    for frame in frames:
        while True:
            print(f"Sending: {frame}")
            client_socket.sendall(frame.encode())

            # Wait for ACK with timeout
            client_socket.settimeout(3)
            try:
                ack = client_socket.recv(1024).decode()
                print(f"Received: {ack}")
                break  # ACK received, move to next frame
            except socket.timeout:
                print(f"Timeout! Resending {frame}...")

            time.sleep(1)

    client_socket.close()

if __name__ == "__main__":
    start_client()
```

---

### ⚙️ How It Works
1. **Client** sends one frame at a time.
2. **Server** receives the frame and sometimes "loses" the ACK (simulated).
3. If the client doesn’t receive an ACK within the timeout, it retransmits the frame.
4. This continues until all frames are successfully acknowledged.

---

### 🔍 Example Output
**Client side:**
```
Sending: Frame1
Timeout! Resending Frame1...
Received: ACK for Frame1
Sending: Frame2
Received: ACK for Frame2
...
```

**Server side:**
```
Received frame: Frame1
ACK for Frame1 lost!
Received frame: Frame1
Sent: ACK for Frame1
Received frame: Frame2
Sent: ACK for Frame2
...
```

---

This is a simple simulation of Stop-and-Wait ARQ. You can extend it to:
- Add sequence numbers to frames.
- Handle corrupted frames.
- Implement Go-Back-N or Selective Repeat for efficiency.

Would you like me to **upgrade this into a multi-client version** where several clients can simultaneously use Stop-and-Wait with the server?
