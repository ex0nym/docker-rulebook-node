# Database
Exonym uses the no-SQL database `CouchDB` to store its data.

## Containers
Exonym creates a separate container for each username to store its encrypted Identity Mixer key data. However, the `cache` container is a reserved User ID and is used for caching public key data.

Note that the `cache` container is not intended to store user data and should not be modified directly.

Here's an improved version of the documentation:

## Network
The Network Map contains verified node data and represents 
a whitelist of registered Advocates in the network.

When you set up your node, it is not currently part of the 
Network Map. To become part of the Network Map, you must be 
added to the trustworthy source list or be added as an 
Advocate of a trusted source. The process for being listed 
as a trustworthy source is different depending on whether 
you are using a test or production environment.

If you're using a test environment, you can add your node to 
the Network Map via Sybil-Test or by pointing your node 
at your own trustworthy sources list and building a network 
map from there. If you're using a production environment, 
you need to follow a different process to be added to the 
trustworthy sources list.

If you're using production, you need to prove honesty, under the trustworthy-sources rulebook; and you get onto the list by adding yourself via Sybil.

## Node
The Node Table contains a local view of the node, which is composed of a row for each Source or Advocate defined on it. It is used to maintain an intrinsic view of the node at runtime.

Both the Node Table and the Network Map are necessary to maintain an accurate and complete view of the node, both intrinsically and extrinsically.

## Users
Users table contains a row for each; 

- Advocate
- Source
- primary-admin
- secondary-admin (username)
- api-key
- plus an RSA Key Pair

If you reset the node, it is only the advocate source and the RSA that is deleted. 

The administrators and the API key are kept intact.
