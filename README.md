#server socket

import socket
import threading

def handle_client_receive(client_socket, client_address):
    while True:
        try:
            message = client_socket.recv(1024).decode('utf-8')
            if message:
                print(f"Received from")
            else:
                break
        except ConnectionResetError:
            break
    print(f"Connection closed with {client_address}")
    client_socket.close()

def handle_client_send(client_socket, client_address):
    while True:
        try:
            message = input(f"Enter message to send to {client_address}: ")
            client_socket.sendall(message.encode('utf-8'))
        except ConnectionResetError:
            break

def start_server(host='127.0.0.1', port=65432):
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.bind((host, port))
    server_socket.listen()
    print(f"Server listening on {host}:{port}")
    while True:
        client_socket, client_address = server_socket.accept()
        print(f"Connected to {client_address}")
        receive_thread = threading.Thread(target=handle_client_receive, args=(client_socket, client_address))
        send_thread = threading.Thread(target=handle_client_send, args=(client_socket, client_address))
        receive_thread.start()
        send_thread.start()

if __name__ == "__main__":
    start_server()
    
#CLient server
import socket

def start_client(host='127.0.0.1', port=65432):
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client_socket.connect((host, port))
    try:
        while True:
            message = input("Enter message to send (type 'exit' to close): ")
            if message.lower() == 'exit':
                break
            client_socket.sendall(message.encode('utf-8'))
            response = client_socket.recv(1024).decode('utf-8')
            print(f"Received from server: {response}")
    except ConnectionAbortedError:
        print("Connection closed by server.")
    finally:
        client_socket.close()

if __name__ == "__main__":
    start_client()
    
