backside
============

An open-source backend-as-a-service inspired by firebase

# Description
A backend-as-a-service allows you to create rich web apps without having to worry about the overhead of a backend
service. They work great or a lot of use cases with simple persistence needs.

backside is an open-source backend-as-a-service that is API compatible with firebase. This means that data is treated as
a tree structure and any path in the tree can be "subscribed" to, which is then updated in real time.

# Architecture
backside is built using NodeJS, RabbitMQ, and websockets, which is a good fit for Node's ability to deal with lots of
IO.
Additionally, backside is split up into multiple components, each in a separate repo and npm package. This allows for
the various components to be easily used together, or run individually for scale or flexibility.

These components are:

- Persistence Store, in order to store long term state, a persistence store is needed. This is intended to be
  pluggable, but a simple and memory-story and MongoDB are being used for now. TODO: add persistence repo and doc the
  API
- STOMP Proxy, in order to provider a robust real-time messaging API, we use rabbitMQ speaking the STOMP protocol. In
  order to support more robust authentication and authorization, as well as support websockets, a small proxy is used.
  TODO: add in the proxy repo
- HTTP API, the HTTP api is used for retrieving the state as well as publishing
- Client Library, a client library that is API compatible with firebase
- Administrative Frontend, allows for browsing the data as well as providing information about backside

# Getting Started
This repo contains everything you need to get started with backside.

## Prerequisites
1. Install RabbitMQ (follow the guide on http://www.rabbitmq.com/download.html)
2. `npm install -g backside`
3. `backside`

# Developing
Websockets via socksjs
Testing via mocha - https://github.com/visionmedia/mocha
Dependency Inject via dependable - https://github.com/idottv/dependable

This project contains almost no code, it simply binds together all the different modules using express application mounts

Config is done via environment variables and JSON with sensible defaults, in other words:
```
var port = process.env.PORT || config.port || 3000
```

## config

Backside is composed of two components, the backside-api and the backside-proxy.

Each component is composed of multiple sub-modules
Currently the depedencies look like this:

Backside-api depends on:
persistor interface (either memory or mongodb)
messenger interface (amqp)
auth interface (token auth and user pass auth)
validator interface (rules)

Together, these form the backside api, which is what the backside-proxy depends on.

Each of the these sub-modules should be extracted into their own library, with the backside-api defining interfaces (either via
a base class or just via documentation)

At runtime, each of these can be submodules can be swapped out for a different implementation. However, some of the submodules
depend on one another (and its not consistent from implementation to implementation),
making dynamic instantation a bit difficult. This is where dependable comes in, it allows for registering a function
that instantiates the abstract interface

We want to support two primary use cases:
1. JSON config used to power things, should be able to swap out major sub modules, like memory store for mongodb store
2. Easy requiring as a module for composition that also opens up more options, like injecting your own implementation of an interface
