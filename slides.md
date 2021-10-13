layout: true

.signature[@algogrit]

---

class: center, middle

# GCP PubSub

Gaurav Agarwal

---
class: center, middle

Messaging and ingestion for event-driven systems and streaming analytics.

---

Use cases:

- Ingestion user interaction and server events

- Real-time event distribution

- Replicating data among databases

- Parallel processing and workflows

- Enterprise event bus

- Data streaming from IoT devices

- Load balancing for reliability

---

The Basics:

- Message: the data that moves through the service.

- Topic: a named entity that represents a feed of messages.

- Subscription: a named entity that represents an interest in receiving messages on a particular topic.

- Publisher (also called a producer): creates messages and sends (publishes) them to the messaging service on a specified topic.

- Subscriber (also called a consumer): receives messages on a specified subscription.

---
class: center, middle

![Basic flow](assets/images/basic-flow.png)

---

- two publishers publishing messages on a single topic

- two subscriptions to the topic.

- first subscription has two subscribers,

  - with each subscriber receiving a subset of the messages

- second subscription has one subscriber
  - will receive all of the messages

- bold letters represent messages

---

## Judging Performance of a Messaging Service

- scalability

- availability

- latency

---
class: center, middle

### Scalability

---
class: center, middle

A scalable service should be able to handle increases in load without noticeable degradation of latency or availability.

---

"Load" can refer to:

- Number of topics

- Number of publishers

- Number of subscriptions

- Number of subscribers

- Number of messages

- Size of messages

- Rate of messages (throughput) published or consumed

- Size of backlog on any given subscription

---
class: center, middle

### Availability

---
class: center, middle

A system’s availability is measured on how well it deals with different types of issues, gracefully failing over in a way that is unnoticeable to end users.

---
class: center, middle

Failures can occur in hardware (e.g., disk drives not working or network connectivity problems), in software, and due to load.

---
class: center, middle

Availability can also degrade due to human error, where one makes mistakes in building or deploying software or configurations.

---
class: center, middle

### Latency

---
class: center, middle

Latency is a time-based measure of the performance of a system.

---
class: center, middle

A service generally wants to minimize latency wherever possible.

---

For Pub/Sub, the two most important latency metrics are:

- The amount of time it takes to acknowledge a published message.

- The amount of time it takes to deliver a published message to a subscriber.

---
class: center, middle

## Basic Architecture

---

- Pub/Sub servers run in all GCP regions around the world

- Pub/Sub’s load balancing mechanisms direct publisher traffic to the nearest GCP data center where data storage is allowed

- Pub/Sub is divided into two primary parts: the data plane, and the control plane

---
class: center, middle

*Data plane* handles moving messages between publishers and subscribers

---
class: center, middle

*Control plane* handles the assignment of publishers and subscribers to servers on the data plane

---
class: center, middle

The servers in the data plane are called *forwarders*, and the servers in the control plane are called *routers*.

---
class: center, middle

### Control Plane

---

- The Pub/Sub control plane distributes clients to forwarders in a way that provides scalability, availability, and low latency for all clients.

- Any forwarder is capable of serving clients for any topic or subscription.

- When a client connects to Pub/Sub, the router decides the data centers the client should connect to based on shortest network distance, a measure of the latency on the connection between two points.

- Within any given data center the router tries to distribute overall load across the set of available forwarders.

- A client takes this list of forwarders and connects to one or more of them.

---

The router must balance two different goals when assigning forwarders:

- uniformity of load

  - ideally every forwarder is equally loaded

- stability of assignments

  - ideally a change in load or a change in the set of available forwarders changes the smallest number of existing assignments

---
class: center, middle

### Data Plane

---
class: center, middle

The data plane receives messages from publishers and sends them to clients.

---

In general, a message goes through these steps:

- A publisher sends a message.

- The message is written to storage.

- Pub/Sub sends an acknowledgement to the publisher that it has received the message and guarantees its delivery to all attached subscriptions.

- At the same time as writing the message to storage, Pub/Sub delivers it to subscribers.

- Subscribers send an acknowledgement to Pub/Sub that they have processed the message.

- Once at least one subscriber for each subscription has acknowledged the message, Pub/Sub deletes the message from storage.

.content-credits[https://cloud.google.com/pubsub/architecture#data_plane_-_the_life_of_a_message]

---

- Different messages for a single topic and subscription can flow through many publishers, subscribers, publishing forwarders, and subscribing forwarders.

- Publishers can publish to multiple forwarders simultaneously and subscribers may connect to multiple subscribing forwarders to receive messages.

---
class: center, middle

The flow of messages through connections among publishers, subscribers, and forwarders can be complex.

---
class: center, middle

## Pub/Sub vs Pub/Sub Lite

---
class: center, middle

Pub/Sub and Pub/Sub Lite are both horizontally scalable, managed messaging services.

---

- Pub/Sub should be the default solution for most application integration and analytics use cases.

- Pub/Sub Lite is only recommended for applications where achieving extremely low cost justifies some additional operational work.

.content-credits[https://cloud.google.com/pubsub/docs/choosing-pubsub-or-lite#comparison_table]

---

- Pub/Sub routes messages globally

- Pub/Sub topics and subscriptions are global resources

- Pub/Sub scales automatically

- Pub/Sub supports multiple message delivery modes

- Pub/Sub uses per-message parallelism

---
class: center, middle

## Integrating with Cloud Functions

---
class: center, middle

*Demo*: Consume messages using `BackgroundFunction`

---
class: center, middle

Code
https://github.com/AgarwalConsulting/presentation-gcp-pubsub

Slides
https://gcp-pubsub.slides.agarwalconsulting.io
