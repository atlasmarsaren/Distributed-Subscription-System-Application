# Distributed Subscription System

A Java client-server learning project that manages subscriptions and login state across three server processes. It explores TCP socket communication, object serialisation and timestamp-based synchronisation without a web framework.

[Watch the project demo](https://youtu.be/NqTfXU1jL_c)

## Features

- Subscribe, unsubscribe, log in and log out through a command-line client.
- Connect the client to a randomly selected server.
- Exchange subscription and login state between peer servers using Java object streams.
- Compare update timestamps before applying received state.
- Handle connections in separate threads and periodically check peer connectivity.

## Architecture

`Client.java` selects one of three servers at startup. Each server handles client requests and exchanges state with its two peers. `MultiStringArrays.java` is the shared serialisable state container.

| Process | Client port | Incoming state-sync ports |
| --- | --- | --- |
| `Server1` | 5001 | 5006, 5007 |
| `Server2` | 5002 | 5004, 5009 |
| `Server3` | 5003 | 5005, 5008 |

All peer connections use `localhost`. The ports above reflect the Java source: 5004 and 5008 are synchronisation ports, not the client ports for Server2 and Server3.

## Run locally

You need a JDK with `javac` and `java` available. The source uses Java 8 language features and the standard library; there is no Maven or Gradle build configuration.

### 1. Clone and compile

```bash
git clone https://github.com/atlasmarsaren/Distributed-Subscription-System-Application.git
cd Distributed-Subscription-System-Application
javac -d out Client.java MultiStringArrays.java Server1.java Server2.java Server3.java
```

The source files declare the package `proje1`, so run the compiled classes using their fully qualified names.

### 2. Start the three servers

Open three terminals in the repository directory:

```bash
# Terminal 1
java -cp out proje1.Server1
```

```bash
# Terminal 2
java -cp out proje1.Server2
```

```bash
# Terminal 3
java -cp out proje1.Server3
```

Start all servers before interacting with the client. Peer connection errors can appear while the other processes are still starting. Keep local ports 5001-5009 available.

### 3. Start the client

In a fourth terminal:

```bash
java -cp out proje1.Client
```

Use a client ID from **1 to 99**. The current client prompt also accepts 100, but the server-side lists are indexed from 0 to 99.

| Menu option | Operation |
| --- | --- |
| 1 - `ABONOL` | Subscribe |
| 2 - `ABONIPTAL` | Unsubscribe |
| 3 - `GIRIS` | Log in |
| 4 - `CIKIS` | Log out |
| 5 | Exit the client |

Example session: choose an ID, subscribe, log in, log out, then unsubscribe. Exit each server process with Ctrl+C when finished.

## Scope and limitations

This is an educational prototype, not a production subscription service. State is held in memory; there is no persistent database, authentication, TLS or consensus protocol. Timestamp comparisons and peer sends are a simple synchronisation approach, not a guarantee of consistency under concurrent updates or failures.

Run it only in a trusted local environment. The listeners use Java object deserialisation and should not be exposed to untrusted networks. Automated tests and performance benchmarks are not included.

## Author

[Atlas Mars Aren](https://www.linkedin.com/in/atlasmarsaren/) · [GitHub](https://github.com/atlasmarsaren)
