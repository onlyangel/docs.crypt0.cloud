---
weight: 10
title: API Reference
---

# Introduction

Welcome to the Crypt0 Cloud API! You can use the this API with the endpoint where an instance of Crypt0 Cloud is installed, to manage a Nodes and Transactions.

Every node farm under your control need to have a setted up Coordinator Node and before a node can be used, it needs to be added to the farm via the Coordinator Node.

Every Transaction have to be under a distributed Application, this distributed application is setted up with it's entpoint url by the Coordinator node in all the nodes. This mean that the app is installed and enabled in all nodes and transactions can be added to any node allowing to use the closest node to your client. Also Every time a Transaction is called using the Distributed Application Keys a callback is executed (as a http POST Request) to the registered endpoint for the Application.

There is two kind of transactions Single Transactions and Group Transactions.

* Single Transaction: this is a transaction that get verified and inserted directly to the ledger under a distributed Application
* Group Transaction: this transaction describe a sequence of transactions in an application. Each of those transactions is a Single Transaction that refers this group Transaction as its parent.

**For more information about our internal protocol visit the [White Paper](#).**

# Cryptographic keys
> To obtain a valid keypair with ed25519

```go
package main

import (
	"golang.org/x/crypto/ed25519"
	"log"
	"math/rand"
	"time"
)

func get_key_pair()(publicKey ed25519.PublicKey, privateKey ed25519.PrivateKey){
	// Generate Key Pair from random data
	publicKey, privateKey, err := ed25519.GenerateKey(rand.New(rand.NewSource(time.Now().UnixNano())))
	if err != nil{
		log.Panic(err)
	}
	return publicKey, privateKey
}
```

```shell
# install a key generator tool
go install github.com/crypt0cloud/keypair_generator

# excecute the command
keypair_generator

[OUTPUT]
> Algorithm: ed25519
>
> Pubic Key:
> {PUBLIC_KEY}
>
> Private Key:
> {PRIVATE_KEY}


```

By design we use the algorithm ed25519 as our key pair algorithm.

It is extensible used in the following identifications

* Coordinator Master keys
* Nodes Signing keys
* Distributed Applications Signing Keys
* Transaction Signer

# Coordinator
## Register Master Key

```go
package main

import (
	"golang.org/x/crypto/ed25519"
	cc "github.com/crypt0cloud/crypt0cloud-sdk-go"
)

func coordinator_init(endpoint string, MKPublicKey ed25519.PublicKey) {

	//Create client and register master public key to setup coordinator
	client := cc.GetClient(endpoint)
	client.Coord_RegisterMasterkey(MKPublicKey)

}
```

```shell
# With shell, you can just pass the correct header with each request
curl http://{ENDPOINT}/api/v1/coord/register_masterkey?url={ENDPOINT}&key={MKPublicKey}

# Make sure to replace `ENDPOINT` with your server url and `MKPublicKey` with your base64 uri enconded Master Public Key
```
To configure the Coordinator Endpoint it needs to know it's own public url (endpoint) and the public key of the master key that will be used for coordinator activities

### HTTP Request

`GET /api/v1/coord/register_masterkey`

### Query Parameters

Parameter | Description
--------- | -----------
url | Escaped URL of the coordinator endpoint.
key | base64 uri enconded Master Public Key




## Add Nodes

## Create Distributed Node

# Nodes

## Get Node Credentials

## Create User

# Single Transactions

# Group Transactions

## Initial Group Transaction
