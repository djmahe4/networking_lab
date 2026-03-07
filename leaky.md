## 🌊 Concept
- Imagine a bucket with a small hole at the bottom.
- Water (packets) can be poured into the bucket at any rate.
- But water leaks out at a fixed rate.
- If the bucket overflows (too many packets arrive too quickly), the excess is discarded.

This ensures **smooth, predictable traffic flow**.

---

## ⚙️ Algorithm Steps
1. Maintain a counter for the number of packets in the bucket.
2. When a packet arrives:
   - If the bucket is not full, add the packet.
   - If full, discard the packet (overflow).
3. At fixed intervals, leak packets at a constant rate into the network.

---

## 🖥️ Python Simulation

```python
import time
import random

class LeakyBucket:
    def __init__(self, capacity, leak_rate):
        self.capacity = capacity      # bucket size
        self.leak_rate = leak_rate    # packets per second
        self.bucket = 0               # current packets in bucket

    def add_packet(self, packets=1):
        if self.bucket + packets > self.capacity:
            print(f"Bucket overflow! {packets} packet(s) discarded.")
        else:
            self.bucket += packets
            print(f"Added {packets} packet(s). Bucket now has {self.bucket}.")

    def leak(self):
        if self.bucket > 0:
            leaked = min(self.leak_rate, self.bucket)
            self.bucket -= leaked
            print(f"Leaked {leaked} packet(s). Bucket now has {self.bucket}.")
        else:
            print("Bucket empty, nothing to leak.")

# Simulation
if __name__ == "__main__":
    bucket = LeakyBucket(capacity=10, leak_rate=2)

    for t in range(1, 11):  # simulate 10 time units
        print(f"\nTime {t}:")
        incoming = random.randint(0, 4)  # random incoming packets
        bucket.add_packet(incoming)
        bucket.leak()
        time.sleep(1)
```

---

## 📊 Example Output
```
Time 1:
Added 3 packet(s). Bucket now has 3.
Leaked 2 packet(s). Bucket now has 1.

Time 2:
Added 4 packet(s). Bucket now has 5.
Leaked 2 packet(s). Bucket now has 3.

Time 3:
Added 0 packet(s). Bucket now has 3.
Leaked 2 packet(s). Bucket now has 1.
...
```

---

## 🚦 Applications
- **Traffic shaping**: Smooths out bursts before entering the network.
- **Congestion control**: Prevents routers from being overloaded.
- **QoS (Quality of Service)**: Ensures fair bandwidth distribution.

---

👉 Since you’re working on your *networking lab project* (I see your GitHub tab open), do you want me to extend this into a **token bucket simulation** as well? It’s closely related but allows controlled bursts, which is often compared with leaky bucket in labs.
