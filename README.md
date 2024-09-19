# README: Torrent File Exchange System - C Socket Programming

### Project Overview

This project implements a simplified version of a torrent-like file exchange system using C socket programming. The system consists of:
- A **server program** (`srv0496.c`) that acts as a seeder, capable of handling up to 100 clients simultaneously. It serves files from a shared directory.
- A **client program** (`cli2236.c`) that can download files from or upload files to the server. The client supports resuming interrupted downloads.

### Prerequisites

- **C Compiler**: GCC or any C compiler.
- **Linux/Unix Environment**: The program was designed for a Unix-like environment. It should also run on Windows using tools like Cygwin or WSL (Windows Subsystem for Linux).
  
### Source Files

- `srv0496.c`: Server-side program that handles file transfer (upload/download) and serves multiple clients.
- `cli2236.c`: Client-side program for interacting with the server to download or upload files.

### Compilation Instructions

**1. Compile the Server Program:**
```bash
gcc srv0496.c -o server
```

**2. Compile the Client Program:**
```bash
gcc cli2236.c -o client
```

### Running the Programs

#### Server Program Instructions

1. **Create a directory** to hold files that the server will serve to clients. This directory must be named `shared_files` and should contain at least 50 files, each at least 10MB in size.
   ```bash
   mkdir shared_files
   ```
   
2. **Populate the `shared_files/` directory** with files that you want to make available for download.

3. **Start the server** using the following command:
   ```bash
   ./server
   ```

4. The server will start listening for client connections on **IP 127.0.0.1** (localhost) and **port 2236**. It will handle up to 100 clients simultaneously using multi-processing.

#### Client Program Instructions

1. **Run the client program** with the following command:
   ```bash
   ./client <client_ip_address>
   ```
   Example:
   ```bash
   ./client 127.0.0.1
   ```

2. Once connected to the server, you will be prompted to choose between downloading or uploading a file:
   - **Option 1**: Download a file from the server.
   - **Option 2**: Upload a file to the server.

3. If you select to **download**, you will be presented with a list of available files on the server. Choose a file by entering the corresponding number. The client will begin downloading the file, showing progress in percentage.

4. If you select to **upload**, you will be prompted to provide the path to the file you want to upload to the server. The file will be transferred and saved to the server’s `shared_files/` directory.

#### Error Handling

Both the server and client programs handle common errors, such as:
- Connection issues.
- File-related errors (e.g., file not found).
- Incomplete downloads.

If an error occurs, an appropriate error message will be displayed in the terminal.

### File Logging

- The server logs every client connection and file transfer activity in a log file named `log_srv0496.txt`. The log file contains the client IP address, port number, file requested, and whether the transfer was successful.

### Notes

- The server runs on **port 2236**, and the client binds to **port 1496**.
- The client and server communicate via TCP sockets.
- The client supports **resuming interrupted downloads**. If a file is partially downloaded, it will resume from where it left off.

### Example Interaction

**Server Output:**
```
Server started on IP: 127.0.0.1, Port: 2236
Waiting for client connections...
Client connection from IP 127.0.0.1, Port: 1496
```

**Client Output:**
```
Connecting to server at 127.0.0.1:2236...
Connected to the server.
Choose action: 1 for download, 2 for upload: 1
Available files:
1. file1.txt
2. file2.jpg
3. file3.mp4
Enter the file number to download: 2
Downloading file2.jpg...
Progress: 1%
Progress: 2%
.
.
.
Progress: 100%
Download complete. File saved as file2.jpg
```

---

### Additional Notes

- Make sure that both the server and client machines are on the same network or adjust the IP address accordingly.
- The project supports downloading and uploading files of any type (text, binary, etc.).

---

### Assumptions Made During Implementation:

1. **Fixed Directories**:
   - **Server's file directory**: It was assumed that the server will always have a `shared_files/` directory, which contains files to serve to the clients. The server program does not dynamically create this directory; the user must manually set it up before running the server.
   
2. **File Availability**:
   - The client program assumes that the files listed by the server are always available for download. It doesn't handle the case where a file might be deleted from the server directory between listing and the client requesting the download.
   
3. **Localhost Testing**:
   - The programs assume that the server and client will most commonly be tested on the same machine using the localhost (`127.0.0.1`) IP address. However, it should work on different machines if they are on the same network.
   
4. **Fixed Ports**:
   - The server always runs on port `2236` and the client binds to port `1496`. No dynamic port allocation is performed.
   
5. **Minimum Number of Files**:
   - The server assumes that the directory will contain at least 50 files (each 10MB or larger) for testing purposes. However, the program will work with fewer files as well.

6. **Chunk Size**:
   - The file transfer uses a fixed chunk size of 1024 bytes for both upload and download processes.

7. **File Permissions**:
   - It was assumed that the server will have the necessary write permissions for the directory to save uploaded files and the necessary read permissions to serve files.

8. **TCP Connection**:
   - The program assumes that the network is stable and reliable enough to maintain a TCP connection between the server and client for the duration of file transfer. The client does support resuming interrupted downloads, but it assumes that unintentional interruptions would be network-related.

### Challenges Encountered During Implementation:
   
1. **Resuming Downloads**:
   - Implementing the functionality for resuming interrupted downloads required the client to send its current file size (in case of partial downloads) and the server to adjust its file read pointer accordingly. This involved careful use of `lseek()` on the server to jump to the correct file position and `open()` flags on the client to ensure it resumed appending to the existing file.
   
2. **Managing Large Files**:
   - Since the server is required to serve files of at least 10MB, the file handling and chunk-based transfer system had to ensure efficient memory usage. Reading and writing files in fixed-size chunks (1024 bytes) helped manage memory constraints, but handling larger files (e.g., 1GB) was a challenge in terms of ensuring that the client could handle a long-running download process without running into memory issues.
   
3. **File Uploads**:
   - Adding file upload functionality was an optional feature but required careful coordination between the client and server, especially when ensuring that the server saved uploaded files correctly with the proper file names and sizes.
   
4. **Progress Display**:
   - Implementing a real-time progress bar for downloads was somewhat tricky. The client had to calculate the download progress as a percentage and update the user interface without overwhelming the terminal with too many print statements. This required balancing between frequent updates and maintaining smooth performance.
   
5. **Logging System**:
   - Ensuring that the server correctly logged each client’s activity (IP, port, file requested, success/failure) was another challenge. Since multiple processes were accessing the log file concurrently, there was potential for race conditions. This was mitigated by ensuring that each process handled its own logging and closed the file properly after use.

By overcoming these challenges, the program was able to meet the assignment requirements and function reliably in a multi-client, file-exchange scenario.
