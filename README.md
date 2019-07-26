# awsmBloodhoundCustomQueries
Collection of cyhpers for bloodhound  
I didnt write most of these, this is a culmination of items gathered from various gists, githubs, and threads in the #cypher-queries bloodhound channel.  I'll try to give props if I can.    

There are 2 ways to view bloodhound results.   
1-In the UI with the sweet graphing and attack path views  
2-Using the neo4j interface directly. These return results in tables, akin to a SQL Database look. 

The customqueries.json file is used to store queries in the Bloodhound UI so you can easily reuse them.  
----Generally these are queries that return paths  
The CYPHER section is queries you would put into the neo4j interface and get results in tables.   
----Generaly these queries are used when you want to return lists or get percentages for report things  
  
Reading materials / References::  
https://github.com/Scoubi/BloodhoundAD-Queries  
https://github.com/porterhau5/BloodHound-Owned  
https://porterhau5.com/blog/extending-bloodhound-track-and-visualize-your-compromise/  
https://blog.cptjesus.com/posts/introtocypher#building-on-top   
  
TODO:  
de-dupe entries  
organize somehow... 
Move neo4j cipher section to its own doc
  
## Custom Queries (Bloodhound UI)

Some customqueries that were removed from the default list, as well as some others, can be found in the *customqueries.json* file.   
Copy/paste the entire thing into your own customqueries file and hit refresh next to "Custom Queries" to see them show up.    

### The customqueries file locations by OS (in default setup):   

Windows : %USERPROFILE%\AppData\Roaming\bloodhound\customqueries.json  
OSX: ~/Library/Application Support/bloodhound/customqueries.json  
NIX: ~/.config/bloodhound/customqueries.json  

-----------------------------------------------------------------------

## Custom Queries (Neo4j Web Interface) 
 A.K.A.
 
-----------------------------------------------------------------------
                    ######     ##    ##    ########     ##     ##    ########    ########  
                   ##    ##     ##  ##     ##     ##    ##     ##    ##          ##     ## 
                   ##            ####      ##     ##    ##     ##    ##          ##     ## 
                   ##             ##       ########     #########    ######      ########  
                   ##             ##       ##           ##     ##    ##          ##   ##   
                   ##    ##       ##       ##           ##     ##    ##          ##    ##  
                    ######        ##       ##           ##     ##    ########    ##     ## 
                                                               
-----------------------------------------------------------------------


These are mostly for running in the web interface of neo4j. ~ http://localhost:7474  
Some need to be modified based on the domain/user/group you are looking for  
i.e  change "EXAMPLE.COM" or "g.name contains "DOMAIN ADMINS""
  

-----------------------------------------------------------------------                                                              
T.o.C

1. [Get a count of computers that do not have admins](#get-a-count-of-computers-that-do-not-have-admins)  
2. [Get the names of computers without admins, sorted in alphabetical order](#get-the-names-of-computers-without-admins,-sorted-in-alphabetical-order)  
3. [Return a list of users who have admin rights on at least one system either explicitly or through group membership](#return-a-list-of-users-who-have-admin-rights-on-at-least-one-system-either-explicitly-or-through-group-membership)  
4. [Return username and number of computers that username is admin for, for top N users](#return-username-and-number-of-computers-that-username-is-admin-for,-for-top-n-users)  
5. [Show all users that are administrator on more than one machine](#show-all-users-that-are-administrator-on-more-than-one-machine)  
6. [Show all users that are administrative on at least one machine, ranked by the number of machines they are admin on.](#show-all-users-that-are-administrative-on-at-least-one-machine,-ranked-by-the-number-of-machines-they-are-admin-on.)  
7. [This will return cross domain 'HasSession' relationships](#this-will-return-cross-domain-'hassession'-relationships)  
8. [Find all other Rights Domain Users shouldn't have](#find-all-other-rights-domain-users-shouldn't-have)  
9. [Show Kerberoastable high value targets](#show-kerberoastable-high-value-targets)  
10. [Show computers where Domain Admins are logged in](#show-computers-where-domain-admins-are-logged-in)  
11. [Show groups with most localAdmin](#show-groups-with-most-localadmin)  
12. [List of unique users with a path (no "GetChanges" path) to a Group tagged as "highvalue"](#list-of-unique-users-with-a-path-(no-"getchanges"-path)-to-a-group-tagged-as-"highvalue")  
13. [Return all users which can rdp to any system, if they belong to adm or svr accounts](#return-all-users-which-can-rdp-to-any-system,-if-they-belong-to-adm-or-svr-accounts)  
14. [Show the number of _users_ that have admin rights on each computer, in descending order](#show-the-number-of-_users_-that-have-admin-rights-on-each-computer,-in-descending-order)  
15. [Stats percentage of enabled users that have a path to a high value group](#stats-percentage-of-enabled-users-that-have-a-path-to-a-high-value-group)  
16. [Shows machines that allow delegation that arent DCs](#shows-machines-that-allow-delegation-that-arent-dcs)  
17. [Groups with most local admin](#groups-with-most-local-admin)  
18. [Find principles with DCSync rights](#find-principles-with-dcsync-rights)  
19. [Find all Windows 7 computers](#find-all-windows-7-computers)  
20. [Find every user object where the "userpassword" attribute is populated. Neo4j web console only.](#find-every-user-object-where-the-"userpassword"-attribute-is-populated.-neo4j-web-console-only.)  
21. [Find every user that doesn't require kerberos pre-authentication. Neo4j web console only.](#find-every-user-that-doesn't-require-kerberos-pre-authentication.-neo4j-web-console-only.)  
22. [Return any group where the name of the group contains the string "SQL" followed by the string "ADMIN". This will match "SQL ADMINS" and will also match "SQL 2017 ADMINS". Neo4j ](#return-any-group-where-the-name-of-the-group-contains-the-string-"sql"-followed-by-the-string-"admin".-this-will-match-"sql-admins"-and-will-also-match-"sql-2017-admins".-neo4j-)  
23. [Find every OU that contains the string "CITRIX".](#find-every-ou-that-contains-the-string-"citrix".)  
24. [Find all computers with sessions from users of a different domain (Looking for cross-domain compromise opportunities). Neo4j web console only.](#find-all-computers-with-sessions-from-users-of-a-different-domain-(looking-for-cross-domain-compromise-opportunities).-neo4j-web-console-only.)  
25. [Find all users trusted to perform constrained delegation, return in order of the number of target computers. Neo4j web console only.](#find-all-users-trusted-to-perform-constrained-delegation,-return-in-order-of-the-number-of-target-computers.-neo4j-web-console-only.)  
26. [Return each OU in the database in order of the number of computers in that OU. Neo4j web console only.](#return-each-ou-in-the-database-in-order-of-the-number-of-computers-in-that-ou.-neo4j-web-console-only.)  
27. [Return the name of every computer in the database where at least one SPN for the computer contains the string "MSSQL". Neo4j web console only.](#return-the-name-of-every-computer-in-the-database-where-at-least-one-spn-for-the-computer-contains-the-string-"mssql".-neo4j-web-console-only.)  
28. [Find groups with both users and computers that belong to the group. Neo4j web console only.](#find-groups-with-both-users-and-computers-that-belong-to-the-group.-neo4j-web-console-only.)  
29. [Return each OU in the database that contains a Windows Server computer. Return rows where the columns are the name of the OU, the name of the computer, and the operating system ](#return-each-ou-in-the-database-that-contains-a-windows-server-computer.-return-rows-where-the-columns-are-the-name-of-the-ou,-the-name-of-the-computer,-and-the-operating-system-)  
30. [Find any computer that is NOT a domain controller that is trusted to perform unconstrained delegation. Neo4j web console only.](#find-any-computer-that-is-not-a-domain-controller-that-is-trusted-to-perform-unconstrained-delegation.-neo4j-web-console-only.)  
31. [Find every instance of a computer account having local admin rights on other computers. Return in descending order of the number of computers the computer account has local ](#find-every-instance-of-a-computer-account-having-local-admin-rights-on-other-computers.-return-in-descending-order-of-the-number-of-computers-the-computer-account-has-local-)  
32. [Find users who are not marked as "Sensitive and Cannot Be Delegated" that have Administrative access to a computer, and where those users have sessions on servers with ](#find-users-who-are-not-marked-as-"sensitive-and-cannot-be-delegated"-that-have-administrative-access-to-a-computer,-and-where-those-users-have-sessions-on-servers-with-)  
33. [Gather the computers where the user is AdminTo and the computers with unconstrained delegation in lists (for better presentation and structure)](#gather-the-computers-where-the-user-is-adminto-and-the-computers-with-unconstrained-delegation-in-lists-for-better-presentation-and-structure)  
34. [DOMAIN USERS is Admin of Computer](#domain-users-is-admin-of-computer)  
35. [Find Shortest Path from DOMAIN USERS to High Value Targets](#find-shortest-path-from-domain-users-to-high-value-targets)  
36. [Find ALL Path from DOMAIN USERS to High Value Targets](#find-all-path-from-domain-users-to-high-value-targets)  
37. [Find all other right Domain Users shouldn’t have](#find-all-other-right-domain-users-shouldn’t-have)  
38. [Kerberoastable Accounts](#kerberoastable-accounts)  
39. [Kerberoastable Accounts member of High Value Group](#kerberoastable-accounts-member-of-high-value-group)  
40. [Find WORKSTATIONS where Domain Users can RDP To](#find-workstations-where-domain-users-can-rdp-to)  
41. [Find SERVERS where Domain Users can RDP To](#find-servers-where-domain-users-can-rdp-to)  
42. [Top 10 Computers with Most Admins](#top-10-computers-with-most-admins)  
43. [How many High Value Target a Node can reach](#how-many-high-value-target-a-node-can-reach)  
44. [All DA Account Sessions](#all-da-account-sessions)  
45. [DA Sessions to NON DC](#da-sessions-to-non-dc)  
46. [Count on how many non DC machines DA have sessions](#count-on-how-many-non-dc-machines-da-have-sessions)  
47. [EXCLUDE PATH (Edge) on the fly](#exclude-path-(edge)-on-the-fly)  
48. [Set SPN to a User](#set-spn-to-a-user)  
49. [Add DOMAIN USERS as Admin to COMPxxxx](#add-domain-users-as-admin-to-compxxxx)  
50. [Test our New Relationship](#test-our-new-relationship)  
51. [Delete a Relationship](#delete-a-relationship)  
52. [Shortest Path from Domain Users to Domain Admins](#shortest-path-from-domain-users-to-domain-admins)  
53. [Shortest Path from Domain Users to Domain Admins (No AdminTo)](#shortest-path-from-domain-users-to-domain-admins-(no-adminto))  
54. [Find Admin Group not tagged highvalue](#find-admin-group-not-tagged-highvalue)  
55. [Set Admin Group as highvalue](#set-admin-group-as-highvalue)  
56. [Remore Admin User highvalue](#remore-admin-user-highvalue)  
57. [If you want to enumerate all available properties](#if-you-want-to-enumerate-all-available-properties)  
58. [List all computers with unconstraineddelegation](#list-all-computers-with-unconstraineddelegation)  
59. [Computer where the most users can RDP to ](#computer-where-the-most-users-can-rdp-to)  
60. [Average Length of Path](#average-length-of-path)  
61. [Count how many Users have a path to DA](#count-how-many-users-have-a-path-to-da)  
62. [Percent of Users that have a path to DA](#percent-of-users-that-have-a-path-to-da)  
63. [Return users with shortest paths to high value targets by name](#return-users-with-shortest-paths-to-high-value-targets-by-name)  

-----------------------------------------------------------------------


### Get a count of computers that do not have admins
```
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins
RETURN COUNT(c2)
```

### Get the names of computers without admins, sorted in alphabetical order
```
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins
RETURN c2.name
ORDER BY c2.name ASC
```

### Return a list of users who have admin rights on at least one system either explicitly or through group membership
```
MATCH (u:User)-[r:AdminTo|MemberOf*1..]->(c:Computer
RETURN u.name
```

### Return username and number of computers that username is admin for, for top N users
```
MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name as n, COUNT(DISTINCT(C)) as c 
RETURN n,c
ORDER BY c DESC LIMIT 5
```

### Show all users that are administrator on more than one machine
```
MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name as n, COUNT(DISTINCT(C)) as c 
WHERE c>1
RETURN n
ORDER BY c DESC
```

### Show all users that are administrative on at least one machine, ranked by the number of machines they are admin on.
```
MATCH (u:User)
WITH u
OPTIONAL MATCH (u)-[r:AdminTo]->(c:Computer)
WITH u,COUNT(c) as expAdmin
OPTIONAL MATCH (u)-[r:MemberOf*1..]->(g:Group)-[r2:AdminTo]->(c:Computer)
WHERE NOT (u)-[:AdminTo]->(c)
WITH u,expAdmin,COUNT(DISTINCT(c)) as unrolledAdmin
RETURN u.name,expAdmin,unrolledAdmin,expAdmin + unrolledAdmin as totalAdmin
ORDER BY totalAdmin ASC
```

### This will return cross domain 'HasSession' relationships
```
MATCH p=((S:Computer)-[r:HasSession*1]->(T:User)) 
WHERE NOT S.domain = T.domain
RETURN p
```

### Find all other Rights Domain Users shouldn't have
```
MATCH p=(m:Group)->[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) 
WHERE m.name STARTS WITH ‘DOMAIN USERS’ 
RETURN p
```

### Show Kerberoastable high value targets
```
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
```


### Show computers where Domain Admins are logged in
```
MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@EXAMPLE.COM"})
WITH n as DAaccount
MATCH (c:Computer)-[b:MemberOf]->(t:Group) WHERE NOT t.name = "DOMAIN CONTROLLERS@EXAMPLE.COM"
WITH c as NonDC
MATCH p = (NonDC)-[:HasSession]->(DAaccount)
```


### Show groups with most localAdmin
```
MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g,COUNT(c) as expAdmin
OPTIONAL MATCH (g)-[r:MemberOf*1..]->(a:Group)-[r2:AdminTo]->(c:Computer)
WITH g,expAdmin,COUNT(DISTINCT(c)) as unrolledAdmin
RETURN g.name,expAdmin,unrolledAdmin, expAdmin + unrolledAdmin as totalAdmin
ORDER BY totalAdmin DESC 
```

### List of unique users with a path (no "GetChanges" path) to a Group tagged as "highvalue"
```
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name),u.enabled
order by u.name
```


### Return all users which can rdp to any system, if they belong to adm or svr accounts
```
MATCH (c:Computer) where c.name contains 'xxxxxx'
MATCH (n:User)-[r:MemberOf]->(g:Group)  WHERE g.name = 'DOMAIN ADMINS@EXAMPLE.COM'
optional match (g:Group)-[:CanRDP]->(c)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c) where u1.enabled = true and u1.name contains 'ADM' OR u1.name contains 'SVR'
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c) where u2.enabled = true and u2.name contains 'ADM' OR u2.name contains 'SVR'
WITH COLLECT(u1) + COLLECT(u2) + collect(n) as tempVar,c
UNWIND tempVar as users
RETURN c.name,COLLECT(users.name) as usernames
ORDER BY usernames  desc 
```

### Show the number of _users_ that have admin rights on each computer, in descending order
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:AdminTo]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c)
WITH COLLECT(u1) + COLLECT(u2) AS tempVar,c
UNWIND tempVar AS admins
RETURN c.name AS computerName,COUNT(DISTINCT(admins)) AS adminCount
ORDER BY adminCount DESC
```

### Stats percentage of enabled users that have a path to a high value group
```
MATCH (u:User {domain:'EXAMPLE.COM',enabled:True})
MATCH (g:Group {domain:'EXAMPLE.COM'})
WHERE g.highvalue = True
WITH g, COUNT(u) as userCount
MATCH p = shortestPath((u:User {domain:'EXAMPLE.COM',enabled:True})-[*1..]->(g))
RETURN toint(100.0 * COUNT(distinct u) / userCount)
```

### Shows machines that allow delegation that arent DCs
```
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group)  
WHERE NOT t.name =~ "(?i)DOMAIN CONTROLLERS*."  
WITH c as NonDC  
MATCH (NonDC {unconstraineddelegation:true}) RETURN NonDC.name
```  

### Groups with most local admin
```
MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g,COUNT(c) as expAdmin
RETURN g.name,expAdmin,expAdmin as totalAdmin
ORDER BY totalAdmin DESC
```


### Find principles with DCSync rights
```
MATCH p=(n1)-[:MemberOf|GetChanges*1..]->(u:Domain {name: {result}}) WITH p,n1 
MATCH p2=(n1)-[:MemberOf|GetChangesAll*1..]->(u:Domain {name: {result}}) WITH p,p2 
MATCH p3=(n2)-[:MemberOf|GenericAll|AllExtendedRights*1..]->(u:Domain {name: {result}}) 
RETURN p,p2,p3
```


### Find all Windows 7 computers
```
MATCH (c:Computer)
WHERE toUpper(c.operatingsystem) CONTAINS "Windows 7"
RETURN c
```


### Find every user object where the "userpassword" attribute is populated. Neo4j web console only.
```
MATCH (u:User)
WHERE NOT u.userpassword IS null
RETURN u.name,u.userpassword
```


### Find every user that doesn't require kerberos pre-authentication. Neo4j web console only.
```
MATCH (u:User {dontreqpreauth: true})
RETURN u.name
```


### Return any group where the name of the group contains the string "SQL" followed by the string "ADMIN". This will match "SQL ADMINS" and will also match "SQL 2017 ADMINS". Neo4j web console only.
```
MATCH (g:Group)
WHERE g.name =~ '(?i).*SQL.*ADMIN.*'
RETURN g.name
```


### Find every OU that contains the string "CITRIX".
```
MATCH (o:OU)
WHERE o.name =~ "(?i).*CITRIX.*"
RETURN o
```


### Find all computers with sessions from users of a different domain (Looking for cross-domain compromise opportunities). Neo4j web console only.
```
MATCH (c:Computer)-[:HasSession]->(u:User {domain:'FOO.BAR.COM'})
WHERE NOT c.domain = u.domain
RETURN u.name,COUNT(c)
```


### Find all users trusted to perform constrained delegation, return in order of the number of target computers. Neo4j web console only.
```
MATCH (u:User)-[:AllowedToDelegate]->(c:Computer)
RETURN u.name,COUNT(c)
ORDER BY COUNT(c) DESC
```


### Return each OU in the database in order of the number of computers in that OU. Neo4j web console only.
```
MATCH (o:OU)-[:Contains]->(c:Computer)
RETURN o.name,o.guid,COUNT(c)
ORDER BY COUNT(c) DESC
```


### Return the name of every computer in the database where at least one SPN for the computer contains the string "MSSQL". Neo4j web console only.
```
MATCH (c:Computer)
WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL")
RETURN c.name,c.serviceprincipalnames
ORDER BY c.name ASC
```


### Find groups with both users and computers that belong to the group. Neo4j web console only.
```
MATCH (c:Computer)-[r:MemberOf*1..]->(groupsWithComps:Group)
WITH groupsWithComps
MATCH (u:User)-[r:MemberOf*1..]->(groupsWithComps)
RETURN DISTINCT(groupsWithComps) as groupsWithCompsAndUsers
```


### Return each OU in the database that contains a Windows Server computer. Return rows where the columns are the name of the OU, the name of the computer, and the operating system of the computer. Neo4j web console only.
```
MATCH (o:OU)-[:Contains]->(c:Computer)
WHERE toUpper(o.name) STARTS WITH "WINDOWS SERVER"
RETURN o.name,c.name,c.operatingsystem
```


### Find any computer that is NOT a domain controller that is trusted to perform unconstrained delegation. Neo4j web console only.
```
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH COLLECT(c1.name) AS domainControllers
MATCH (c2:Computer {unconstraineddelegation:true})
WHERE NOT c2.name IN domainControllers
RETURN c2.name,c2.operatingsystem
ORDER BY c2.name ASC
```


### Find every instance of a computer account having local admin rights on other computers. Return in descending order of the number of computers the computer account has local admin rights on.
```
MATCH (c1:Computer)
OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer)
OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer)
WITH COLLECT(c2) + COLLECT(c3) AS tempVar,c1
UNWIND tempVar AS computers
RETURN c1.name,COUNT(DISTINCT(computers))
ORDER BY COUNT(DISTINCT(computers)) DESC
```


### Find users who are not marked as "Sensitive and Cannot Be Delegated" that have Administrative access to a computer, and where those users have sessions on servers with Unconstrained Delegation enabled  
```
MATCH (n {sensitive:FALSE})-[:MemberOf*0..5]->(g:Group)-[:AdminTo]->(c:Computer) 
WITH n,c MATCH (d:Computer {unconstraineddelegation:TRUE})-[:HasSession]->(n:User) 
RETURN n.name AS user,
c.name as AdminTo ,
d.name as TicketLocation
```
----or this one does sort of the same thing?----- 
```
MATCH (u:User {sensitive:false})-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c1:Computer)
WITH u,c1
MATCH (c2:Computer {unconstraineddelegation:true})-[:HasSession]->(u)
RETURN u.name AS user,c1.name AS AdminTo,c2.name AS TicketLocation
ORDER BY user ASC
```

### Gather the computers where the user is AdminTo and the computers with unconstrained delegation in lists (for better presentation and structure)
```
MATCH (u:User {sensitive:false})-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c1:Computer)
WITH u,c1
MATCH (c2:Computer {unconstraineddelegation:true})-[:HasSession]->(u)
RETURN u.name AS user,COLLECT(DISTINCT(c1.name)) AS AdminTo,COLLECT(DISTINCT(c2.name)) AS TicketLocation
ORDER BY user ASC
```

### DOMAIN USERS is Admin of Computer 
```
MATCH p=(m:Group)-[r:AdminTo]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
```

### Find Shortest Path from DOMAIN USERS to High Value Targets
```
MATCH (g:Group),(n {highvalue:true}),p=shortestPath((g)-[*1..]->(n)) 
WHERE g.name STARTS WITH 'DOMAIN USERS' 
RETURN p
```

### Find ALL Path from DOMAIN USERS to High Value Targets
```
MATCH (g:Group) WHERE g.name STARTS WITH 'DOMAIN USERS'  MATCH (n {highvalue:true}),p=shortestPath((g)-[r*1..]->(n)) return p
```

### Find all other right Domain Users shouldn’t have
```
MATCH p=(m:Group)-[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
```

### Kerberoastable Accounts
```
MATCH (n:User)WHERE n.hasspn=true RETURN n
```

### Kerberoastable Accounts member of High Value Group
```
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
```

### Find WORKSTATIONS where Domain Users can RDP To
```
match p=(g:Group)-[:CanRDP]->(c:Computer) 
where g.name STARTS WITH 'DOMAIN USERS' AND NOT c.operatingsystem CONTAINS 'Server' 
return p
```

### Find SERVERS where Domain Users can RDP To
```
match p=(g:Group)-[:CanRDP]->(c:Computer) 
where g.name STARTS WITH 'DOMAIN USERS' 
AND c.operatingsystem CONTAINS 'Server' 
return p
```

### Top 10 Computers with Most Admins
```
MATCH (n:User),(m:Computer), (n)-[r:AdminTo]->(m) WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name='' WITH m, count(r) as rel_count order by rel_count desc LIMIT 10 MATCH p=(m)<-[r:AdminTo]-(n) RETURN p
```


### How many High Value Target a Node can reach
```
MATCH p = shortestPath((n)-[*1..]->(m {highvalue:true}))
WHERE NOT n = m
RETURN DISTINCT(m.name),LABELS(m)[0],COUNT(DISTINCT(n))
ORDER BY COUNT(DISTINCT(n)) DESC
```
 
### All DA Account Sessions
```
MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) 
MATCH p = (c:Computer)-[:HasSession]->(n) 
return p
```

### DA Sessions to NON DC
```
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group) 
WHERE NOT t.name = "DOMAIN CONTROLLERS@TESTLAB.LOCAL"
WITH c as NonDC
MATCH p=(NonDC)-[:HasSession]->(n:User)-[:MemberOf]->
(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"})
RETURN DISTINCT (n.name) as Username, COUNT(DISTINCT(NonDC)) as Connexions
ORDER BY COUNT(DISTINCT(NonDC)) DESC
```

### Count on how many non DC machines DA have sessions
```
MATCH (c:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH c.name as DomainControllers
MATCH p = (c2:Computer)-[:HasSession]->(u:User)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-512" AND NOT c2.name in DomainControllers
RETURN DISTINCT(u.name),COUNT(DISTINCT(c2))
ORDER BY COUNT(DISTINCT(c2)) DESC
```

### EXCLUDE PATH (Edge) on the fly
```
MATCH (n:User),(m:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((n)-[r*1..]->(m)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
RETURN p
```


### Set SPN to a User
```
MATCH (n:User {name:"JNOA00093@TESTLAB.LOCAL"}) SET n.hasspn=true
```


### Add DOMAIN USERS as Admin to COMPxxxx
```
MERGE (n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}) WITH n MERGE (m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) WITH n,m MERGE (n)-[:AdminTo]->(m)
```

### Test our New Relationship 
```
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
RETURN p
```
#### Might try to replace AdminTo by 1**

### Delete a Relationship
```
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->
(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
DELETE r
```


### Shortest Path from Domain Users to Domain Admins
```
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[r*1..]->(n)) 
return p
```


### Shortest Path from Domain Users to Domain Admins (No AdminTo)
```
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[r*1..]->(n)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
return p
```


### Find Admin Group not tagged highvalue
```
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
RETURN g.name
```

### Set Admin Group as highvalue
```
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
SET g.highvalue=true
```

### Remore Admin User highvalue
```
MATCH p=(g:Group {highvalue: true})<-[:MemberOf]-(u:User)
WHERE g.name CONTAINS "ADMIN"
SET g.highvalue = NULL
```

### If you want to enumerate all available properties
``` 
Match (n:Computer) return properties(n)
```

### List all computers with unconstraineddelegation
```
MATCH (c:Computer {unconstraineddelegation:true})
return c.name
```

### Computer where the most users can RDP to 
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name,COUNT(DISTINCT(users))
ORDER BY COUNT(DISTINCT(users)) DESC
```

### Average Length of Path
```
MATCH (g:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'})
MATCH p = shortestPath((u:User)-[r:AdminTo|MemberOf|HasSession*1..]->(g))
WITH EXTRACT(n in NODES(p) | LABELS(n)[0]) as pathNodes
WITH FILTER(x IN pathNodes WHERE x = "Computer") as filteredPathNodes
RETURN AVG(LENGTH(filteredPathNodes))
```

### Count how many Users have a path to DA
```
MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
RETURN COUNT (DISTINCT(u))
```

### Percent of Users that have a path to DA
```
OPTIONAL MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
OPTIONAL MATCH (uT:User)
WITH COUNT (DISTINCT(uT)) as uTotal, COUNT (DISTINCT(u)) as uHasPath
RETURN uHasPath / uTotal * 100 as Percent
```


### Return users with shortest paths to high value targets by name
```
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name),u.enabled
order by u.name
```
