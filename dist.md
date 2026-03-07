## 🧩 Algorithm Recap
- Each router maintains a **routing table** with distances to all other routers.
- Routers exchange their tables with **neighbors**.
- Update rule (Bellman-Ford style):  
$\text{Cost}(R,D) = \min_{N \in Neighbors} \big( \text{Cost}(R,N) + \text{Cost}(N,D) \big)$
- Repeat until no changes occur (convergence).

---

## 🖥️ Python Simulation

```python
import copy
import time

INF = 999  # Represent infinity

# Define network topology (adjacency matrix)
graph = {
    'A': {'A': 0, 'B': 1, 'C': 5},
    'B': {'A': 1, 'B': 0, 'C': 1},
    'C': {'A': 5, 'B': 1, 'C': 0}
}

# Initialize distance vector tables
tables = copy.deepcopy(graph)

def print_tables(step):
    print(f"\n--- Routing Tables after iteration {step} ---")
    for router, table in tables.items():
        print(f"{router}: {table}")

def distance_vector_routing(iterations=5):
    for step in range(1, iterations+1):
        updated = False
        for router in tables:
            for dest in tables[router]:
                for neighbor in graph[router]:
                    new_cost = graph[router][neighbor] + tables[neighbor][dest]
                    if new_cost < tables[router][dest]:
                        tables[router][dest] = new_cost
                        updated = True
        print_tables(step)
        time.sleep(1)
        if not updated:
            print("\n✅ Convergence achieved!")
            break

if __name__ == "__main__":
    print("Initial Routing Tables:")
    print_tables(0)
    distance_vector_routing()
```

---

## 📊 Example Output
```
Initial Routing Tables:
A: {'A': 0, 'B': 1, 'C': 5}
B: {'A': 1, 'B': 0, 'C': 1}
C: {'A': 5, 'B': 1, 'C': 0}

--- Routing Tables after iteration 1 ---
A: {'A': 0, 'B': 1, 'C': 2}
B: {'A': 1, 'B': 0, 'C': 1}
C: {'A': 2, 'B': 1, 'C': 0}

✅ Convergence achieved!
```
