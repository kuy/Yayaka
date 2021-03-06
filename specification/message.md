# Message

A *message* is a JSON data transfered between two services.
It is transferred by a packet if the two services are on different hosts.


## Types of messages

There are two types of messages, request messages and answer messages.
Request messages is a basic type of messages and answer messages is a type for replying to a message.


## Layered message routing

Each request messages have the properties to routing, **host**, **protocol**, **service**, and **action**.
They are sent to proper message handlers by the following procedure.

1. A connection sends a messages to the specified **host**.
2. The **host** routes the message to the specified **protocol**.
3. The **protocol** routes the message to the specified **service**.
4. The **service** routes the message to the handler to handle the specified **action**.

Each answer messages have the properties to routing, **host**, **reply_to**.
They are sent to proper message callers by the following procedure.

1. A connection sends a messages to the specified **host**.
2. The **host** routes the message to the caller of a message which has specified "id".


## Validation

Each layers of message routing SHOULD check messages are addressed to themselves.
They also MAY ignore messages at any time if one or more of **host**, **protocol**, **service**, and **action** are unknown.


## Timeout

Yayaka Protocol doesn't specify about timeout of messages.


## JSON format

### Request

A request message have following properties.

#### Properties

- MUST **sender** object  
  An object have following properties

  - MUST **host** string  
    A sender host

  - SHOULD **protocol** string  
    A sender protocol

  - SHOULD **service** string  
    A sender service

- MUST **id** string  
  An ID which is unique in sender host

- MUST **host** string  
  A destination host

- MUST **protocol** string  
  A destination protocol

- MUST **service** string  
  A destination service

- MUST **action** string  
  The text to describe the message

- MUST **payload** object  
  The body of the message

- SHOULD NOT all other properties

#### Example

```
{
  "sender": {
    "host": "host1.example.com",
    "protocol": "example",
    "service": "form"
  },
  "id": "0123456789",
  "host": "host2.example.com",
  "protocol": "example",
  "service": "repository",
  "action": "post example",
  "payload": {
    "text": "example text"
  }
}
```

### Answer

Big differences with request messages are answer messages have **reply_to** property and don't have **action** property.

An answer message SHOULD have properties, **protocol** and **service**, if the **sender** property of the message to reply includes the properties.

#### Properties

- MUST **sender** object  
  An object MUST, MAY, or SHOULD have following properties.

  - MUST **host** string  
    A sender host

  - SHOULD **protocol** string  
    A sender protocol

  - SHOULD **service** string  
    A sender service

- MUST **id** string  
  An ID which is unique in sender host

- MUST **reply-to** string  
  An ID of a message to reply

- MUST **host** string  
  A destination host

- MAY **protocol** string  
  A destination protocol

- MAY **service** string  
  A destination service

- MUST **payload** object  
  The body of the message

- SHOULD NOT all other properties

#### Example

```
{
  "sender": {
    "host": "host2.example.com",
    "protocol": "example",
    "service": "repository"
  },
  "id": "abcdefghij",
  "reply-to": "0123456789",
  "host": "host1.example.com",
  "protocol": "example",
  "service": "form",
  "payload": {
    "status": "ok"
  }
}
```
