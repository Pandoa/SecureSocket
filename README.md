# [SecureSocket](https://www.unrealengine.com/marketplace/en-US/product/securesocket) - Documentation
![Secure socket features](https://github.com/Pandoa/SecureSocket/blob/master/Images/Show0.png?raw=true)
# Table of Content
1. [Blueprint](#1-blueprint)</br>
    1.1. [TCP](#11-tcp)</br>
    1.2. [UDP](#12-udp)</br>
2. [C++](#2-c)</br>
    2.1. [Includes](#21-includes)</br>
    2.2. [TCP](#22-tcp)</br>
    2.3. [UDP](#23-udp)</br>
 3. [Support](#3-support)
# 1. Blueprint
## 1.1. TCP
### 1.1.1. Creating a TCP socket
You can create a TCP socket with the function `Create TCP socket`. You must save the socket to a variable to be sure it is not garbage collected:

![Create socket example](https://github.com/Pandoa/SecureSocket/blob/master/Images/CreateSocket.png?raw=true)
### 1.1.2. Listening to events
Before connecting to the server, you should bind the events you plan to use to be sure to not miss one. The available events are the following:
|Event Name|Signature|Description|
|:---|:---|:--|
|`OnMessage`|`void Func(const TArray<uint8>& Data)`|Called when the server sent us data.|
|`OnConnected`|`void Func()`|Called when the TCP socket is connected to the server. |
|`OnConnectionError`|`void Func()`|Called when the TCP failed to connect to the server.|
|`OnClosed`|`void Func()`|Called when the connection is closed.|
|`OnUpgradeSuccess`|`void Func()`|Called when the connection has been upgraded to SSL/TLS.|
|`OnUpgradeFailed`|`void Func(const FString& Error)`|Called when the connection failed to upgrade to SSL/TLS.|

| :information_source:|Some nodes automatically bind the events they use to an execution pin.|
|:---|:---|
### 1.1.3. Connecting to the server
To easily connect to the server, you can use an helper node that automatically binds the `OnConnected` and `OnConnectionFailed` events for you:

![Connect to server example](https://github.com/Pandoa/SecureSocket/blob/master/Images/ConnectToServer.png?raw=true)
The same node exists to connect to a server with SSL/TLS:

![Connect to server SSL example](https://github.com/Pandoa/SecureSocket/blob/master/Images/ConnectToServerSSL.png?raw=true)
The `Connected` execution pin is called when the connection and the upgrade is over and the socket is ready to communicate trough an SSL/TLS connection.
### 1.1.4. Upgrading the connection (SSL/TLS)
You can upgrade an unencrypted connection to SSL/TLS at any time with the `Upgrade TCP connection to SSL/TLS` node:

![SSL upgrade TCP socket](https://github.com/Pandoa/SecureSocket/blob/master/Images/UpgradeConnection.png?raw=true)
### 1.1.5. Sending messages
You can send binary or string messages easily through your TCP socket:

![Send TCP example](https://github.com/Pandoa/SecureSocket/blob/master/Images/SendDataTcp.png?raw=true)

| :information_source:|When you send a string message, the `As ANSI` option allows you to choose how the string is converted to binary. If `As ANSI` is set to `true`, each character will use 8 bits. If it is set to `false` each character will use 16 bits.|
|:---|:---|

### 1.1.6. Closing the connection
You can close the TCP connection at any time with the `Close Connection` node:

![TCP close connection example](https://github.com/Pandoa/SecureSocket/blob/master/Images/CloseConnection.png?raw=true)
| :information_source:|When the TCP socket is destroyed, the connection is automatically cleanly closed.|
|:---|:---|
## 1.2. UDP
### 1.2.1. Creating a socket
As for TCP, there is a node to create an UDP socket:

![](https://github.com/Pandoa/SecureSocket/blob/master/Images/CreateSocketUdp.png?raw=true)

### 1.2.2. Sending messages
You can send data through your UDP socket with the `Send` or `Send Binary` nodes. You can use DTLS by setting `Use Dtls` to true:

![Send data UDP](https://github.com/Pandoa/SecureSocket/blob/master/Images/SendDataUdp.png?raw=true)

### 1.2.3. Receiving messages
To receive messages, you have to bind the `OnMessage` event and add a listening port:

![Receive message UDP](https://github.com/Pandoa/SecureSocket/blob/master/Images/ReceiveDataUdp.png?raw=true)
### 1.2.4. DTLS
DTLS is used like UDP. When you send a message with `Use Dtls` set to true, a DTLS handshake is established with the server and stored in the socket for the specific IP/Port combination. Messages exchanged with this host will then be encrypted and decrypted. You can handle the lifetime of these DTLS sessions with the nodes:

![DTLS options](https://github.com/Pandoa/SecureSocket/blob/master/Images/DtlsOptions.png?raw=true)

| :information_source:|Setting the auto close delay to a negative number (< 0) disable the delay and the DTLS connection will never be destroyed unless the socket is destroyed or `Forget DTLS Connection` is called.|
|:---|:---|

# 2. C++
## 2.1. Includes
Secure socket has 3 files to include:
```cpp
#include "TcpSocket.h"           // For TCP sockets
#include "UdpSocket.h"           // For UDP sockets
#include "SecureSocketLibrary.h" // For MD5, SHA, AES and conversion
```
## 2.2. TCP
### 2.2.1. Creating a TCP socket
```cpp
UTcpSocket* const TcpSocket = UTcpSocket::CreateTcpSocket();
```
### 2.2.2. Listening to events
To handle status change or messages, you must bind `UFUNCTION()` functions to the following events:
|Event Name|Signature|Description|
|:---|:---|:--|
|`OnMessage`|`void Func(const TArray<uint8>& Data)`|Called when the server sent us data.|
|`OnConnected`|`void Func()`|Called when the TCP socket is connected to the server. |
|`OnConnectionError`|`void Func()`|Called when the TCP failed to connect to the server.|
|`OnClosed`|`void Func()`|Called when the connection is closed.|
|`OnUpgradeSuccess`|`void Func()`|Called when the connection has been upgraded to SSL/TLS.|
|`OnUpgradeFailed`|`void Func(const FString& Error)`|Called when the connection failed to upgrade to SSL/TLS.|
As these events are `Dynamic Multicast Delegates`, you have to bind them with `AddDynamic()`:
```cpp
TcpSocket->OnMessage        .AddDynamic(this, &UMyClass::OnMessageTCP);
TcpSocket->OnConnected      .AddDynamic(this, &UMyClass::OnConnectedTCP);
TcpSocket->OnClosed         .AddDynamic(this, &UMyClass::OnClosedTCP);
TcpSocket->OnConnectionError.AddDynamic(this, &UMyClass::OnConnectionError);
TcpSocket->OnUpgradeSuccess .AddDynamic(this, &UMyClass::OnConnectionUpgradedTCP);
TcpSocket->OnUpgradeFailed  .AddDynamic(this, &UMyClass::OnConnectionUpgradFailedTCP);
```
### 2.2.3. Connecting to the server
To connect, simply call the `Connect()` function:
```cpp
TcpSocket->Connect(TEXT("my.server.com"), 500);
```
### 2.2.4. Upgrading the connection to SSL/TLS
Once your socket is connected, you can upgrade the connection to an encrypted connection with `UpgradeToSsl`:
```cpp
if (TcpSocket->UpgradeToSsl())
{
    // We successfully started the handshake procedure.
    // Be careful that the connection is still not encrypted here,
    // OnUpgradeSuccess will be called when the handshake succeeded
    // and you can start sending encrypted data.
}
else
{
    // An internal error occurred and the handshake wasn't able to start.
    // Additional information are available in the output log.
}
```
You can downgrade the connection to unencrypted at any time with `DowngradeFromSsl()`:
```cpp
if (TcpSocket->DowngradeFromSsl())
{
    // Connection is not encrypted anymore.
}
else
{
    // The connection wasn't encrypted.
}
``` 
### 2.2.5. Sending messages
To send data, you have to call the `Send()` function. It is overloaded to accept either a `const FString&` or a `const TArray<uint8>&`:
```cpp
// You can get it programmatically.
const FString		StringData = TEXT("Hello there!");
const TArray<uint8> BinaryData = { 0, 1, 2, 3, 4, 5 };

TcpSocket->Send(StringData, /* bIsAnsi */ true);
TcpSocket->Send(BinaryData);
```
### 2.2.6. Closing the connection
To cleanly close the connection, call the `CloseConnection()` function:
```cpp
TcpSocket->CloseConnection();
```
## 2.3. UDP
### 2.3.1. Creating an UDP socket
```cpp
UUdpocket* const UdpSocket = UUdpSocket::CreateUdpSocket();
```
### 2.3.2. Sending messages
To send data, you have to call the `Send()` function. It is overloaded to accept either a `const FString&` or a `const TArray<uint8>&`:
```cpp
const FString ServerAddress = TEXT("my.server.com");
const int32   ServerPort    = 501;

const FString StringData       = USocketLibrary::MD5_HashString(TEXT("MyPassword"));
const TArray<uint8> BinaryData = { 0, 1, 2, 3, 4, 5}

UdpSocket->Send(ServerAddress, ServerPort, StringData, false /* bIsANSI */, false /* bUseDTLS */);
UdpSocket->Send(ServerAddress, ServerPort, BinaryData, /* bUseDTLS */ false);
```
### 2.3.3. Receiving messages
To receive messages, you have to listen on a port with `SetListeningPort()` and bind a function to the `OnMessage` event:
```cpp
UCLASS()
class MYGAME_API UMyClass : public UObject
{
    GENERATED_BODY()
public:
    // Called when we receive a message
    void OnMessage(const FString& Host, const int32 Port, const TArray<uint8>& Data)
    {
        const FString StringData = USocketLibrary::ToString_Binary(Data, EStringEncoding::ANSICHAR);
        UE_LOG(LogTemp, Log, TEXT("New message from %s:%d: %s."), *Host, Port, *StringData);
	}
	
	// Setup UDP receive example 
	void SetupSocket()
	{
		UdpSocket->OnMessage.AddDynamic(this, &UMyClass::OnMessage);
		UdpSocket->SetListeningPort(502);
	}

private:
    // Our socket previously created
	UPROPERTY()
	UUdpSocket* UdpSocket;
};
```
### 2.3.4. DTLS
DTLS is used like UDP. When you send a message with `bUseDtls` set to true, a DTLS handshake is established with the server and stored in the socket for the specific IP/Port combination. Messages exchanged with this host will then be encrypted and decrypted. You can handle the lifetime of these DTLS sessions with `bool UUdpSocket::ForgetDtlsConnection(const FString & Host, const int32 Port)` and `void UUdpSocket::SetDtlsConnectionAutoCloseDelay(const float Delay)`.
| :information_source:|Setting the auto close delay to a negative number (< 0) disable the delay and the DTLS connection will never be destroyed unless the socket is destroyed or `ForgetDtlsConnection()` is called.|
|:---|:---|
# 3. Support
If you need help, have a feature request, experience troubles or detected a bug, please contact us at [pandores.marketplace@gmail.com](mailto:pandores.marketplace+SecureSocket@gmail.com?subject=Secure%20Socket%20-%20).
