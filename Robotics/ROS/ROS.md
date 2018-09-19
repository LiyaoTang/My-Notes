# ROS System

### Concept

#### Master

- Function
  - Main Server
    - responsible for registration of nodes
    - managing nodes
    - collecting statistics of nodes & their behaviors

  - Registration Logic

    - unique name for each node

      $\Rightarrow$ new-comer with the same name replace the existing node

      $\Rightarrow$ easy fix for malfunctioning node

#### Node

- Function
  - Process
    - single-purpose
    - basic unit of the system

#### Communication

- Overview
  - Type
    - asynchronous
    - peer-to-peer; able to multi-cast
    - multi-lingual protocol embedded
  - Design
    - separate types of communication
      1. publish-subscribe model (yet, different from MQTT)
      2. service-client model

- Topic
  - Type
    - defined by the type of message in the topic

- Message
  - Type
    - self-defined (will be recorded by the Master)
    - defined in ".msg" file - shared interface for various source codes

- Publisher & Subscriber

  - Publishing
    - to a specific channel
    - using a specified message type
    - with control over size of message queue for late subscriber
    - Timing
      1. python: busy waiting loop with living check (in case of external shut-down order)

  - Subscribing
    - to channel(s)
    - waiting on message with a parallel sub-thread (running the callback function)
    - callback function triggered on receiving message
  - Implementation
    - between-process communication: modified TCP/IP stack $\Rightarrow$ transmission layer

- Server & Client

  - Registering Service
    - service type defined in text file ".srv" - shared interface for various source codes