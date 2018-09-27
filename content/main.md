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

func coordinator_init(Endpoint string, MKPublicKey ed25519.PublicKey) {

	//Create client and register master public key to setup coordinator
	client := cc.GetClient(Endpoint)
	client.Coord_RegisterMasterkey(MKPublicKey)

}
```
<!--
```shell
# With shell, you can just pass the correct header with each request
curl http://{ENDPOINT}/api/v1/coord/register_masterkey?url={ENDPOINT}&key={MKPublicKey}

# Make sure to replace `ENDPOINT` with your server url and `MKPublicKey` with your base64 uri enconded Master Public Key
```
-->

To configure the Coordinator Endpoint it needs to know it's own public url (endpoint) and the public key of the master key that will be used for coordinator activities

### Parameters

Parameter | Description
--------- | -----------
Endpoint | Escaped URL of the coordinator endpoint.
MKPublicKey | Master Public key




## Add Nodes

```go
package main

import (
	cc "github.com/crypt0cloud/crypt0cloud-sdk-go"
	"golang.org/x/crypto/ed25519"
)

func add_node(CoorEndpoint, NodeEndpoint string, MKPrivateKey ed25519.PrivateKey) {

	//Create client and register new Node
	client := cc.GetClient(CoorEndpoint)
	client.Coord_AddNode(MKPrivateKey, CoorEndpoint, NodeEndpoint)

}

```

<!--
```shell
# With shell, you can just pass the correct header with each request
curl http://{ENDPOINT}/api/v1/coord/register_nodes

# Make sure to replace `ENDPOINT` with your server url and `MKPublicKey` with your base64 uri enconded Master Public Key
```
-->

To configure a Node with the Coordinator it needs to know the node endpoint (endpoint) and the public key of the master key that will be used for coordinator activities

### Parameters

Parameter | Description
--------- | -----------
CoorEndpoint | URL of the Coordinator server
NodeEndpoint | URL of the Node to integrate to the Coordinator
MKPrivateKey | The Master Private Key


<!--
> See JSON for POST RAW BODY

```json
{

		"Content": "{base64(register_node_payload)}",
		"Sign":    "{base64(ed25519.Sign(MKPrivateKey, sha256(register_node_payload)))}"
}
```


Methods used in the JSON representation

Method | Description
--------- | -----------
base64([]byte) | Convert a byte array to a string encoded in base64.
sha256([]byte) | Calculate a Hash sha256 from a byte array.
ed25519.Sign(PrivateKey, payload) | Sign with a PrivateKey a payload, using the ed25519 algorithm.


> See JSON for the Structure of the **register_node_payload**

```json
{
		"Urls": ["{node_endpoint}"]
}
```


Variables used in the JSON REPRESENTATION

Method | Description
--------- | -----------
register_node_payload | the payload JSON structure that contains the url of the nodes.
node_endpoint | The url of the server where the node is ready to serve.
-->

## Create Distributed App

```go
package main

import (
	"github.com/crypt0cloud/core/model"
	cc "github.com/crypt0cloud/crypt0cloud-sdk-go"
	"golang.org/x/crypto/ed25519"
)

func create_app(CoorEndpoint string, MKPublicKey ed25519.PublicKey, MKPrivateKey ed25519.PrivateKey)(*model.Transaction, []byte, []byte){

	client := cc.GetClient(CoorEndpoint)
	NewAppTransaction, AppPublicKey, AppPrivateKey := client.Coord_CreateAPP(CoorEndpoint, MKPublicKey, MKPrivateKey)

	return NewAppTransaction, AppPublicKey, AppPrivateKey

}

```


To configure a Node with the Coordinator it needs to know the node endpoint (endpoint) and the public key of the master key that will be used for coordinator activities

### Parameters

Parameter | Description
--------- | -----------
CoorEndpoint | URL of the Coordinator server
MKPublicKey | The Master Public Key
MKPrivateKey | The Master Private Key


# Nodes

## Get Node Credentials

## Create User

# Single Transactions

# Group Transactions

## Initial Group Transaction
