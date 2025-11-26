## Code

### server1

```
#server1.py
import socket

HOST = "0.0.0.0"
PORT = 9101
SERVER_ID = 1


def main():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        s.bind((HOST, PORT))
        s.listen()
        print(f"Server-{SERVER_ID} listening on {HOST}:{PORT}")

        while True:
            conn, addr = s.accept()
            with conn:
                print(f"Server-{SERVER_ID}: connection from {addr}")
                while True:
                    data = conn.recv(1024)
                    if not data:
                        break
                    msg = data.decode().strip()
                    print(f"Server-{SERVER_ID}: received '{msg}'")
                    # 回傳「原本的字 + server-id」
                    response = f"{msg}server-{SERVER_ID}"
                    conn.sendall(response.encode())


if __name__ == "__main__":
    main()
```

### server2
```
# server2.py
import socket

HOST = "0.0.0.0"
PORT = 9102
SERVER_ID = 2


def main():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        s.bind((HOST, PORT))
        s.listen()
        print(f"Server-{SERVER_ID} listening on {HOST}:{PORT}")

        while True:
            conn, addr = s.accept()
            with conn:
                print(f"Server-{SERVER_ID}: connection from {addr}")
                while True:
                    data = conn.recv(1024)
                    if not data:
                        break
                    msg = data.decode().strip()
                    print(f"Server-{SERVER_ID}: received '{msg}'")
                    response = f"{msg}server-{SERVER_ID}"
                    conn.sendall(response.encode())


if __name__ == "__main__":
    main()
```

### proxy

```
# proxy.py
import socket
import threading

PROXY_HOST = "0.0.0.0"
PROXY_PORT = 9000

SERVERS = [
    ("20.2.140.157", 9101),  # server-1
    ("20.2.92.111", 9102),  # server-2
]


def forward_to_server(data: bytes, server_addr):
    """和某一台 server 建立連線，把 data 丟出去並拿回回應"""
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect(server_addr)
        s.sendall(data)
        response = s.recv(1024)
    return response


def handle_client(conn, addr):
    print(f"Proxy: client {addr} connected")

    # 每個 client handler 都有**自己的** round-robin index
    next_index = 0

    try:
        while True:
            data = conn.recv(1024)
            if not data:
                break
            msg = data.decode().strip()

            # 選擇這次要用的 server
            server_addr = SERVERS[next_index]
            server_id = next_index + 1
            print(f"Proxy: from {addr} -> server-{server_id}: '{msg}'")

            # 更新這個 client 的 round-robin pointer
            next_index = (next_index + 1) % len(SERVERS)

            # 把資料轉送到選到的 server，拿回回應後再回給 client
            response = forward_to_server(data, server_addr)
            conn.sendall(response)
    finally:
        print(f"Proxy: client {addr} disconnected")
        conn.close()


def main():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        s.bind((PROXY_HOST, PROXY_PORT))
        s.listen()
        print(f"Proxy listening on {PROXY_HOST}:{PROXY_PORT}")

        while True:
            conn, addr = s.accept()
            # 一個 client 一個 thread，同時支援多個 client
            t = threading.Thread(target=handle_client, args=(conn, addr), daemon=True)
            t.start()


if __name__ == "__main__":
    main()
```

### client1
```
# client1.py
import socket

HOST = "20.243.160.11"   # proxy 的位址
PORT = 9000          # proxy 的 port
CLIENT_ID = 1


def main():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((HOST, PORT))
        print(f"Client {CLIENT_ID} connected to proxy {HOST}:{PORT}")

        while True:
            msg = input(f"[Client {CLIENT_ID}] Input message (empty to quit): ")
            if not msg:
                break

            s.sendall(msg.encode())
            data = s.recv(1024)
            if not data:
                print("proxy closed connection")
                break

            print(f"[Client {CLIENT_ID}] received: {data.decode().strip()}")

    print(f"Client {CLIENT_ID} closed.")


if __name__ == "__main__":
    main()
```

### client2
```
# client2.py
import socket

HOST = "20.243.160.11"   # proxy 的位址
PORT = 9000          # proxy 的 port
CLIENT_ID = 2


def main():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((HOST, PORT))
        print(f"Client {CLIENT_ID} connected to proxy {HOST}:{PORT}")

        while True:
            msg = input(f"[Client {CLIENT_ID}] Input message (empty to quit): ")
            if not msg:
                break

            s.sendall(msg.encode())
            data = s.recv(1024)
            if not data:
                print("proxy closed connection")
                break

            print(f"[Client {CLIENT_ID}] received: {data.decode().strip()}")

    print(f"Client {CLIENT_ID} closed.")


if __name__ == "__main__":
    main()
```
