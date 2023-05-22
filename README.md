
Please see [our wiki](https://github.com/ex0nym/rulebook-node/wiki) for full-documentation.


# Decentralized Rulebooks - Developer Orientation
Decentralized Rulebooks enable transactional governance without centralized control. This approach empowers users with a democratic and distributed mechanism, eliminating constraints of centralized systems.


## Outcomes
This documentation provides a hands-on orientation for developers to understand the network topology and user lifecycle. After reading this guide, developers will:

- Understand actor roles and responsibilities.
- Grasp the user lifecycle (command line), including Sybil credential acquisition, Exonym Wallet interaction, and user experience.
- Explore rulebook inheritance and polymorphism, enhancing system flexibility.
- Experience the complete user lifecycle, including setup, subscription, revocation, and rejoining. This hands-on approach offers practical insights into user navigation.

## **Quick Definitions:** 
| Term | Definition |
| --- | --- |
| [Rulebook Document](https://github.com/ex0nym/rulebook-node/wiki/Rulebook-Definition) | Immutable rules for wide acceptance, supporting polymorphism and reuse. |
| Source | Interprets rulebook, governs reputation within its defined context. |
| Advocate | Subscribes to a Source, interprets rulebook, sets criteria for Producers. |
| Trust Network | Reputation system guided by a Rulebook document. |
| Sybil | Service resisting clone accounts, verifies authenticity and controls authorized clones. |
| Exonym Wallet | Digital wallet for managing data and transactions, empowering users. |
| Producer | Creates content adhering to rulebook interpretation, accepted by Advocate. |
| Consumer | User verifying Proof of Current Honesty. |

## Core Components

The Rulebook Node consists of the following core components:

| Component | Description |
| --- | --- |
| Rulebook Node Web Server | Web-based interface for administrative tasks and API key management. |
| Static Data Replication | Publicly accessible repository for storing signed Node Data (supports read and write operations). |
| UDP Listener (x0basic) | Listens to Join and Revocation messages from connected Trust Networks. |


# Lifecycle Walkthrough
This page offers a comprehensive walk-through of Exonym's Decentralised Rulebooks. We'll start by setting up a local rulebook node and appointing administrators. Then, we'll assist you in obtaining an API key and checking your node status.

Next, we'll create Source data based on the rulebook and install the Exonym command-line utility. We'll guide you on adding the source to your trustworthy sources list and explore the generated Source data. 

After understanding the Source data, we'll generate Advocate data using the Source as a reference and add the Advocate to the Source. We'll also provide you with a Sybil Test Credential.

In the final steps, we'll guide you in subscribing to a Rulebook and gaining access to a Service. Throughout this walk-through, we aim to offer a seamless and interactive experience, simplifying the understanding and utilization of Exonym's Decentralised Rulebooks.

Let's get started!

# Installation Instructions
Please note that these instructions are for Linux-based operating system.  Windows will follow shortly.

## Pre-requisites

- `git` command line tools installed
- Docker with docker compose support installed
- Access to an SFTP static file server (one server can be used for all rulebook instances)

## Linux Based OS
1. Clone this repository
2. From the `node.env` file set up a `envfile` with the necessary local [environment variables]((#rulebook-node-environment-variables)) in.

```
export DB_USERNAME=admin
export ...=...
```

Currently, setting up a SFTP server with known host data 
from SSH key-scan is required. Although this is an important 
step for production nodes, it will become optional for local 
test nodes in the near future. This will significantly speed 
up the setup process, allowing you to get started as quickly 
as possible.

1. Set the following permissions: 

```
chmod 600 envfile
```

2. Ensure your local environment contains the environment variable using;

```
source envfile
```
2. Start the instance

```
docker compose up -d
```
> Note that if you already have instances running on the ports specified in the Docker Compose file, you will need to change the ports to the ones you prefer. This will ensure that your new instances do not conflict with any existing instances running on the same ports.

When the node first starts, an admin password will be displayed with the username set to the requested value and a random one-time password assigned. Please ensure that you take note of this password as it is required for node administration.

It is important to check for any exceptions that may occur during the startup process. Some of the environment variables are mandatory, and the node will throw an exception if they are not set. Make sure to set these variables appropriately before starting the node to avoid any issues.

> If you've started the node multiple times and haven't saved the password, you'll need to log in to the database, open the users table, and delete the primary admin user. This will cause the password to be regenerated the next time you start the node.
>
> You'll find the the couchDB interface at:
> 
> ```
> http://localhost:5984/_utils/
> ```

A brief tour of the database tables is below.

# Administration
The node employs a standard administrative setup, with a primary administrator having complete control over the system. The primary admin is tasked with adding additional administrators and managers who are given local access but limited permission to perform certain actions.

API keys can be created by the primary admin, which can be issued to administrators or remote servers to grant them specific permissions to perform actions such as creating, modifying, or deleting data within the system.

The primary admin is restricted to the following actions:

- Adding and deleting API keys
- Adding and deleting secondary administrators
- Resetting the node

Secondary administrators are responsible for all other activities.

> If anything goes wrong, you can access the logs: 
> ```
> docker logs local-rulebook-node -f
> ```

# Defining the Trust Network
The set up assumes that you are testing and so does not cover rulebook composition.

A Decentralised Rulebook Trust Network consists of a 
Source and trusted Advocates. This is a single Trust Network
and then maybe multiple trust networks for each Rulebook.  This can be represented symbolically by;

$$
  H \leftarrow \mathbf{H_0} \leftrightarrow \mathbf{H_{0,A}}
$$

And so $\mathbf{H_1}$ associated Advocates are on a different Trust Network. There is no need for trust between these two sources.



An example of a rulebook can be found [here](https://trust.exonym.io/sources-rulebook.json);

```
https://trust.exonym.io/sources-rulebook.json
```

The steps we will now execute proceed as follows;
1. Set up the administrators
2. Set up an API key ready for use
3. Generate the Source data by referencing the above rulebook.
4. Inspecting generated Source data and optionally interpreting the Rulebook.
5. Generating Advocate data by referencing the Source.
6. Adding the Advocate to the Source

## Setting up Administrators:

Access the rulebook control panel by navigating to:

```
http://localhost:<port_number_defined_in_docker-compose.yml>
```

> N.B. if you use your computer name, you may not be able to sign in due to browser security protections.

Click the dropdown menu and select "Control Panel".

Enter your admin username and the one-time password provided. You will be prompted to change your password.

To add a new administrator, select "Access Type" and enter the username. Copy the resulting password as it cannot be recovered. If a user loses their password, they will need to be deleted and created again.

```
{"username":"second_admin","password":"7C+UdBkP"}
```

Select "Create API Key".

```
{"keyId":"0e817560-ad82-4ee6-b11c-c66ec5b37f3d","apiKey":"5b048d1be4673ed43dd86bde3fab204e703517a075eb64d86e7cc63667dcbc61"}
```

Close the window and click "Sign Out". 

# Establishing the Rulebook Node and a Trust Network 

As we delve into the intricacies of building a Rulebook Node and establishing a Trust Network, it's important to grasp a few key concepts that will guide us through the process. This section will discuss the fundamentals of the Network Map, Node Data, establishing Trust on the Network, the functionality of the Public Node Status through the `/whois` endpoint, and the crucial differences between Testnet and Production Network. 

Understanding these concepts will serve as the foundation for creating and maintaining a secure and efficient network. We will explore how nodes are defined, how they interact with each other, and how trust is established and maintained within the network. 

We'll also provide practical insights into the node status and offer guidance on handling potential issues. Finally, we'll touch upon the unique aspects of operating within a Testnet versus a Production Network, ensuring clarity on how to navigate both environments successfully. Let's get started.

## Network Map

The network map collectively represents all nodes in the network. It outlines each node's identity, role, and relevant attributes. Rather than physical connections, the network map is constructed through cryptographic verifications and pointers. It allows each node to independently verify other nodes using the computed information stored in their local NetworkMap. 

## Node Data

Node data is the information specific to an individual node. It includes the node's identity, role, relationships, and other attributes. Each node maintains its own node data, which contributes to the broader network map. This data is used for cryptographic verifications and forms the basis for trust within the network.

## Trust on the Network

To gain trust on the network, certain criteria must be met, varying based on the node's role:

**Source Nodes**
- Proper Node Definition: Correctly defining the source node, including its identity and attributes.
- Inclusion in Source List: The source node should be listed in the source list from where its node originates.

**Advocate Nodes**
- Proper Node Definition: Accurately defining the advocate node, including its identity and other pertinent details.
- Advocacy of a Valid Source: The advocate node must advocate for a valid source node.

These criteria help ensure that nodes are properly defined and establish trustworthy relationships within the network.

## Public Node Status : `/whois`

Each node features a `/whois` endpoint allowing anyone to check its status. It is particularly useful in determining why the node isn't accepted on the network and if an updated `rai.xml` or `pp.xml` is required. 

The `/whois` endpoint provides the following information: (to do - update with full information)

```json
{
  "lastRefresh": "2023-05-12T13:45:14Z",
  "currentLocalState": "LOCAL_STATE_SOURCE_AND_HOST",
  "currentGlobalState": "GLOBAL_STATE_SOURCE_SET_AVAILABLE_THIS_SOURCE_UNLISTED",
  "advocateUID": "urn:rulebook:unique:basis:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c",
  "thisAdvocateSourceUID": "urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c",
  "thisNodeSourceUID": "urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c",
  "listeningToSources": [
    "urn:rulebook:exonym:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c",
    "urn:rulebook:sybil:800a5e641a0f9e6f2ba77c8a31384df2d034a969ccd565b8b9206fd4f8126296"
  ],
  "latestRevocationInformationHash": "62e577836a396da98b626394fc19b49b93129bcdb63144ae757e007a46d624a4",
  "latestPresentationPolicyHash": "f73f4a7f12bccf67fb3db01443320eff128d7e82030145d77dd39612b99e1de1"
}
```

The local state indicates the node's current configuration, while the global state determines its discoverability within the network.

### Local State

| Local States                                               | Definition | Resolution |
|------------------------------------------------------------|------------|------------------|
| LOCAL_STATE_ UNDEFINED                                      | Neither Source nor Advocate are defined. | Log into the control panel as secondary admin and define the Source/Advocate  |
| LOCAL_STATE_ HOST                                           | The node is an advocate only. |  |
| LOCAL_STATE_ SOURCE                                         | The node is a source only. |  |
| LOCAL_STATE_ SOURCE_AND_HOST                                | The node is a source and advocate. | | 
| LOCAL_STATE_ INDEPENDENT_SOURCE_AND_HOST                    | The node acts as both a source and an advocate, and these roles are controlled by the same entity. If the trust network's cardinality is 2, the Rulebook is considered centralized, but it can also be decentralized. |  |

### Global State

| Global States                                             | Definition | Resolution |
|-----------------------------------------------------------|------------|------------------|
| GLOBAL_STATE_ SOURCE_SET_UNAVAILABLE                       | Could not find the `sources.xml` file. | Check the `SPAWN_NETWORK_FROM` and the log files during start-up. |
| GLOBAL_STATE_ SOURCE_SET_AVAILABLE_ THIS_SOURCE_UNLISTED    | Found the `sources.xml` file, but this Source UID was not on it. | Add yourself to the list via the command line utility, `net add_source <url>`, or via the `registerSource` endpoint on your node (depending on your `SPAWN_NETWORK_FROM` environment variable). |
| GLOBAL_STATE_ DEFINED_SOURCE_LISTED_ _THIS_HOST_UNLISTED    | Found the `sources.xml` file, this source is listed, but the Advocate is not listed on the Source. | Add the Advocate of this node to this node's Source using the Control Panel. |
| GLOBAL_STATE_ THIS_SOURCE_LISTED_HOST_INDETERMINATE         | The source is added, and it was not possible to determine if this Advocate was part of the Trust Network. | Check the availability of the Source's static data. |
| GLOBAL_STATE_ THIS_NODE_LISTED                             | Everything is fine. | No specific remedy needed. |

### Testnet vs. Production Network

It's vital to understand the differences between the testnet and the production network. In production, legal entities register as Sources and Advocates with unique names through Sybil. 

These Sources adhere to the Trustworthy Sources Rulebook. When a Source or Advocate generates local data, they prove their honesty to the node, which assigns the entity's registered name as a prefix along with a user-defined suffix. 

Entities can register multiple Sources or Advocates, but their name will always form part of the Node UID. This name, viewable on Sybil, helps identify the legal entity operating the Node and enforcing the rulebook.

Duplicate UIDs can exist in tests but not in production. Avoid duplicating UIDs during testing to prevent software issues.

Rulebooks are either "test-only" or "production". As the rulebook document is immutable, changing a test rulebook to production essentially creates a new rulebook. This division ensures security against social engineering hacks and maintains accountability for production credentials. Avoid using test credentials in a production setting.

# Sybil Test

## Introduction
Sybil is a special case of a rulebook within the network. It shares similarities with regular rulebooks but includes some distinct features and configurations.

## Credential Specification
In Sybil, the credential specification allows for the assignment of Sybil class, determining the type of actor. This class can define various types, such as a person, entity, robot, representative, or product. The Sybil class provides additional context and categorization for actors within the network.

## The "Hard-Fork" and "Reciprocal Capabilities" Challenge
The Sybil rulebook follows a specific configuration known as a vertical or singular rulebook, which is centralized. This configuration consists of one source and one advocate. Unlike regular rulebooks, there will never be more Sources of the Sybil rulebook. 

This guarantee is possible because the code determines what is considered Sybil, and our Software License safeguards the code from unauthorized modification to avoid the consumption of Sybil. Our Pending Patents further prevents the creation of a similar system without utilizing our code. 

While the code is open source, our legal protections ensure that Sybil remains protected and prevents unauthorized consumption of our Sybil service. Broadcasting oneself as a Sybil-Source into production is strictly prohibited.  It does infringe upon our legal and patent protections; but attempting will also result in revocation under the Trustworthy Sources Rulebook - which is a much quicker more cost effective resolution.

Our legal standpoint provides a resolution to the challenges arising from open source software development within the Sybil solution itself. As long as the consumption of Sybil is not evaded, there are no concerns. Additionally, it's important to note that the utilization of Sybil is voluntary.

## Onboarding Process
The Sybil rulebook has a different onboarding process compared to regular rulebooks. It does not have an open onboarding system where anyone can join. Instead, individuals or entities interested in becoming part of the Sybil rulebook must go through a specific endpoint. The endpoint determines whether to issue the necessary credentials based on certain criteria.

## Rulebook Node Configurations
In the network, there are two possible configurations for a Rulebook node:

1. Open Onboarding: In this configuration, the privilege of joining the Rulebook is permissionless. Any individual or entity can join the Rulebook by fulfilling the necessary requirements without going through a specific onboarding process.

2. Criteria-based Onboarding: The second configuration requires specific criteria to be met before issuing the necessary credentials. This configuration involves additional tests or checks that need to be passed through the node's API to ensure compliance and suitability.

These configurations provide flexibility in establishing Rulebook nodes based on different network requirements and security considerations.


This is where you find Sybil Test


This is the Onboarding domain
```
    https://t0.sybil.exonym.io
```
This is the Rulebook Node domain
```
    https://node.t0.sybil.exonym.io
```

This is the Static Data replication domain
```
    https://static.t0.sybil.exonym.io
```

You will find indexes at: 
```
        https://node.t0.sybil.exonym.io/x-source/signatures.xml
        https://node.t0.sybil.exonym.io/x-node/signatures.xml
```  

This is the UDP domain
```
    x0.t0.sybil.exonym.io
```

# Source Registration API

## Introduction
The Source Registration API allows you to register a Source and publish it, so that your Source becomes part of the `NetworkMap`.

This document provides detailed information on how to use the API endpoint `/registerSource` for source registration.

## Availability
The `/registerSource` endpoint is available on all Rulebook Nodes. You can make requests to this endpoint from any Rulebook Node in the network.

## Configuration
The following configuration option is available for the `/registerSource` endpoint. If not specified, it defaults to false.

### OPEN_SOURCE_PUBLICATION
Boolean flag that enables or disables open source publication.
- Default Value: false

If the `OPEN_SOURCE_PUBLICATION` configuration is set to true, the `/registerSource` endpoint accepts JSON data in the following format and publishes it to the network:

```json
{
    "sourceUrl": "",
    "test": true
}
```

If the `test` flag is set to true, you do not need to provide a proof of honesty. 
Please note that this feature is only available on the test network, as there is currently no production network to publish to. When providing the `sourceUrl`, ensure it is a valid URL.

If the provided URL is verified, the `sources.xml` file will be updated accordingly. 

> N.B you cannot add Advocates to the `sources.xml` list through this API.

## NetworkMap Building
When a new node starts, it will build its NetworkMap based on the following configuration option:

### SPAWN_WIDER_NETWORK_FROM
- Description: Configuration option for building the NetworkMap when a note starts.
- Accepted Values: [Valid Node ID]

Specify a valid Node ID for the `SPAWN_WIDER_NETWORK_FROM` configuration. The note will build its NetworkMap starting from the specified Node ID.

## Additional Information

- In production, all of the apps and notes read from the same index.
- For testing purposes, you have the flexibility to point your source registration anywhere you like.
- When testing, it is essential to include Sybil on your list. 
- When you want your Rulebook to go live, you will need to join a Trustworthy Source's Rulebook.
- To accomplish this, you will use the command line to send the Sybil Rulebook node URL of your source.
- Once your proof is verified, the note will add your source to the `sources.xml` for production.


# An Independent Source and Advocate

## Source Definition
Sign back in with the created user and change the password as requested.

When successfully signed in, three tabs will be available: Source, Advocate, and Hosting. 
- Select the "Source" tab, then "New Source". 
- Reference the Trustworthy Sources Rulebook `https://trust.exonym.io/sources-rulebook.json`
- Enter a unique organization name that aligns with the context defined in the `sources.xml` file.
- Click Create

After a short period, the page should update to display the `Network Name` at the top, along with the `Source URL` for the static data. There should be no `Authorized Nodes` listed.

- Copy the `Source URL` to your clipboard 

## Advocate Definition
> ⚠️ Ensure you're still signed in.
>
> The message "This could take a few minutes" may never resolve, which is a known bug.

> If you are not receiving any status updates during the key creation process, it is likely that your session has been signed out.
To resolve this, please refresh the page and sign back in, then repeat the steps.

- Select the "Advocate" tab and then click `Source Attachment`
- Define a unique `Public Name`
- Paste the `Source URL` into the field.
- Click `Attach`

After successfully creating all the keys, you will gain access to a copy of all the links associated with the advocate's static data.

Once the process is complete, it is highly recommended to check the `/whois` endpoint to identify and address any potential issues that may arise.

When examining the `/whois` endpoint, it is expected to observe the following state:

```
  GLOBAL_STATE_SOURCE_SET_AVAILABLE_THIS_SOURCE_UNLISTED
```

This state indicates that you have not yet added yourself to the `sources.xml` file. We will resolve this next:


## Trustworthy Sources
> If you haven't already, install the command line utility

If you are operating in a test environment, there are two options to add your node to the Network Map. Firstly, you can utilize Sybil-Test, which allows you to include your node directly. Alternatively, you can point your node to your own trusted sources list and construct a network map based on that. On the other hand, if you are operating in a production environment, a distinct process must be followed to become part of the trustworthy sources list.

In a production environment, establishing trustworthiness is paramount, and it is required to adhere to the guidelines outlined in the trustworthy-sources rulebook.  Here we only address the test-net.

To proceed, copy the source URL from the control panel. Then, using your exonym command line utility, enter the following command:

```
  wallet open <container-name>
  net add_source <source-url>
```

> The wallet needs to be open because in production, you will need to provide a proof. 

Please restart your node.

> Note that while node restarts are currently necessary in some cases, they will not be required in the future.

Note the Global State change:

```
  GLOBAL_STATE_DEFINED_SOURCE_LISTED__THIS_HOST_UNLISTED
```

To resolve this issue, follow the steps below:

1. Log into your control panel as a secondary administrator.
2. Navigate to the "Advocate~Source Attachment" section and copy the advocate's address to the clipboard.
3. Go to the "Source~Advocate Management" section and paste the advocate URL into the "Node URL" field.
4. Optionally, you can add a name for your own management. If left blank, it will use the advocate's assigned name.
5. Click on the "Add" button to complete the process.

If you check the `whois` endpoint you should find: 

```
  "currentLocalState": "LOCAL_STATE_SOURCE_AND_HOST",
  "currentGlobalState": "GLOBAL_STATE_THIS_NODE_LISTED",
```


# So Far... 

Note that we have now achieved the following state:

$$
  H \leftarrow \mathbf{H_0} \leftrightarrow H_{0,0}
$$

As the Source you can add more Advocates, resulting in:

$$
  H \leftarrow \mathbf{H_0} \leftrightarrow \mathbf{H_{0,A}}
$$


When you navigate to your homepage, you should see the displayed rules along with any modifications. If you want to view the original rule interpretations, you can click on each rule to reveal its corresponding URN (Uniform Resource Name).

The QR code is intended for scanning with a smartphone when accessing the rulebook on a desktop.

The "On Device" link is a universal link that directs to the app and initiates an onboarding request.

To disable open subscription and restrict it to privileged actions, set the following environment variable:

```
OPEN_SUBSCRIPTION=false
```
Afterward, you need to facilitate onboarding through a custom server using the API.

For an example of how the home screen looks for privileged onboarding, take a look at the Sybil Test-Net node: 

- [https://node.t0.sybil.exonym.io](https://node.t0.sybil.exonym.io)

Note also, that the rule has the word "**many**" in bold face font. Click on the rule to see the original version.

# Everyday Usage of Rulebooks and Exonym
Until now we have been looking at establishing the Trust Network that we can imagine as a network of networks that govern the context defined by the rulebook document.

From the perspective of everyday Internet usage, Trust Networks operate in the background. With robust governance mechanisms in place, instances of infringements decrease, leading to more trustworthy transactions compared to environments where infringements go unnoticed.

The everyday application of rulebooks and Exonym primarily revolves around authentications. Here are the various authentication scenarios:

1. **Plain Access**: This authentication process results in the generation of an `endonym` for identifying the user on your service. It ensures secure access while preserving user privacy.
   1.  Access can also be delegated to a third-party at the first party authenticates.

2. **Sybil Credential Ownership**: By authenticating Sybil Credential Ownership, you can effectively resist clone accounts and prevent fraudulent activities associated with multiple identities.

3. **Arbitrary Standards or Conduct**: Rulebooks can be used to verify compliance with specific standards or requirements of conduct. This ensures that users adhere to predefined guidelines and maintain a desired level of behavior within your platform.

## Authentications with a Web Server:
Next to look at authentications with a Web server, that is neither a Source, an Advocate, or a Producer.

> Verifiers, in this case, do not require their own identity container. There's no need to register for Sybil authentication.

As part of this local deployment, an example verification web server is provided. To get started with the authentication process, follow these steps:

*Here's what's coming...*
- We'll start the command line utility
- Create a new identity wallet
- Get a Sybil-Test credential
- Subscribe to your local Advocate
- Prove ownership to the example server.

## Wallet Set-up and Test-Net Onboarding

Start the command line utility and create a new wallet
```
exonym(test-net)> wallet create
```

Get a Sybil-Test credential
```
exonym(test-net)--username--> wallet get sybil person
exonym(test-net)--username--> wallet list
```
The list command will show you the credentials in your wallet.

> The revocation handle is provided in the `h` attribute. If you want to save it for testing purposes, make sure to write it down somewhere as you won't be able to access this number later.

To onboard to your Advocate, find the Advocate UID from the advocateUID of your `whois` endpoint.
```
exonym(test-net)--username--> wallet get <advocate-uid>
```

We have now completed the user onboarding: 

$$
  H \leftarrow \mathbf{H_0} \leftrightarrow \mathbf{H_{0,A}} \leftarrow U_A
$$

The wallet you created is Alice $U_A$, she has a Sybil class of `person`.

## Authentication

Navigate to the example container with your browser; the default location is [here](http://localhost:20001). 

Scroll down to the bottom and you will see three tiers of access:
- Basic Auth 
- Switch on Sybil
- Switch on Rulebooks

### Producing an Endonym
If you want to see the endonym created:
```
docker logs sso-example -f
```

To authenticate using basic authentication, follow these steps:

1. Navigate to the [basic authentication](http://localhost:20001/basic.html) page.
2. Click "Sign In"
3. Copy the device link to your clipboard.

Using the command line interface, follow these instructions:

1. Open the command line interface.
2. Type `prove sso` followed by a space.
3. Paste the universal link that you copied earlier into the command line.
4. Press Enter to execute the command.

Once the authentication process is completed, you will notice the word 'Authenticated' displayed on the page. To confirm the establishment of an authenticated session within the context of basic authentication, simply refresh the page. 

In the log files you will see an endonym that is unique to the identity container that authenticated. 

```
urn:endonym:13bd03c4d4942b4bd65fc168b614a488:263094-a359b95f5b1692022d92075974fb1b9a774010d764e5ca6667125b72d92875e4
```
It takes the form:
```
urn:endonym:<scope-representation>:<nibble6>-<pseudonym-hash>
```

- `<scope-representation>` is the last half of a hash of your define scope, so in this case the scope defined by basic auth.
- `nibble6` is the first 3 bytes of the un-hashed cryptographic pseudonym.
- `<pseudonym-hash>` is the hashed pseudonym.

Providing you have been given a token that results in the pseudonym, the authentication is complete.  The producer has control of the identity container associated with it.

**Notes:**
- The process of signing up for a service is identical to the process of authenticating with a service.
- If the identity container that you are authenticating with contains the requested credentials, the authentications are exactly the same for the utility and the user.

### Set-up
Select [Switch on Rulebooks~End Point](http://localhost:20001/exonym/rulebooks) to view the result of the GET at `exonym/<authentication-path>`.

You'll find the code in the exonym wallet repository, however I will detail set-up here.

The `qrPngB64` and `link` contain the `challenge` object as defined.

```json 
{
    "challenge": "zQDmcu08CyAqJvTBQIBCBEd0C08JyVJOj2nCKe9Ozsk\u003d",
    "domain": "http://localhost:20001/accountability-required",
    "sybil": true,
    "honestUnder": {
      "urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c": {
        "rulebookUID": "urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c",
        "sourceBlacklist": [],
        "advocateBlacklist": []
      }
    }
  }
```

## SSO Config
The `SsoConfigWrapper` class provides a wrapper for the Single Sign-On (SSO) configuration. 

`SsoConfigWrapper(URI domain)`

To configure the domain for your service, ensure that it includes the hosting domain and uses TLS (i.e., `https://`). Tokens will be sent to `https://example.com/exonym` for verification.

You have the flexibility to freely define the path to ring-fence your user's resources.

In the following example, we create three namespaces, each generating a unique endonym for every user:

1. `/entry`: This namespace handles basic authentication for user accounts.
2. `/no-clones`: Here, measures are taken to prevent the creation of clone accounts.
3. `/accountability-required`: This namespace enforces accountability for user actions.

By utilizing these namespaces, each endpoint securely segregates the associated data and ensures that it is used only within the specified authentication context, providing a higher level of security for your service.

Example Use Cases:

1. User Account Management:
   - Authenticate a user's account using basic authentication.
   - Set up a separate namespace to allow the user to update and modify credit card details securely.

2. Delegated Authentication:
   - Enable users to delegate authentication to a trusted third-party.
   - Create a dedicated namespace (e.g., "invoices") for users who wish to delegate access to invoices specifically.
   - Authenticate users with a specific endonym to grant them access to the designated invoices while maintaining security for other services.

| Method                          | Description                                                     |
|---------------------------------|-----------------------------------------------------------------|
| addSourceToBlacklist(URI)       | Adds a source to the blacklist in the SSO configuration.        |
| addAdvocateToBlacklist(URI)     | Adds an advocate to the blacklist in the SSO configuration.     |
| requireSybil(boolean)           | Sets whether Sybil authentication is required in the SSO configuration. |
| getConfig()                     | Retrieves the SSO configuration.                                |
| requireRulebook(URI)            | Requires a specific rulebook in the SSO configuration.          |


> Coming Soon: you will soon have the capability to perform multiple authentications using a single token. This means that if someone authenticates under a rulebook, you will also have the option to authenticate them under all pseudonyms that possess lower authority requirements.

_______

__&copy; 2023 Exonym GmbH__

This documentation is licensed under the Mozilla Public License, version 2.0 (the "License"); you may not use this file 
except in compliance with the License.

You may obtain a copy of the License at https://www.mozilla.org/en-US/MPL/2.0/.

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
