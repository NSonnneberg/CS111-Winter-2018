# Project 1B Notes, Tips, and Tricks

## Client Server Connections
Client Code:
```c
int main()
{
	int socketfd;
	char in[100];
	char out[100];

	struct sockaddr_in client;

	socketfd = socket(AF_INET, SOCK_STREAM, 0);

	client.sin_addr.s_addr = inet_addr("127.0.0.1");
	client.sin_family = AF_INET;
	client.sin_port = 31337;

	connect(socketfd, (struct sockaddr * ) &client, sizeof(client)); 
	// connects the socket to the server based on the settings specified in your client struct  

	while(1)
	{
		memset(in, 0, 100*sizeof(char));  //important
		memset(out, 0, 100*sizeof(char));

		printf("Send to server ");
		fgets(out, sizeof(out), stdin);
		write(socketfd, out, sizeof(out));
		read(socketfd, in, 100);
		printf("Server replied \"%s\"\n", in);
	}
}
```

Server Code:
```c
int main()
{
	char in[100];
	char out[100];
	int listenfd;
	int commfd;
	struct sockaddr_in server;

	listenfd = socket(AF_INET, SOCK_STREAM, 0);

	server.sin_family = AF_INET;
	server.sin_addr.s_addr = INADDR_ANY;
	server.sin_port = 31337; // sets port for client to connect to. for project make sure its > 1024

	bind(listenfd, (struct sockaddr *) &server, sizeof(server));
	// binds server to a port so clients can connect to you

	listen(listenfd, 3);
	// opens up 3 connections for looking for information

	commfd = accept(listenfd, (struct sockaddr *) NULL, NULL);
	// needed for TCP connections so that the files can be accepted for reading
	// the 2 NULL arguments can be replaced with options for handling the accepting of packets that are not
	// needed for such a simple example 

	while(1);
	{
		memset(in, 0, 100*sizeof(char));
		memset(out, 0, 100*sizeof(char));

		read(commfd, in, 100);
		in[strcspn(in, "\r\n")] = 0;
		printf("Received [%s]", in);
		sprintf(out, "Data [%s] was received", in);
		write(commfd, out, sizeof(out));
	}
}
```

## Compression
	* If you send data (before a `write()`), you need to `deflate()`
	* If you receive data (after a `read()`, you need to `inflate()` 

