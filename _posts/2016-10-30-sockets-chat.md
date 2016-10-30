---
layout: post
title: C++简单聊天程序
modified: 2016-10-30
tags: [C++]

---

```c++
#include "stdafx.h"
#include <iostream>
#include <winsock2.h>
#include <ws2tcpip.h>
#include <string>
#include <thread>
#include <vector>

#pragma comment (lib, "Ws2_32.lib")

#define IP_ADDRESS "192.168.1.141"
#define DEFAULT_PORT "4869"
#define BUFFER_SIZE 1024

struct ClientType {
	int id;
	SOCKET socket;
};

const char OPTION_VALUE = 1;
const int MAX_CLIENTS = 5;

//Function Prototypes
int processClient(ClientType &newClient, std::vector<ClientType> &clientsArray, std::thread &thread);
int main();

int processClient(ClientType &newClient, std::vector<ClientType> &clientsArray, std::thread &thread) {
	std::string message = "";
	char tempMessage[BUFFER_SIZE] = "";

	//Session
	while (1) {
		memset(tempMessage, 0, BUFFER_SIZE);

		if (newClient.socket != 0) {
			int iResult = recv(newClient.socket, tempMessage, BUFFER_SIZE, 0);

			if (iResult != SOCKET_ERROR) {
				if (strcmp("", tempMessage))
					message = "Client #" + std::to_string(newClient.id) + ": " + tempMessage;

				std::cout << message.c_str() << std::endl;

				//Broadcast that message to the other clients
				for (int i = 0; i < MAX_CLIENTS; i++) {
					if (clientsArray[i].socket != INVALID_SOCKET)
						if (newClient.id != i)
							iResult = send(clientsArray[i].socket, message.c_str(), strlen(message.c_str()), 0);
				}
			}
			else {
				message = "Client #" + std::to_string(newClient.id) + " Disconnected";

				std::cout << message << std::endl;

				closesocket(newClient.socket);
				closesocket(clientsArray[newClient.id].socket);
				clientsArray[newClient.id].socket = INVALID_SOCKET;

				//Broadcast the disconnection message to the other clients
				for (int i = 0; i < MAX_CLIENTS; i++) {
					if (clientsArray[i].socket != INVALID_SOCKET)
						iResult = send(clientsArray[i].socket, message.c_str(), strlen(message.c_str()), 0);
				}

				break;
			}
		}
	} //end while

	thread.detach();

	return 0;
}

int main() {
	WSADATA wsaData;
	struct addrinfo hints;
	struct addrinfo *server = NULL;
	SOCKET serverSocket = INVALID_SOCKET;
	std::string message = "";
	std::vector<ClientType> client(MAX_CLIENTS);
	int numberOfClients = 0;
	int tempId = -1;
	std::thread myThreads[MAX_CLIENTS];

	//Initialize Winsock
	WSAStartup(MAKEWORD(2, 2), &wsaData);

	//Setup hints
	ZeroMemory(&hints, sizeof(hints));
	hints.ai_family = AF_INET;
	hints.ai_socktype = SOCK_STREAM;
	hints.ai_protocol = IPPROTO_TCP;
	hints.ai_flags = AI_PASSIVE;

	//Setup Server
	getaddrinfo(static_cast<PCSTR>(IP_ADDRESS), DEFAULT_PORT, &hints, &server);

	//Create a listening socket for connecting to server
	serverSocket = socket(server->ai_family, server->ai_socktype, server->ai_protocol);

	//Setup socket options
	setsockopt(serverSocket, SOL_SOCKET, SO_REUSEADDR, &OPTION_VALUE, sizeof(int)); 
	setsockopt(serverSocket, IPPROTO_TCP, TCP_NODELAY, &OPTION_VALUE, sizeof(int)); 
	bind(serverSocket, server->ai_addr, (int)server->ai_addrlen);

	//Listen for incoming connections.
	std::cout << "Listening..." << std::endl;
	listen(serverSocket, SOMAXCONN);

	//Initialize the client list
	for (int i = 0; i < MAX_CLIENTS; i++) {
		client[i] = { -1, INVALID_SOCKET };
	}

	while (1) {

		SOCKET incoming = INVALID_SOCKET;
		incoming = accept(serverSocket, NULL, NULL);

		if (incoming == INVALID_SOCKET) continue;

		//Reset the number of clients
		numberOfClients = -1;

		//Create a temporary id for the next client
		tempId = -1;
		for (int i = 0; i < MAX_CLIENTS; i++) {
			if (client[i].socket == INVALID_SOCKET && tempId == -1) {
				client[i].socket = incoming;
				client[i].id = i;
				tempId = i;
			}

			if (client[i].socket != INVALID_SOCKET)
				numberOfClients++;

			//std::cout << client[i].socket << std::endl;
		}

		if (tempId != -1) {
			//Send the id to that client
			std::cout << "Client #" << client[tempId].id << " Accepted" << std::endl;
			message = std::to_string(client[tempId].id);
			send(client[tempId].socket, message.c_str(), strlen(message.c_str()), 0);

			//Create a thread process for that client
			myThreads[tempId] = std::thread(processClient, std::ref(client[tempId]), std::ref(client), std::ref(myThreads[tempId]));
		}
		else {
			message = "Server is full";
			send(incoming, message.c_str(), strlen(message.c_str()), 0);
			std::cout << message << std::endl;
		}
	} //end while


	  //Close listening socket
	closesocket(serverSocket);

	//Close client socket
	for (int i = 0; i < MAX_CLIENTS; i++) {
		myThreads[i].detach();
		closesocket(client[i].socket);
	}

	//Clean up Winsock
	WSACleanup();
	std::cout << "Program has ended successfully" << std::endl;

	system("pause");
	return 0;
}
```

```c++
#include "stdafx.h"
#include <winsock2.h>
#include <ws2tcpip.h>
#include <iostream>
#include <string>
#include <thread>

using namespace std;

#pragma comment (lib, "Ws2_32.lib")
        
#define IP_ADDRESS "192.168.1.141"
#define DEFAULT_PORT "4869"
#define BUFFER_SIZE 1024 

struct ClientType {
	SOCKET socket;
	int id;
	char receivedMessage[BUFFER_SIZE];
};

int processClient(ClientType &newClient);
int main();

int processClient(ClientType &newClient) {
	while (1) {
		memset(newClient.receivedMessage, 0, BUFFER_SIZE);

		if (newClient.socket != 0) {
			int iResult = recv(newClient.socket, newClient.receivedMessage, BUFFER_SIZE, 0);

			if (iResult != SOCKET_ERROR)
				cout << newClient.receivedMessage << endl;
			else {
				cout << "recv() failed: " << WSAGetLastError() << endl;
				break;
			}
		}
	}

	if (WSAGetLastError() == WSAECONNRESET)
		cout << "The server has disconnected" << endl;

	return 0;
}

int main() {
	WSAData wsadata;
	struct addrinfo *result = NULL, *ptr = NULL, hints;
	string sentMessage = "";
	ClientType client = { INVALID_SOCKET, -1, "" };
	int iResult = 0;
	string message;

	cout << "Starting Client...\n";

	// Initialize Winsock
	iResult = WSAStartup(MAKEWORD(2, 2), &wsadata);
	if (iResult != 0) {
		cout << "WSAStartup() failed with error: " << iResult << endl;
		return 1;
	}

	ZeroMemory(&hints, sizeof(hints));
	hints.ai_family = AF_UNSPEC;
	hints.ai_socktype = SOCK_STREAM;
	hints.ai_protocol = IPPROTO_TCP;

	cout << "Connecting...\n";

	// Resolve the server address and port
	iResult = getaddrinfo(static_cast<PCSTR>(IP_ADDRESS), DEFAULT_PORT, &hints, &result);
	if (iResult != 0) {
		cout << "getaddrinfo() failed with error: " << iResult << endl;
		WSACleanup();
		system("pause");
		return 1;
	}

	// Attempt to connect to an address until one succeeds
	for (ptr = result; ptr != NULL; ptr = ptr->ai_next) {

		// Create a SOCKET for connecting to server
		client.socket = socket(ptr->ai_family, ptr->ai_socktype,
							   ptr->ai_protocol);
		if (client.socket == INVALID_SOCKET) {
			cout << "socket() failed with error: " << WSAGetLastError() << endl;
			WSACleanup();
			system("pause");
			return 1;
		}

		// Connect to server.
		iResult = connect(client.socket, ptr->ai_addr, (int)ptr->ai_addrlen);
		if (iResult == SOCKET_ERROR) {
			closesocket(client.socket);
			client.socket = INVALID_SOCKET;
			continue;
		}
		break;
	}

	freeaddrinfo(result);

	if (client.socket == INVALID_SOCKET) {
		cout << "Unable to connect to server!" << endl;
		WSACleanup();
		system("pause");
		return 1;
	}

	cout << "Successfully Connected" << endl;

	//Obtain id from server for this client;
	recv(client.socket, client.receivedMessage, BUFFER_SIZE, 0);
	message = client.receivedMessage;

	if (message != "Server is full") {
		client.id = atoi(client.receivedMessage);

		thread my_thread(processClient, client);

		while (1) {
			getline(cin, sentMessage);
			iResult = send(client.socket, sentMessage.c_str(), strlen(sentMessage.c_str()), 0);

			if (iResult <= 0) {
				cout << "send() failed: " << WSAGetLastError() << endl;
				break;
			}
		}

		//Shutdown the connection since no more data will be sent
		my_thread.detach();
	}
	else
		cout << client.receivedMessage << endl;

	cout << "Shutting down socket..." << endl;
	iResult = shutdown(client.socket, SD_SEND);
	if (iResult == SOCKET_ERROR) {
		cout << "shutdown() failed with error: " << WSAGetLastError() << endl;
		closesocket(client.socket);
		WSACleanup();
		system("pause");
		return 1;
	}

	closesocket(client.socket);
	WSACleanup();
	system("pause");
	return 0;
}
```

