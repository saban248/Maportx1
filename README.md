# Maportx1

**Maportx1** is a Windows console application written in C++ that demonstrates TCP port scanning with concurrent worker threads. For ports that accept a connection, it attempts to read a short response and prints the result to the console.

> **Authorized use only:** Run this tool only against systems you own or have explicit permission to test. Port scanning may trigger monitoring alerts or violate a network's acceptable-use policy. Keep scan ranges small and approved.

## Features

- TCP connection checks for a user-specified IPv4 address and port range.
- Configurable connection timeout.
- Concurrent scanning using C++ threads.
- Console output for ports that accept connections.
- A basic attempt to read a service response from an open port.

## Technology

- C++
- Windows Sockets (Winsock)
- Visual Studio C++ project (`.sln` / `.vcxproj`)
- Windows console APIs for colored output

## Requirements

- Windows 10 or later (the project targets the Windows SDK).
- Visual Studio 2019 or a compatible Visual Studio installation with **Desktop development with C++**.
- Windows SDK and MSVC toolset compatible with the project (`v142` is configured).
- No third-party package manager or external library is currently required. The project links against the Windows Winsock library (`Ws2_32.lib`).

## Build

1. Clone or download this repository.
2. Open `Maportx1.sln` in Visual Studio.
3. Select `x64` and either `Debug` or `Release`.
4. Choose **Build > Build Solution**.
5. Run the generated executable from Visual Studio or from the build output directory.

The project currently uses a Visual Studio solution rather than CMake. A CMake build is not provided.

## Usage

Run the application. It interactively asks for:

1. IPv4 address
2. Connection timeout (seconds)
3. Starting port
4. Number of ports to scan (range size)
5. Number of worker threads

Example input for a **local machine you own**:

```text
IP address:       127.0.0.1
Timeout:          1
Starting port:    1
Range:            100
Number of threads: 4
```

The range is intended as a count beginning at the starting port. For example, starting at `1` with range `100` should examine ports `1` through `100` if the range handling is configured correctly. Review the implementation before relying on exact boundary behavior; current scan-range partitioning has known limitations.

## Output

The program prints ports for which a TCP connection appears to succeed. It then attempts to send a basic HTTP-like request and read a response. The response is not a reliable service fingerprint: many services do not speak HTTP, and the request format and response buffer are limited.

## Limitations and known issues

This is an educational/learning project and should not be treated as a production-grade scanner. The current source should be reviewed and improved before relying on scan results:

- Input validation is limited; invalid IP addresses, ports, timeouts, and thread counts are not handled robustly.
- The scan-range partitioning and remainder handling need correction and tests to ensure all requested ports are scanned exactly once.
- Socket lifecycle and Winsock initialization/cleanup need stronger error handling. `WSAStartup` is called, but `WSACleanup` is not currently performed.
- Non-blocking connection handling and timeout logic should be reviewed; `connect` is checked in a way that may misclassify results.
- Socket error cases and failed socket creation need consistent handling before sockets are used or closed.
- The response buffer is small and service detection is simplistic.
- Concurrent workers write to the same console and shared scanner state; thread-safety and output coordination should be reviewed.
- The application currently supports IPv4 addresses only.
- Automated unit tests are not included yet.

These limitations are documented so users understand the current implementation and its boundaries.

## Suggested improvements

- Extract input validation and port-range partitioning into testable functions.
- Add unit tests for valid/invalid ranges, boundary ports, and thread partitioning.
- Use RAII wrappers for Winsock sockets and Winsock initialization.
- Implement robust non-blocking connect handling with `select` and explicit error checks.
- Synchronize console output and avoid shared mutable state between workers.
- Add a CI build with MSBuild on Windows.
- Separate port detection from optional service-probing behavior.

## Repository structure

```text
Maportx1/
├── Maportx1.sln
└── Maportx1/
    ├── Maportx1.cpp       # Console input and program entry point
    ├── MapPort.cpp        # TCP connection and scan logic
    ├── MapPort.h          # Scanner class declaration
    ├── Color.cpp          # Windows console color helper
    ├── Color.h
    └── Maportx1.vcxproj   # Visual Studio C++ project
```

## License

No license file is currently included. Unless a license is added, standard copyright restrictions apply and reuse permissions are not explicitly granted.

## Disclaimer

This project is provided for educational purposes. You are responsible for obtaining authorization before scanning any host or network.
