Introduction
============

This is a simple network library for Go.

It focus on packet based persistent connection communication.

It provide a packet splitting protocol like Erlang's `{packet: N}` in default. And supported custom packet splitting protocol.

But it didn't limit the encode or decode format of the request and response.

Also this library provide session management and broadcast features to make your life easy.

How to install
==============

```
go get github.com/funny/link
```

How to use
===========

Choose a protocol for your project.

```go
proto := link.PacketN(4, binary.BigEndian)
```

Setup a server on port `8080` and set protocol.

```go
server, _ := link.Listen("tcp", "0.0.0.0:8080", proto)
```

Handle incoming connections. And setup a message handler on the new session.

```go
server.Handle(func(session *Session) {
	fmt.Println("new session in")

	session.OnMessage(func(session *Session, msg []byte) {
		fmt.Printf("new message: %s\n", msg)
	})

	session.Start()
})
```

***NOTE: After initialize the session, you need to start it by manual.***

Use the same protocol dial to the server.

```go
proto := link.PacketN(4, binary.BigEndian)

client, _ := link.Dial("tcp", "127.0.0.1:8080", proto)

client.Start()
```

***NOTE: You need to start the session before you use it.***

Implement a message type.

```go
type TestMessage struct {
	Message string
}

func (msg TestMessage) RecommendPacketSize() uint {
	return uint(len(msg.Message))
}

func (msg TestMessage) AppendToPacket(packet []byte) []byte {
	return append(packet, msg.Message...)
}
```

Send a message to server.

```go
client.Send(TestMessage{ "Hello World!" })
```

Examples
========

* [Echo server](https://github.com/funny/link-demo/tree/master/echo_server/main.go)
* [Echo client](https://github.com/funny/link-demo/tree/master/echo_client/main.go)
* [Broadcast server](https://github.com/funny/link-demo/tree/master/broadcast/main.go)
* [Benchmark tool](https://github.com/funny/link-demo/tree/master/benchmark/main.go)

Document
========

[Let's Go!](http://godoc.org/github.com/funny/link)
