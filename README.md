# Project 4: Inter-Process Communication with FIFOs

## 🧩 Overview

This project extends a previously built restricted shell (`rsh`) by adding basic inter-process communication (IPC) capabilities using **named pipes (FIFOs)**. The enhancement introduces a new built-in command, `sendmsg`, which allows users running different instances of the shell to send text messages to one another in real time.

The goal is to demonstrate user-level message passing using standard Linux system calls, without relying on sockets or complex networking — just pure FIFO-based IPC.

---

## ✉️ Key Feature: `sendmsg`

The `sendmsg` command lets one shell user send a message to another. Example:

```bash
rsh> sendmsg user2 hello there!

## 🏗️ System Architecture

The system consists of three major components:

### 🔧 Server (`server.c`)

- A background process that listens on a common FIFO (`serverFIFO`).
- It receives messages from clients and forwards them to the appropriate user’s FIFO (e.g., `user1`, `user2`).
- Acts as the central hub for routing messages between users.

### 🖥️ Client Shell (`rsh.c`)

An interactive restricted shell that supports:

- Basic commands (`cp`, `ls`, `pwd`, etc.)
- Built-in shell features (`cd`, `exit`, `help`)
- Messaging (`sendmsg`)

Each client instance identifies itself by a username passed as an argument and listens for messages on a FIFO with the same name.

### 🧵 FIFOs (Named Pipes)

- `serverFIFO`: Shared FIFO where clients send their outbound messages for the server to dispatch.
- `user1`, `user2`, etc.: Individual FIFOs where each client receives messages directed at them.

## 💬 Communication Flow

- A user types `sendmsg user2 hello!` in their `rsh` shell.
- The client writes this message to `serverFIFO`.
- The server reads the message, parses the target username, and writes the message to `user2`’s FIFO.
- The `rsh` shell running as `user2` reads the message and displays it instantly in the shell.

> If the target user's FIFO does not exist or the user is not online, the message is dropped (no acknowledgment or delivery guarantee is implemented).
