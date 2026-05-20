Here are simple C programs for the requested networking concepts.

---

# 1. TCP Client–Server

## TCP Server

```c id="tcp-server"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {
    int server_fd, client_fd;
    struct sockaddr_in server_addr;
    char buffer[1024] = {0};

    server_fd = socket(AF_INET, SOCK_STREAM, 0);

    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = INADDR_ANY;
    server_addr.sin_port = htons(8080);

    bind(server_fd, (struct sockaddr*)&server_addr, sizeof(server_addr));

    listen(server_fd, 3);

    printf("Waiting for client...\n");

    client_fd = accept(server_fd, NULL, NULL);

    read(client_fd, buffer, 1024);
    printf("Client: %s\n", buffer);

    send(client_fd, "Hello Client", 12, 0);

    close(client_fd);
    close(server_fd);

    return 0;
}
```

## TCP Client

```c id="tcp-client"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {
    int sock;
    struct sockaddr_in server_addr;
    char buffer[1024] = {0};

    sock = socket(AF_INET, SOCK_STREAM, 0);

    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(8080);

    inet_pton(AF_INET, "127.0.0.1", &server_addr.sin_addr);

    connect(sock, (struct sockaddr*)&server_addr, sizeof(server_addr));

    send(sock, "Hello Server", 12, 0);

    read(sock, buffer, 1024);
    printf("Server: %s\n", buffer);

    close(sock);

    return 0;
}
```

Compile:

```bash id="tcp-build"
gcc server.c -o server
gcc client.c -o client
```

---

# 2. Stop-and-Wait Protocol

## Sender

```c id="stopwait-sender"
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {

    int sock;
    struct sockaddr_in server;
    char msg[100];

    sock = socket(AF_INET, SOCK_STREAM, 0);

    server.sin_family = AF_INET;
    server.sin_port = htons(9000);
    server.sin_addr.s_addr = inet_addr("127.0.0.1");

    connect(sock, (struct sockaddr*)&server, sizeof(server));

    for(int i=1; i<=5; i++) {

        sprintf(msg, "%d", i);

        printf("Sending Frame %d\n", i);

        send(sock, msg, strlen(msg)+1, 0);

        recv(sock, msg, sizeof(msg), 0);

        printf("ACK Received: %s\n", msg);

        sleep(1);
    }

    strcpy(msg, "end");
    send(sock, msg, strlen(msg)+1, 0);

    close(sock);

    return 0;
}
```

## Receiver

```c id="stopwait-receiver"
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {

    int server_sock, client_sock;
    struct sockaddr_in server;
    char msg[100];

    server_sock = socket(AF_INET, SOCK_STREAM, 0);

    server.sin_family = AF_INET;
    server.sin_port = htons(9000);
    server.sin_addr.s_addr = INADDR_ANY;

    bind(server_sock, (struct sockaddr*)&server, sizeof(server));

    listen(server_sock, 1);

    printf("Waiting for sender...\n");

    client_sock = accept(server_sock, NULL, NULL);

    while(1) {

        recv(client_sock, msg, sizeof(msg), 0);

        if(strcmp(msg, "end") == 0)
            break;

        printf("Received Frame: %s\n", msg);

        send(client_sock, msg, strlen(msg)+1, 0);

        printf("ACK Sent: %s\n", msg);
    }

    close(client_sock);
    close(server_sock);

    return 0;
}
```

---

# 3. Distance Vector Routing

```c id="dvr"
#include <stdio.h>

struct node {
    unsigned dist[20];
    unsigned from[20];
} rt[10];

int main() {
    int cost[20][20], nodes, i, j, k, count = 0;

    printf("Enter number of nodes: ");
    scanf("%d", &nodes);

    printf("Enter cost matrix:\n");

    for(i = 0; i < nodes; i++) {
        for(j = 0; j < nodes; j++) {
            scanf("%d", &cost[i][j]);

            cost[i][j] = (cost[i][j] == 0) ? 999 : cost[i][j];

            rt[i].dist[j] = cost[i][j];
            rt[i].from[j] = j;
        }
    }

    do {
        count = 0;

        for(i = 0; i < nodes; i++) {
            for(j = 0; j < nodes; j++) {
                for(k = 0; k < nodes; k++) {

                    if(rt[i].dist[j] > cost[i][k] + rt[k].dist[j]) {
                        rt[i].dist[j] = rt[i].dist[k] + rt[k].dist[j];
                        rt[i].from[j] = k;
                        count++;
                    }
                }
            }
        }

    } while(count != 0);

    for(i = 0; i < nodes; i++) {
        printf("\nRouter %d\n", i + 1);

        for(j = 0; j < nodes; j++) {
            printf("Node %d via %d Distance %d\n",
                   j + 1,
                   rt[i].from[j] + 1,
                   rt[i].dist[j]);
        }
    }

    return 0;
}
```

---

# 5. UDP Client–Server

## UDP Server

```c id="udp-server"
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {
    int sockfd;
    char buffer[1024];
    struct sockaddr_in server_addr, client_addr;
    socklen_t len;

    sockfd = socket(AF_INET, SOCK_DGRAM, 0);

    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = INADDR_ANY;
    server_addr.sin_port = htons(8080);

    bind(sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr));

    len = sizeof(client_addr);

    recvfrom(sockfd, buffer, sizeof(buffer), 0,
             (struct sockaddr*)&client_addr, &len);

    printf("Client: %s\n", buffer);

    sendto(sockfd, "Hello Client", 12, 0,
           (struct sockaddr*)&client_addr, len);

    close(sockfd);

    return 0;
}
```

## UDP Client

```c id="udp-client"
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {
    int sockfd;
    char buffer[1024];
    struct sockaddr_in server_addr;
    socklen_t len;

    sockfd = socket(AF_INET, SOCK_DGRAM, 0);

    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(8080);
    inet_pton(AF_INET, "127.0.0.1", &server_addr.sin_addr);

    sendto(sockfd, "Hello Server", 12, 0,
           (struct sockaddr*)&server_addr,
           sizeof(server_addr));

    len = sizeof(server_addr);

    recvfrom(sockfd, buffer, sizeof(buffer), 0,
             (struct sockaddr*)&server_addr, &len);

    printf("Server: %s\n", buffer);

    close(sockfd);

    return 0;
}
```

---

# FTP Server

```c id="ftp-real-server"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {
    int server_fd, client_fd;
    struct sockaddr_in server_addr;
    char filename[100], buffer[1024];
    FILE *fp;

    server_fd = socket(AF_INET, SOCK_STREAM, 0);

    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(9000);
    server_addr.sin_addr.s_addr = INADDR_ANY;

    bind(server_fd, (struct sockaddr*)&server_addr, sizeof(server_addr));

    listen(server_fd, 5);

    printf("FTP Server Waiting...\n");

    client_fd = accept(server_fd, NULL, NULL);

    recv(client_fd, filename, sizeof(filename), 0);

    printf("Client requested file: %s\n", filename);

    fp = fopen(filename, "r");

    if(fp == NULL) {
        strcpy(buffer, "File not found");
        send(client_fd, buffer, sizeof(buffer), 0);
    } else {

        while(fgets(buffer, sizeof(buffer), fp) != NULL) {
            send(client_fd, buffer, sizeof(buffer), 0);
        }

        strcpy(buffer, "END");
        send(client_fd, buffer, sizeof(buffer), 0);

        fclose(fp);
    }

    close(client_fd);
    close(server_fd);

    return 0;
}
```

---

# FTP Client

```c id="ftp-real-client"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {

    int sock;
    struct sockaddr_in server_addr;
    char filename[100], buffer[1024];

    sock = socket(AF_INET, SOCK_STREAM, 0);

    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(9000);

    inet_pton(AF_INET, "127.0.0.1", &server_addr.sin_addr);

    connect(sock, (struct sockaddr*)&server_addr, sizeof(server_addr));

    printf("Enter filename: ");
    scanf("%s", filename);

    send(sock, filename, sizeof(filename), 0);

    printf("\nFile Contents:\n");

    while(1) {

        recv(sock, buffer, sizeof(buffer), 0);

        if(strcmp(buffer, "END") == 0)
            break;

        printf("%s", buffer);
    }

    close(sock);

    return 0;
}
```


---

# Run

## Terminal 1

```bash id="ftp-server-run"
./server
```

## Terminal 2

```bash id="ftp-client-run"
./client
```


---

# Run Instructions

Compile:

```bash id="compile-all"
gcc server.c -o server
gcc client.c -o client
```

Run server first:

```bash id="run-server"
./server
```

Then client:

```bash id="run-client"
./client
```

For Windows:

```powershell id="windows-run"
server.exe
client.exe
```
