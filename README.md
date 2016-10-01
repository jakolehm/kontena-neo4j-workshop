# Workshop: Running Neo4j Container on Kontena Platform to Analyze Your Twitter Graph

In this workshop, you'll learn how to install and scale the [Kontena](https://kontena.io) container and microservices platform. Once the Kontena platform is up and running, we'll move on to installing [Neo4j](https://neo4j.com) & [Twitter](https://twitter.com) graph analyzer, enabling the audience to analyze their own respective Twitter graphs.

Take homes:

- Learn how to install Kontena container management platform
- Experience the power of graph analysis using Neo4j

## Prerequisities

- a laptop with following software installed:
  - Ruby (2.0 or higher)
- Twitter account
- Git clone this repo!

## Setup Kontena Environment


#### Install Kontena CLI

Install the Kontena CLI with Rubygems package manager (included in Ruby):

```
$ gem install kontena-cli kontena-plugin-digitalocean
```

> prefix command with sudo  if you don't have permissions to install gems

#### Register Kontena Personal Account

```
$ kontena register
```

#### Install Kontena Master

Set DigitalOcean token to env:

- linux/mac:
  ```
  $ export DO_TOKEN=cd9215088f02a2a249d93ad7cbc91d22f30dd9a04f40e9a2388f02f36f9ee2bf
  ```
- windows:
  ```
  $ set DO_TOKEN=cd9215088f02a2a249d93ad7cbc91d22f30dd9a04f40e9a2388f02f36f9ee2bf
  ```


Provision Kontena Master:

```
$ kontena digitalocean master create --token $DO_TOKEN --ssh-key ./id_rsa.pub  --region sfo2
```

#### Login to Kontena Master and Create a Grid

```
$ export SSL_IGNORE_ERRORS=true
$ kontena login --name workshop https://ip
```

Enter the login info:

```
Email: your.email@domain.com
Password: *********
 _               _
| | _ ___  _ __ | |_ ___ _ __   __ _
| |/ / _ \| '_ \| __/ _ \ '_ \ / _` |
|   < (_) | | | | ||  __/ | | | (_| |
|_|\_\___/|_| |_|\__\___|_| |_|\__,_|
-------------------------------------
Copyright (c)2016 Kontena, Inc.

Logged in as your.email@domain.com
Welcome! See 'kontena --help' to get started.
```

Create a grid:

```
$ kontena grid create twitter-graph
```

#### Install Kontena Nodes

```
$ kontena digitalocean node create --token $DO_TOKEN --ssh-key ./id_rsa.pub --region sfo2 --size 2gb
```
> use bigger --instances number if your laptop can manage multi-node grid


#### Verify Kontena Nodes Installation

```
$ kontena node ls
```

You should see all installed nodes with status `online`.


## Setup Twitter Application

You need to create a twitter application because twitter importer needs read access to users tweets.

#### Create Twitter Application

- go to: https://apps.twitter.com/
- click "Create New App"
- fill in information:
  - Name: "unique name for your app"
  - Description: kontena-neo4j-demo
  - Website: http://network.graphdemos.com
  - Agree
  - click "Create your Twitter Application"
- go to "Keys and Access Tokens" tab in Twitter app settings
  - click "Create my access token" (bottom of the page)
- go to "Test OAuth" (top right of the page)
  - write tokens to Kontena Vault:

  ```
  $ kontena vault write TWITTER_GRAPH_CONSUMER_KEY <consumer_key>
  $ kontena vault write TWITTER_GRAPH_CONSUMER_SECRET <consumer_secret>
  $ kontena vault write TWITTER_GRAPH_USER_KEY <access_token>
  $ kontena vault write TWITTER_GRAPH_USER_SECRET <access_token_secret>
  ```

## Deploy Neo4j / Importer Stack

#### Configure Neo4j authentication

Write Neo4j auth & password to vault:

```
$ kontena vault write TWITTER_GRAPH_NEO4J_AUTH neo4j/secretzz
$ kontena vault write TWITTER_GRAPH_NEO4J_PASSWORD secretzz
```

#### Deploy Stack (kontena.yml)

Deploy the whole stack (loadbalancer, neo4j, importer):

```
$ TWITTER_USER=<your_twitter_username> kontena app deploy
```

Watch logs:

```
$ kontena app logs -t
```

## Login to Neo4j Browser

Copy public ip from the loadbalancer instance:

```
$ kontena app show loadbalancer
...
  public ip: x.x.x.x <-- this
...
```

Open browser with ip from previous step and login to Neo4j:

- username: neo4j
- password: secretzz

Start exploring your Twitter graph!

## Scaling Neo4J

> You need to have at least 3 nodes in your grid

- add more nodes to your grid

  ```
  $ kontena digitalocean node create --token $DO_TOKEN --ssh-key ./id_rsa.pub --size 2gb --region sfo2
  $ kontena digitalocean node create --token $DO_TOKEN --ssh-key ./id_rsa.pub --size 2gb --region sfo2
  ```

- change `neo` service instance count from 1 to 3
- uncomment `neo` service environment definitions
- redeploy `neo` service

  ```
  $ kontena app deploy neo
  ```

- check from service logs that Neo4j cluster is working

  ```
  $ kontena app logs -t neo
  ```
