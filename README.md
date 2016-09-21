# Workshop: Running Neo4j Container on Kontena Platform to Analyze Your Twitter Graph

In this workshop, you'll learn how to install and scale the Kontena container and microservices platform. Once the Kontena platform is up and running, we'll move on to installing Neo4j & Twitter graph analyzer, enabling the audience to analyze their own respective Twitter graphs.

Take homes:

- Learn how to install Kontena container management platform
- Experience the power of graph analysis using Neo4j

## Prerequisities

- a laptop with following software installed:
  - Ruby (2.0 or higher)
  - Vagrant (1.6 or higher)
  - VirtualBox (4.0 or higher)
- twitter account

## Setup Kontena Environment


#### Install Kontena CLI

Install the Kontena CLI with Rubygems package manager (included in Ruby):

```
$ gem install kontena-cli kontena-plugin-vagrant
```

> prefix command with sudo  if you don't have permissions to install gems

#### Register Kontena Personal Account

```
$ kontena register
```

#### Install Kontena Master

```
$ kontena vagrant master create
```

#### Login to Kontena Master and Create a Grid

```
$ kontena login --name vagrant http://192.168.66.100:8080
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
$ kontena vagrant node create --instances 1
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
  - Website: http://neo4j-demo.kontena.io/
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

Copy private ip from one of the grid nodes:

```
$ kontena node ls
$ kontena node show <name>
...
  private ip: 172.28.x.x <--
...
```

Open browser with ip from previous step and login to Neo4j:

- username: neo4j
- password: secretzz

Start exploring your Twitter graph!
