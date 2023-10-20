# Data Waves: Explore Graph Databases
Graph Series Part 1 was all about the basics of a graph and for which use cases graphs could be a big added value. Within the demo you already had a sneak preview in the Neo4j graph database. this Repos can be used to spin up the demo and explore neo4j even more or to re-run the queries used during the demo. The Queries used during the demo are available in **QUERIES.md** file.

# Prerequisites
Please make sure Docker is installed and running.

# Installation
1) Please open your cmd/terminal and go to the root folder of this project
2) Run the following command: **docker-compose up**
3) If the build process is done, please confirm neo4j is running on **localhost:7474** in your web browser
4) Start a new terminal/cmd and go to the root of this project and run the following command: **sh import_data.sh**
5) Log on the neo4j browser with username: **neo4j** and password: **neo4jneo4j**
6) Run the following Query: **"CREATE DATABASE p2p"** and wait for a minute to get the database in running state
7) Run the following Query: **:USE p2p**
8) Have fun exploring with the p2p transactions dataset!



