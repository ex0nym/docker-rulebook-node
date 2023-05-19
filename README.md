
Please see [our wiki](https://github.com/ex0nym/rulebook-node/wiki) for full-documentation.


# Decentralized Rulebooks - Developer Orientation

Decentralized Rulebooks represent a transactional governance framework designed to facilitate the establishment of transaction governance without the need for centralized control. This novel approach empowers users by providing a democratic and distributed mechanism to oversee transactions, effectively eliminating the constraints traditionally associated with centralized systems.

The Rulebook Node repository is responsible for managing Exonym nodes and facilitates 
the setup of a _local_ Decentralized Rulebook Node using Docker containers. It serves as 
the foundational infrastructure for deploying rulebooks using Trust Networks, 
enabling the overall system to function effectively.

## Outcomes
This documentation is designed to serve as a hands-on orientation for developers, aiming to provide a thorough understanding of the network topology and the lifecycle of its users. After going through this guide, developers will be able to:

- Grasp the clear roles and responsibilities associated with each actor within the system.
- Comprehend the user lifecycle in its entirety (command line only), starting from how a user obtains a Sybil credential, interacts with their Exonym Wallet, and what their user experience entails.
- Understand the concept of rulebook inheritance and the polymorphism offered by the rulebook schema. This will enable you to see how rules can be repurposed and reused across different contexts, adding a layer of flexibility to the system.
- Experience firsthand the complete lifecycle of a user, from initial setup, subscription, revocation, to rejoining. This hands-on approach will help you understand how a user navigates through the system.

## **Quick Definitions:** 
| Term | Definition |
| --- | --- |
| [Rulebook Document](https://github.com/ex0nym/rulebook-node/wiki/Rulebook-Definition) | An immutable set of rules formulated subjectively for wide acceptance and set at a universally agreeable standard. This document supports polymorphism, facilitating its federation and reuse. |
| Source | May provide an interpretation of the rulebook and governs a reputation system within the narrow context defined by the rulebook. |
| Advocate | An Advocate is an individual or entity that subscribes to a Source, interprets the rulebook further, and sets criteria for Producers to join their node. |
| Trust Network | A Trust Network refers to a Source/Advocates reputation system, guided by a specific Rulebook document. |
| Sybil | A service that not only resists clone accounts but also governs them by verifying the authenticity of individuals or entities. It further allows the control of authorized clone accounts across different classes. |
| Exonym Wallet | The Exonym Wallet is a digital wallet for managing access to utilities. It empowers users with control over their personal data and transactions. |
| Producer | A Producer is an individual or entity that creates content in adherence to an interpretation of a rulebook. They are accepted into the network by an Advocate, who safeguard Consumers. |
| Consumer | A user who verifies a Proof of Current Honesty. |

## Core Components

The Rulebook Node consists of the following core components:

### Rulebook Node Web Server

- Rulebook Control Panel
  - A user interface for performing administrative tasks, creating administrators, and managing API keys.
- Node API: 
  - Provides detailed operations and functionality for interacting with the Rulebook Node.

### Static Data Replication
A publicly accessible repository that supports both read (HTTPS) and write (SFTP) operations. This repository is used for storing signed Node Data.

### UDP Listener (x0basic)

Listens to Join and Revocation messages from the Trust Network(s) it is connected to.

As developers, understanding the architecture and functionality of the Rulebook Node is crucial as it forms the foundation for building and managing rulebooks within the Exonym ecosystem. These components work together to provide the necessary infrastructure and tools for efficient rulebook deployment and system operation.

Please refer to the appropriate sections and documentation for more detailed information on each component and its specific functionalities.

# Lifecycle Walkthrough

This page will provide a comprehensive walk-through of Exonym's Decentralised Rulebooks. This session is designed to help you grasp the key aspects of this transformative technology. We'll begin by setting up a local rulebook node and appointing administrators. Following that, we'll assist you in getting an API key ready for use and checking your node status. 

Next, we'll focus on creating the Source data by referencing the rulebook. After that, we'll install the Exonym command-line utility and guide you on how to add the source to your list of trustworthy sources. We'll take a deep dive into the Source data generated, and, if you wish, we can delve into interpreting the Rulebook. 

Once we have a solid understanding of the Source data, we'll move on to generating Advocate data using the Source as a reference. Adding the Advocate to the Source will be our next step. Afterward, we'll get you a Sybil Test Credential. 

The final steps of our walk-through will involve subscribing to a Rulebook and helping you gain access to a Service. Throughout this walk-through, we aim to provide a seamless and interactive experience, making it as easy as possible to comprehend and utilize Exonym's Decentralised Rulebooks. 

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

# Wallet Set-up and Test-Net Onboarding

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

# Access Demonstration

> # This is where you left off.

# Static Data
To verify the data that has been created, you can navigate to your FTP site and view the list of published files.

# Source
In the `STATIC_DATA` folder, you will find a `rulebook.json` file:

```json
{
  "rulebookId" : "urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c",
  "description" : {
    "name" : "Trustworthy-Sources-Rulebook",
    "simpleDescriptionEN" : "New sources can be included in the network whitelist by following the standards for selecting and evaluating Advocates.",
    "production" : false
  },
  "rules" : [ {
    "id" : "urn:rule:0:protected:f291700cfcf950552b1625f2f0023fd3bee9c2f1d8d64887ac5301575d23845b:b5ad98c66835bb37",
    "description" : "Host rulebooks that offer _[0]protections_ to consumers, without unnecessarily limiting the _[1]freedoms_ of producers.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "protections"
    }, {
      "modifier" : "public",
      "definition" : "freedoms"
    } ]
  }, {
    "id" : "urn:rule:1:protected:079f08418197576e015964fe1f1f97bef2960a3c9bd222ee76de3f0b10ec60b9:b5ad98c66835bb37",
    "description" : "List only _[0]honest_ Advocates with respect to the scope of the rulebook.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "honest"
    } ]
  }, {
    "id" : "urn:rule:2:public:10a7650e29c4e6a0cc9f8675571005769491d076960755b482d555d8ea181be3:b5ad98c66835bb37",
    "description" : "Only implement rulebooks _[0]with a narrow scope of applicability to a Producer's online activities_.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "with a narrow scope of applicability to a Producer's online activities"
    } ]
  }, {
    "id" : "urn:rule:3:protected:c8e3cb518192cf88aa11cf0d93072730c9e33c2713f187e1e2e402d8ad6f51ff:b5ad98c66835bb37",
    "description" : "Restrict the possibilities of malicious actors to _[0]manipulate the rules for their own benefit_ through interpretations.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "manipulate the rules for their own benefit"
    } ]
  } ],
  "ruleExtensions" : [ ],
  "acceptsSybilClasses" : [ "all" ],
  "penalties" : {
    "accountabilityTypes" : [ "test-net" ]
  }
```

Before a Source or an Advocate is utilized, their static data is inspected. If the information is found to be invalid, any tokens produced under it will fail to verify. Additionally, if a node is corrupted, it is not considered part of the Trust Network.

In the `STATIC_DATA/x-source` folder you will find the following files: 

| File Name                                     | Description                                                            |
|-----------------------------------------------|------------------------------------------------------------------------|
| signatures.xml                                | Binds the files together and ensures secure modifications.             |
| `<rulebook-hash>`.c.xml                       | `CredentialSpecification` for the rulebook.                             |
| `<org-name>-<source-name>`.`<rulebook-hash>`.ni.xml | `TrustNetwork` file defining the location of this node's resources.      |
| `<org-name>-<source-name>`.`<rulebook-hash>`.pp.xml | `PresentationPolicy` file identifying trusted Advocates for this Source. |

> `CredentialSpecification` can be fully defined from inspecting the rulebook.json file and is here for convenience.

### Credential Specification
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<abc:CredentialSpecification xmlns:abc="http://abc4trust.eu/wp2/abcschemav1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:idmx="http://zurich.ibm.com" xmlns:xs="http://www.w3.org/2001/XMLSchema" Version="1.0" KeyBinding="true" Revocable="true">
    <abc:SpecificationUID>urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:c</abc:SpecificationUID>
    <abc:AttributeDescriptions MaxLength="256">
        <abc:AttributeDescription Type="http://abc4trust.eu/wp2/abcschemav1.0/revocationhandle" DataType="xs:integer" Encoding="urn:abc4trust:1.0:encoding:integer:unsigned"/>
    </abc:AttributeDescriptions>
</abc:CredentialSpecification>
```

### Node Information
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<exonym:TrustNetwork xmlns:exonym="urn:exonym:rulebookschema1.0">
    <exonym:NodeLastUpdatedUTC>2023-05-15T09:26:39Z</exonym:NodeLastUpdatedUTC>
    <exonym:NodeInformation>
        <exonym:BroadcastAddress>computer-name:2024</exonym:BroadcastAddress>
        <exonym:NodeName>unique</exonym:NodeName>
        <exonym:NodeUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c</exonym:NodeUID>
        <exonym:Jurisdiction>DE</exonym:Jurisdiction>
        <exonym:RulebookNodeURL>http://computer-name:8080</exonym:RulebookNodeURL>
        <exonym:SourceUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c</exonym:SourceUID>
        <exonym:StaticNodeURL0>https://trust.exonym.io/nu3/unique/x-source</exonym:StaticNodeURL0>
        <exonym:StaticNodeURL1>https://exonym.io/nu3/unique/x-source</exonym:StaticNodeURL1>
        <exonym:StaticSourceURL0>https://trust.exonym.io/nu3/unique/x-source</exonym:StaticSourceURL0>
        <exonym:StaticSourceURL1>https://exonym.io/nu3/unique/x-source</exonym:StaticSourceURL1>
    </exonym:NodeInformation>
    <exonym:NodeInformationUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:ni</exonym:NodeInformationUID>
</exonym:TrustNetwork>
```

### Presentation Policy

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<abc:PresentationPolicy xmlns:abc="http://abc4trust.eu/wp2/abcschemav1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:idmx="http://zurich.ibm.com" xmlns:xs="http://www.w3.org/2001/XMLSchema" PolicyUID="urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:pp">
    <abc:Pseudonym Exclusive="false" Scope="urn:io:exonym" Alias="urn:io:exonym"/>
</abc:PresentationPolicy>
```


### Signatures.xml
Binds the files together and ensures secure modifications.
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<exonym:KeyContainer xmlns:exonym="urn:exonym:rulebookschema1.0">
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:keys:rsa:trust-network-root</exonym:KeyUID>
        <exonym:PublicKey>MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA4+gnq1d+JlJtdTSXHtjkg1pfbz00fylDteQF8aCUmG2OdebjUq1IcoV3gmaoeh2UXUhCJLT3djENhHwxdSrcLlEdFxTAXZsh1OZN1JW+LngWpDc4eHy4eyuHwLMwo48u4kjPcZAbALB7MojHXl/W9sOc2NB4c+vkoicPX5ys7MVdgAdROYYOekZV3lBcgkDBJICmU6Jxk+HbigHzqrFwTqsHQMkuL/d17p3/sBnLiq4x3UMtLv65LILALQZyw8Mpa22Uc8MwaBHFujepoXu9E2BU18h3xiFN7s4tl2b/jwy+Vm1ZN4tgI2mPydUQJwFut1E+VzNdgnkWleQHmd+4mQIDAQAB</exonym:PublicKey>
        <exonym:Signature>XDhTJiy3sok2kxsMMD4UVpecKjXFROufXo5UTjZlIlgGa+IatLzIHuoEgbek8poZ1aF+9JnNzdcklKdVkkcu7NbMjZMhmmZNHn9dq12j6cZvJbLGcDotxWvBXq1Jdvziso71OCOnciIXNoGmNHJvRp/fFbt1QCD+AmmOGHG1rR9l7nDKHruNMuWUepN7HOa7oaHM7sDUB/i5w07RofHupSWcK8Gg/r93p1Izgk17WO2Ei5vrckxDmDeTJrW1h5gwl8BsQFKmmM602jM7Xn4j2UTuwL5UxVwAsutWQzypyLRfZAjWBZFeB83l0ft6Pgzom65n3+/x8aG+MomDlJGsXA==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:pp</exonym:KeyUID>
        <exonym:Signature>bpPITGvVFi9rzsrdvx/T8OgsHouZ0c46l+kERJ1Q0yRFEqk7cBEF9wXqURogL5iUZaEoS1RLh57KgDbM6OqWttryCMPX4gZuju3uw3+xEO4JgUyCXA8aAOjIfi5DR3IOTrjfkQlV9+n8YMHo71xjFqffJ5yTUzC+I/lkQD89QDojVHTziYW3dhSAwYMiZQ1vd5iwjfwWDBrBfvsGVPiDP+EnngY8qsrQKvufeHz38sJTwEIblgDhdoBA1+QJn1ZPFCZ4H6myNLMuwYOmm4/Gc93yyMu9LbNkegh/fh7dBkLfHv0ebf6nBoDLRfBn8JRhplqXHNnAwsxJwbSiakMVRA==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:ni</exonym:KeyUID>
        <exonym:Signature>svi8Z5GpqmObT2SN3huADzMhVF2qllPGE4GDdvPy/Uz7KC8Or0GkKXr+z2PCrJXbrt9Jm208yQbJJukSjvFK3Lu6tySoy0R0DWf7U84Fdwm/+C+2DCCjqwgT+v2FalSS30Pe09pXGuccSGZdl2feP7aiCAuz0LqGMLGwdKDG2/woT0WJ1Te2YTLrnveN9tGlAjginy0UqU5TBZnOQ/cuwwcXTAJ2l0T1kRPNULnA32iWtsxtbm9/Hh9FAJRXej7VwqoNG4Sh+CpITyKMXTdx5DtYjOJpNTI/XPYMMi0p0jDgQybhuzyCVDqc3RqvEMPzOQcRe+fY7IZjs/ynqXJoQw==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:c</exonym:KeyUID>
        <exonym:Signature>e2ez6O43ay0qtSiinAwKhb2Gmbe0S+gF70WHWMnvLAo5XmwxpCCXKLBxNjNA8cZXesd3+FR1mccEFZUUImKmTMRo9p2ZEdVO3dKk3i4Vq3ffCEV3Ty78az7RAcbi6+fHk0FEAQeTBodQ5nbn9sTwgplp2sXwhccl5iYItyttz0u5fxT4/+cAhVB8Tr7lr7iTbTT8yApxio5EpfuYCT2fzSAtqwyAgsmpUaH7ti33tzvoi6hGg4m0Yfeu41UrgZajoH/9YXU2MzLQ++A4c/44AW1JRbeckHXAdnr8RgIQ8bWsMiRRdw1VyguYceu7f/wgir66CJL8+wzwiEKI56AmbA==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:sig-checksum</exonym:KeyUID>
        <exonym:Signature>cOW1pV/LurwQ1WjP9gS5ETqremqjEuFn/VniIr7zOJqRQRu65NoE9lNVwXqS4VoQpkexPEuCH37v0HnjKVq0HXpQtFmalwVfpwOh2hBgHv/ylG2vjU8A2VZFF58mNp/mDo0MAFLlmFP0/6DTawAS/HDGzo/tidv14oeK9LEymwMQom2fcpG9SwxiJlCUHLGz+KElo/AtJe18T4O+jsbuxHZsWHKCA+gVq3XrNA1mqdpGSkUSTJfHiYuYuiqHWyPo3TEHFMm82k2nPSZy6n6Y9tTCu17EduSb+T3ZJ6KRuAd4h80GbNTjVnC1BzV5/zJSXe3O2ki3P2fSob9BX0ou4Q==</exonym:Signature>
    </exonym:KeyPairs>
    <lastUpdateTime>2023-05-15T09:26:39Z</lastUpdateTime>
</exonym:KeyContainer>
```



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




_______

__&copy; 2023 Exonym GmbH__

This documentation is licensed under the Mozilla Public License, version 2.0 (the "License"); you may not use this file 
except in compliance with the License.

You may obtain a copy of the License at https://www.mozilla.org/en-US/MPL/2.0/.

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
