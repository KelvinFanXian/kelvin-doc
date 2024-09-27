# Data Link Layer

## Data-link Control & Protocols

### Flow Control

### Error Control

### Go-Back-N ARQ

### Selective Repeat ARQ





# Network Layer

## Network Layer Protocols

### Address Resolution Protocol

### Internet Control Message Protocol

### Internet Protocol Version 4

### Internet Protocol Version 6

### 



# Transport Layer

## Transmission Control Protocol

## User Datagram Protocol









# Application Layer

## Application Protocols

### Domain Name System

### Simple Mail Transfer Protocol

### File Transfer Protocol

### Post Office Protocol

### Hyper Text Transfer Protocol





# 2.2.3 Http Message Format

```bash
GET /somedir/page.html HTTP/1.1
Host: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: fr
```



- Request line
  - Method field
  - URL field
  - version field
- header line
- Entity body

## HEAD Method

- Use for debugging

## PUT Method

- Used in conjunction with Web publishing tools.
  - It allows a user to upload an object to a specific path(directory) on specific Web





# 2.5.3 DNS Records and Messages

## RRs

resource records (RRs)

## TTL

time to live



## nslookup

to send a DNS query message directyly from the host you're working on to some DNS server



# 2.6 Peer-to-Peer Applicatioin



# 2.6.1 P2P File Distribution

## Scalability of P2P Architecture

## BitTorrent



# 2.6.2 Distributed Hash Tables (DHTs)

## Circular DHT







# 2.7 Socket Programming: Creating Network Applications



## 2.7.1 Socket Programming with UDP

### UDPClient.py

```python
from socket import *
serverName = 'hostname'
serverPort = 12000
clientSocket = socket(socket.AF_INET, socket.SOCK_DGRAM)
message = raw_input('Input lowercase sentence:')
clientSocket.sendto(message, (serverName, serverPort))
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
clientSocket.close()
```



### UDPServer.py

```python
from socket import *
serverPort = 12000
serverSocket = socket(AF_INET, SOCK_DGRAM)
serverSocket.bind(('', serverPort))
print 'The server is ready to receive'
while 1:
  message, clientAddress = serverSocket.recvfrom(2048)
  modifiedMessage = message.upper()
  serverSocket.sendto(modifiedMessage, clientAddress)
```



## 2.7.2 Socket Programming with TCP

<img src="https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20230519101819526.png" alt="image-20230519101819526" style="zoom:40%;" />

### TCPClient.py

```python
from socket import *
serverName = 'servername'
serverPort = 12000
clientSocket = socket(AF_INET, SOCKET_STREAM)
clientSocket.connect((serverName, serverPort))
sentence = raw_input('Input lowercase sentence:')
clientSocket.send(sentence)
modifiedSentence = clientSocket.recv(1024)
print 'From Server:', modifiedSentence
clientSocket.close()
```

### TCPServer.py

```python
from socket import *
serverPort = 12000
serverSocket = socket(AF_INET,SOCK_STREAM)
serverSocket.bind(('', serverPort))
serverSocket.listen(1)
print 'The Server is ready to receive'
while 1:
  connectionSocket, addr = serverSocket.accept()
  sentenc = connectionSocket.recv(1024)
  capitalizedSentence = sentence.upper()
  connectionSocket.send(capitalizedSentence)
  connectionSocket.close()
```



# 2.8 Summary



# Homework Problems and Questions

- https://quizlet.com/55365136/computer-networking-chapter-2-review-questions-flash-cards/

## Chapter 2 Review Questions

### SECTION 2.1

```bash
R1. List five nonproprietary Internet applications and the application-layer protocols that they use.
R2. What is the difference between network architecture and application architecture?
R3. For a communication session between a pair of processes, which process is the client and which is the server?
R4. For a P2P file-sharing application, do you agree with the statement, “There is no notion of client and server sides of a communication session”? Why or why not?
R5. What information is used by a process running on one host to identify a process running on another host?
R6. Suppose you wanted to do a transaction from a remote client to a server as fast as possible. Would you use UDP or TCP? Why?
R7. Referring to Figure 2.4, we see that none of the applications listed in Figure 2.4 requires both no data loss and timing. Can you conceive of an application that requires no data loss and that is also highly time-sensitive?
R8. List the four broad classes of services that a transport protocol can provide. For each of the service classes, indicate if either UDP or TCP (or both) pro- vides such a service.
R9. Recall that TCP can be enhanced with SSL to provide process-to-process security services, including encryption. Does SSL operate at the transport layer or the application layer? If the application developer wants TCP to be enhanced with SSL, what does the developer have to do?
```



### SECTIONS 2.2–2.5

```bash
R10. What is meant by a handshaking protocol?
R11. WhydoHTTP,FTP,SMTP,andPOP3runontopofTCPratherthanonUDP?
R12. Consider an e-commerce site that wants to keep a purchase record for each of its customers. Describe how this can be done with cookies.
R13. Describe how Web caching can reduce the delay in receiving a requested object. Will Web caching reduce the delay for all objects requested by a user or for only some of the objects? Why?
R14. Telnet into a Web server and send a multiline request message. Include in the request message the If-modified-since: header line to force a responsemessagewiththe304 Not Modifiedstatuscode.
R15. Why is it said that FTP sends control information “out-of-band”?
R16. Suppose Alice, with a Web-based e-mail account (such as Hotmail or gmail), sends a message to Bob, who accesses his mail from his mail server using POP3. Discuss how the message gets from Alice’s host to Bob’s host. Be sure to list the series of application-layer protocols that are used to move the mes- sage between the two hosts.
R17. Print out the header of an e-mail message you have recently received. How many Received: header lines are there? Analyze each of the header lines in the message.
R18. From a user’s perspective, what is the difference between the download-and- delete mode and the download-and-keep mode in POP3?
R19. Is it possible for an organization’s Web server and mail server to have exactly the same alias for a hostname (for example, foo.com)? 
What would be the type for the RR that contains the hostname of the mail server?
R20. Look over your received emails, and examine the header of a message sent from a user with an .edu email address. Is it possible to determine from the header the IP address of the host from which the message was sent? Do the same for a message sent from a gmail account.
```

### SECTION 2.6

```bash
R21. In BitTorrent, suppose Alice provides chunks to Bob throughout a 30-second interval. Will Bob necessarily return the favor and provide chunks to Alice in this same interval? Why or why not?
R22. Consider a new peer Alice that joins BitTorrent without possessing any chunks. Without any chunks, she cannot become a top-four uploader for any of the other peers, since she has nothing to upload. How then will Alice get her first chunk?
R23. What is an overlay network? Does it include routers? What are the edges in the overlay network?
R24. Consider a DHT with a mesh overlay topology (that is, every peer tracks all peers in the system). What are the advantages and disadvantages of such a design? What are the advantages and disadvantages of a circular DHT (with no shortcuts)?
R25. List at least four different applications that are naturally suitable for P2P architectures. (Hint: File distribution and instant messaging are two.)
```



### SECTION 2.7

```bash
R26. InSection2.7,theUDPserverdescribedneededonlyonesocket,whereasthe TCP server needed two sockets. Why? If the TCP server were to support n simultaneous connections, each from a different client host, how many sockets would the TCP server need?
R27. For the client-server application over TCP described in Section 2.7, why must the server program be executed before the client program? For the client- server application over UDP, why may the client program be executed before the server program?
```



# wireshark

- https://gaia.cs.umass.edu/kurose_ross/wireshark.php



```bash
构造方法注入（constructor injection）
setter方法注入（setter injection）
接口注入（interface injection）
```



































