# [SecureSocket](https://www.unrealengine.com/marketplace/en-US/product/securesocket) - Documentation
![Secure socket features](https://github.com/Pandoa/SecureSocket/blob/master/Images/Show0.png?raw=true)
# Table of Content
1. [Blueprint](#1-blueprint)</br>
    1.1. [TCP](#11-tcp)</br>
    1.2. [UDP](#12-udp)</br>
2. [C++](#2-c)</br>
    2.1. [TCP](#21-tcp)</br>
    2.2. [UDP](#22-udp)</br>
 3. [Support](#3-support)
# 1. Blueprint
## 1.1. TCP
### 1.1.1. Creating a TCP socket
You can create a TCP socket with the function `Create TCP socket`. You must save the socket to a variable be sure it is not garbage collected:

![Create socket example](https://github.com/Pandoa/SecureSocket/blob/master/Images/CreateSocket.png?raw=true)
### 1.1.1. Listening to events
Before connecting to the server, you should bind the events you plan to use to be sure to not miss one. The available events are the following:
|Event Name|Signature|Description|
|:---|:---|:--|
||||
||||
||||
### 1.1.2. Connecting to the server
To easily connect to the server, you can use an helper node that automatically binds the `OnConnected` and `OnConnectionFailed` events for you:

![Connect to server example](https://github.com/Pandoa/SecureSocket/blob/master/Images/ConnectToServer.png?raw=true)
The same node exists to connect to a server with SSL/TLS:

![Connect to server SSL example](https://github.com/Pandoa/SecureSocket/blob/master/Images/ConnectToServerSSL.png?raw=true)
The `Connected` execution pin is called when the connection and the upgrade is over and the socket is ready to communicate trough an SSL/TLS connection.
### 1.1.3. Upgrading the connection (SSL/TLS)
You can upgrade an unencrypted connection to SSL/TLS at any time with the `Upgrade TCP connection to SSL/TLS` node:

![SSL upgrade TCP socket](https://github.com/Pandoa/SecureSocket/blob/master/Images/UpgradeConnection.png?raw=true)
### 1.1.4. Sending messages
You can send binary or string messages easily trough your TCP socket:

![Send TCP example](https://github.com/Pandoa/SecureSocket/blob/master/Images/SendDataTcp.png?raw=true)

| :information_source:|When you send a string message, the `As ANSI` option allows you to choose how the string is converted to binary. If `As ANSI` is set to `true`, each character will use 8 bits. If it is set to `false` each character will use 16 bits.|
|:---|:---|

### 1.1.5. Closing the connection
## 1.2. UDP
### 1.2.1. Sending messages
### 1.2.2. Receiving messages
### 1.2.3. DTLS
# 2. C++
## 2.1. TCP
## 2.2. UDP
# 3. Support
If you need help, have a feature request or experience troubles, please contact us at [pandores.marketplace@gmail.com](mailto:pandores.marketplace+SecureSocket@gmail.com?subject=Secure%20Socket%20-%20).
