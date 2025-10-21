# Lab 4: UDP File Transfer

**Author:** David Chousal  
**Date:** 10/21/2025  
**Course:** COEN 146L

## Description

This lab implements a simple file transfer system using UDP (User Datagram Protocol). The system consists of two programs that work together to transfer files over a network:

### UDP Server (`udpsTemplate.c`)
The UDP server receives a file from a client and saves it to disk. The server:
- Listens on a specified port for incoming UDP packets
- Receives file data in small packets (10 bytes at a time)
- Sends acknowledgments (ACKs) back to the client for each received packet
- Writes the received data to a destination file
- Terminates when it receives a zero-length packet from the client

### UDP Client (`udpcTemplate.c`)
The UDP client sends a file to the server. The client:
- Connects to a specified server IP address and port
- Reads a source file in chunks of 10 bytes
- Sends each chunk as a UDP packet to the server
- Waits for an acknowledgment from the server after each packet
- Sends a final zero-length packet to signal end of transmission

### Packet Structure
Both programs use a custom packet structure consisting of:
- **Header:** Contains the length of the data (integer)
- **Data:** 10-byte payload containing file content

### Important Notes
- **UDP is connectionless and unreliable** - packets may be lost, duplicated, or arrive out of order
- This implementation uses a simple stop-and-wait mechanism with acknowledgments
- No error recovery or retransmission is implemented in this lab
- In Lab 5, you will implement reliable data transfer mechanisms (timeouts, retransmissions, sequence numbers)

## Compilation

### Compile the Server
```bash
gcc -o udpserver udpsTemplate.c
```

### Compile the Client
```bash
gcc -o udpclient udpcTemplate.c
```

### Compile Both at Once
```bash
gcc -o udpserver udpsTemplate.c && gcc -o udpclient udpcTemplate.c
```

## Running the Programs

### Step 1: Start the Server
The server must be started first to listen for incoming connections.

**Syntax:**
```bash
./udpserver <port> <dstfile>
```

**Parameters:**
- `<port>`: Port number to listen on (e.g., 8080)
- `<dstfile>`: Destination file path where received data will be saved

**Example:**
```bash
./udpserver 8080 received_file.txt
```

The server will display:
```
Waiting for packets to come.....
```

### Step 2: Run the Client
After the server is running, start the client in a separate terminal.

**Syntax:**
```bash
./udpclient <ip> <port> <srcfile>
```

**Parameters:**
- `<ip>`: Server IP address (use `127.0.0.1` for localhost)
- `<port>`: Server port number (must match server's port)
- `<srcfile>`: Source file path to send to the server

**Example:**
```bash
./udpclient 127.0.0.1 8080 source_file.txt
```

## Complete Example

### Terminal 1 (Server):
```bash
$ gcc -o udpserver udpsTemplate.c
$ ./udpserver 8080 received_file.txt
Waiting for packets to come.....
Packet{ header: { len: 10 }, data: "Hello Worl" }
	 Server sending Acknowled 
Packet{ header: { len: 3 }, data: "d!\n" }
	 Server sending Acknowled 
Packet{ header: { len: 0 }, data: "" }
	 Server sending Acknowled 
```

### Terminal 2 (Client):
```bash
$ gcc -o udpclient udpcTemplate.c
$ echo "Hello World!" > source_file.txt
$ ./udpclient 127.0.0.1 8080 source_file.txt
Client sending packet
Packet{ header: { len: 10 }, data: "Acknowled" }
Client sending packet
Packet{ header: { len: 10 }, data: "Acknowled" }
Client sending packet
Packet{ header: { len: 10 }, data: "Acknowled" }
```

### Verify the Transfer:
```bash
$ cat received_file.txt
Hello World!
```

## Testing Over Network

To test between different machines:

1. **On Server Machine:**
   ```bash
   ./udpserver 8080 received_file.txt
   ```

2. **On Client Machine (replace with actual server IP):**
   ```bash
   ./udpclient 192.168.1.100 8080 source_file.txt
   ```

## Troubleshooting

### Port Already in Use
If you get a "bind failed" error, the port may already be in use:
- Try a different port number (e.g., 8081, 8082)
- Or kill the process using that port

### Permission Denied
If you get permission errors:
- Make sure the executables have execute permissions: `chmod +x udpserver udpclient`
- Use ports above 1024 (ports below 1024 require root privileges)

### File Not Found
Make sure the source file exists before running the client:
```bash
ls -l source_file.txt
```

## Clean Up

To remove compiled executables:
```bash
rm udpserver udpclient
```

To remove test files:
```bash
rm received_file.txt source_file.txt
```

## Future Enhancements (Lab 5)

In the next lab, you will implement:
- Timeouts and retransmission mechanisms
- Sequence numbers for packet ordering
- Duplicate packet detection
- Reliable data transfer protocol over UDP
- Performance metrics (throughput, packet loss rate)

