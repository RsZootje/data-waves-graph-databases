### show simple data model view of the graph
CALL db.schema.visualization()

### check node counts
CALL apoc.meta.stats() YIELD labels UNWIND keys(labels) AS nodeLabel RETURN nodeLabel, labels[nodeLabel] AS nodeCount

### check relationship counts
CALL apoc.meta.stats() YIELD relTypesCount UNWIND keys(relTypesCount) AS relationshipType RETURN relationshipType, relTypesCount[relationshipType] AS relationshipCount

### show users
MATCH(u:User) RETURN u LIMIT 200

### show users transacting money
MATCH(u:User)-[r:P2P]-(n:User) RETURN u,r,n LIMIT 200

### show users using the same creditcard
MATCH (n:User)-[r:HAS_CC]-(u:Card)-[f:HAS_CC]-(t:User) RETURN n,r,u,f,t LIMIT 100

### check the labeled fraud users
MATCH(u:User) RETURN u.fraudMoneyTransfer AS fraudMoneyTransfer, count(u.fraudMoneyTransfer) AS cnt

### add new fraud user label to the nodes
match (n:User {fraudMoneyTransfer:1})
set n : FlaggedUser
return n

### create a subgraph with desired nodes and edges for analysis and running algorithms
call gds.graph.project(
'subgraph_p2p',
['User','Card', 'Device'],
{
HAS_CC: {orientation: 'UNDIRECTED'},
USED: {orientation: 'UNDIRECTED'},
P2P: {orientation: 'NATURAL', aggregation: 'SINGLE'}
}
)

### show estimate for running Louvain algorithm on subgraph
CALL gds.louvain.write.estimate('subgraph_p2p', { writeProperty: 'louvainCommunityId' })
YIELD nodeCount, relationshipCount, bytesMin, bytesMax, requiredMemory

### write the community id for every node
CALL gds.louvain.write('subgraph_p2p', {
writeProperty: 'louvainCommunityId'
})
YIELD communityCount, modularity, modularities

### show communities with most flagged user within community on top
MATCH (u:User)
WITH u.louvainCommunityId AS community,
count(u) AS cnt,
sum(u.fraudMoneyTransfer) as flaggedCount
RETURN community,
cnt,
flaggedCount,
toFloat(flaggedCount)/toFloat(cnt) AS flaggedRatio
ORDER BY flaggedCount DESC LIMIT 100

### show nodes and edges within a specific community using the community id
MATCH (u)
WHERE u.louvainCommunityId = <louvainCommunityId>
RETURN u
