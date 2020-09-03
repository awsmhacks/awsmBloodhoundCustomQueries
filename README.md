
This repo contains a [customqueries.json](https://raw.githubusercontent.com/awsmhacks/awsmBloodhoundCustomQueries/master/customqueries.json) file with several examples you can use and/or modify.  
Further down in the [Cypher](#cypher-queries-neo4j-web-interface)  section I have several queries that can be used in the neo4j interface.  

I didnt write most of these, this is a culmination of items gathered from various gists, githubs, and threads in the #cypher-queries bloodhound channel.    
 
## Custom Queries (Bloodhound UI)  

Bloodhound's UI allows you to create 'custom queries' to make repeating often used queries easy.  
These are stored in a customqueries.json file.  
- Generally these are queries that return paths  

Some queries that were removed from the default list, as well as other useful queries, can be found in the [customqueries.json](https://raw.githubusercontent.com/awsmhacks/awsmBloodhoundCustomQueries/master/customqueries.json) file.   
  
Copy/paste the entire thing into your own customqueries file and hit refresh next to "Custom Queries" to see them show up in the bloodhound UI.    

#### The customqueries file locations by OS (in default setup):   

Windows : %USERPROFILE%\AppData\Roaming\bloodhound\customqueries.json  
OSX: ~/Library/Application Support/bloodhound/customqueries.json  
NIX: ~/.config/bloodhound/customqueries.json  

-----------------------------------------------------------------------

## Cypher Queries (Neo4j Web Interface) 
  
The CYPHER section are queries you would put into the neo4j interface and get results in tables.   
- Generally these queries are used when you want to return lists or get percentages for report things  
  
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

1. [Get a count of computers that do not have admins](#1-get-a-count-of-computers-that-do-not-have-admins)  
2. [Get the names of computers without admins, sorted in alphabetical order](#2-get-the-names-of-computers-without-admins-sorted-in-alphabetical-order)  
3. [Return a list of users who have admin rights on at least one system either explicitly or through group membership](#3-return-a-list-of-users-who-have-admin-rights-on-at-least-one-system-either-explicitly-or-through-group-membership)  
4. [Return username and number of computers that username is admin for, for top N users](#4-return-username-and-number-of-computers-that-username-is-admin-for-for-top-n-users)  
5. [Show all users that are administrator on more than one machine](#5-show-all-users-that-are-administrator-on-more-than-one-machine)  
6. [Show all users that are administrative on at least one machine, ranked by the number of machines they are admin on.](#6-show-all-users-that-are-administrative-on-at-least-one-machine-ranked-by-the-number-of-machines-they-are-admin-on)  
7. [This will return cross domain 'HasSession' relationships](#7-this-will-return-cross-domain-hassession-relationships)  
8. [Find all other Rights Domain Users shouldn't have](#8-find-all-other-rights-domain-users-shouldnt-have)  
9. [Show Kerberoastable high value targets](#9-show-kerberoastable-high-value-targets)  
10. [Show computers where Domain Admins are logged in](#10-show-computers-where-domain-admins-are-logged-in)  
11. [Show groups with most localAdmin](#11-show-groups-with-most-localadmin)  
12. [List of unique users with a path (no "GetChanges" path) to a Group tagged as "highvalue"](#12-list-of-unique-users-with-a-path-no-getchanges-path-to-a-group-tagged-as-highvalue)  
13. [Return all users which can rdp to any system, if they belong to adm or svr accounts](#13-return-all-users-which-can-rdp-to-any-system-if-they-belong-to-adm-or-svr-accounts)  
14. [Show the number of _users_ that have admin rights on each computer, in descending order](#14-show-the-number-of-users-that-have-admin-rights-on-each-computer-in-descending-order)  
15. [Stats percentage of enabled users that have a path to a high value group](#15-stats-percentage-of-enabled-users-that-have-a-path-to-a-high-value-group)  
16. [Shows machines that allow delegation that arent DCs](#16-shows-machines-that-allow-delegation-that-arent-dcs)  
17. [Groups with most local admin](#17-groups-with-most-local-admin)  
18. [Find principles with DCSync rights](#18-find-principles-with-dcsync-rights)  
19. [Find all Windows 7 computers](#19-find-all-windows-7-computers)  
20. [Find every user object where the "userpassword" attribute is populated. Neo4j web console only.](#20-find-every-user-object-where-the-userpassword-attribute-is-populated-neo4j-web-console-only)  
21. [Find every user that doesn't require kerberos pre-authentication. Neo4j web console only.](#21-find-every-user-that-doesnt-require-kerberos-pre-authentication-neo4j-web-console-only)  
22. [Return any group where the name of the group contains the string "SQL" followed by the string "ADMIN". This will match "SQL ADMINS" and will also match "SQL 2017 ADMINS".](#22-return-any-group-where-the-name-of-the-group-contains-the-string-sql-followed-by-the-string-admin-this-will-match-sql-admins-and-will-also-match-sql-2017-admins-neo4j-web-console-only)  
23. [Find every OU that contains the string "CITRIX".](#23-find-every-ou-that-contains-the-string-citrix)  
24. [Find all computers with sessions from users of a different domain (Looking for cross-domain compromise opportunities). Neo4j web console only.](#24-find-all-computers-with-sessions-from-users-of-a-different-domain-looking-for-cross-domain-compromise-opportunities-neo4j-web-console-only)  
25. [Find all users trusted to perform constrained delegation, return in order of the number of target computers. Neo4j web console only.](#25-find-all-users-trusted-to-perform-constrained-delegation-return-in-order-of-the-number-of-target-computers-neo4j-web-console-only)  
26. [Return each OU in the database in order of the number of computers in that OU. Neo4j web console only.](#26-return-each-ou-in-the-database-in-order-of-the-number-of-computers-in-that-ou-neo4j-web-console-only)  
27. [Return the name of every computer in the database where at least one SPN for the computer contains the string "MSSQL". Neo4j web console only.](#27-return-the-name-of-every-computer-in-the-database-where-at-least-one-spn-for-the-computer-contains-the-string-mssql-neo4j-web-console-only)  
28. [Find groups with both users and computers that belong to the group. Neo4j web console only.](#28-find-groups-with-both-users-and-computers-that-belong-to-the-group-neo4j-web-console-only)  
29. [Return each OU in the database that contains a Windows Server computer. Return rows where the columns are the name of the OU, the name of the computer, and the operating system](#29-return-each-ou-in-the-database-that-contains-a-windows-server-computer-return-rows-where-the-columns-are-the-name-of-the-ou-the-name-of-the-computer-and-the-operating-system-of-the-computer-neo4j-web-console-only)  
30. [Find any computer that is NOT a domain controller that is trusted to perform unconstrained delegation. Neo4j web console only.](#30-find-any-computer-that-is-not-a-domain-controller-that-is-trusted-to-perform-unconstrained-delegation-neo4j-web-console-only)  
31. [Find every instance of a computer account having local admin rights on other computers. Return in descending order of the number of computers the computer account has local ](#31-find-every-instance-of-a-computer-account-having-local-admin-rights-on-other-computers-return-in-descending-order-of-the-number-of-computers-the-computer-account-has-local-admin-rights-on)  
32. [Find users who are not marked as "Sensitive and Cannot Be Delegated" that have Administrative access to a computer, and where those users have sessions on servers with unconstrained delegation](#32-find-users-who-are-not-marked-as-sensitive-and-cannot-be-delegated-that-have-administrative-access-to-a-computer-and-where-those-users-have-sessions-on-servers-with-unconstrained-delegation-enabled)  
33. [Gather the computers where the user is AdminTo and the computers with unconstrained delegation in lists (for better presentation and structure)](#33-gather-the-computers-where-the-user-is-adminto-and-the-computers-with-unconstrained-delegation-in-lists-for-better-presentation-and-structure)  
34. [DOMAIN USERS is Admin of Computer](#34-domain-users-is-admin-of-computer)  
35. [Find Shortest Path from DOMAIN USERS to High Value Targets](#35-find-shortest-path-from-domain-users-to-high-value-targets)  
36. [Find ALL Path from DOMAIN USERS to High Value Targets](#36-find-all-path-from-domain-users-to-high-value-targets)  
37. [Find all other right Domain Users shouldn’t have](#37-find-all-other-right-domain-users-shouldnt-have)  
38. [Kerberoastable Accounts](#38-kerberoastable-accounts)  
39. [Kerberoastable Accounts member of High Value Group](#39-kerberoastable-accounts-member-of-high-value-group)  
40. [Find WORKSTATIONS where Domain Users can RDP To](#40-find-workstations-where-domain-users-can-rdp-to)  
41. [Find SERVERS where Domain Users can RDP To](#41-find-servers-where-domain-users-can-rdp-to)  
42. [Top 10 Computers with Most Admins](#42-top-10-computers-with-most-admins)  
43. [How many High Value Target a Node can reach](#43-how-many-high-value-target-a-node-can-reach)  
44. [All DA Account Sessions](#44-all-da-account-sessions)  
45. [DA Sessions to NON DC](#45-da-sessions-to-non-dc)  
46. [Count on how many non DC machines DA have sessions](#46-count-on-how-many-non-dc-machines-da-have-sessions)  
47. [EXCLUDE PATH (Edge) on the fly](#47-exclude-path-edge-on-the-fly)  
48. [Set SPN to a User](#48-set-spn-to-a-user)  
49. [Add DOMAIN USERS as Admin to COMPxxxx](#49-add-domain-users-as-admin-to-compxxxx)  
50. [Test our New Relationship](#50-test-our-new-relationship)  
51. [Delete a Relationship](#51-delete-a-relationship)  
52. [Shortest Path from Domain Users to Domain Admins](#52-shortest-path-from-domain-users-to-domain-admins)  
53. [Shortest Path from Domain Users to Domain Admins (No AdminTo)](#53-shortest-path-from-domain-users-to-domain-admins-no-adminto)  
54. [Find Admin Group not tagged highvalue](#54-find-admin-group-not-tagged-highvalue)  
55. [Set Admin Group as highvalue](#55-set-admin-group-as-highvalue)  
56. [Remore Admin User highvalue](#56-remore-admin-user-highvalue)  
57. [If you want to enumerate all available properties](#57-if-you-want-to-enumerate-all-available-properties)  
58. [List all computers with unconstraineddelegation](#58-list-all-computers-with-unconstraineddelegation)  
59. [Computer where the most users can RDP to ](#59-computer-where-the-most-users-can-rdp-to)  
60. [Average Length of Path](#60-average-length-of-path)  
61. [Count how many Users have a path to DA](#61-count-how-many-users-have-a-path-to-da)  
62. [Percent of Users that have a path to DA](#62-percent-of-users-that-have-a-path-to-da)  
63. [Return users with shortest paths to high value targets by name](#63-return-users-with-shortest-paths-to-high-value-targets-by-name)  
64. [Return labels i.e. group/name for a given SID](#64-return-labels-ie-groupusername-for-a-given-sid)
65. [Return top 10 users with most Derivative local admin rights](#65-return-top-10-users-with-most-derivative-local-admin-rights)
66. [Find all users WITHOUT a path to DA](#66-find-all-users-without-a-path-to-da)
67. [Find users that have never logged on AND their account is still active](#67-find-users-that-have-never-logged-on-and-their-account-is-still-active)
68. [Find users with DCSync rights who are not members of DA](#68-find-users-with-dcsync-rights-who-are-not-members-of-da) 
69. [Show attack paths from X domain to Specific Domain](#69-show-attack-paths-from-x-domain-to-specific-domain)  
70. [Find all computers that are NOT dc's](#70-find-all-computers-that-are-NOT-dcs)
  
-----------------------------------------------------------------------


### 1. Get a count of computers that do not have admins
```
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins
RETURN COUNT(c2)
```

### 2. Get the names of computers without admins, sorted in alphabetical order
```
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins
RETURN c2.name
ORDER BY c2.name ASC
```

### 3. Return a list of users who have admin rights on at least one system either explicitly or through group membership
```
MATCH (u:User)-[r:AdminTo|MemberOf*1..]->(c:Computer
RETURN u.name
```

### 4. Return username and number of computers that username is admin for, for top N users
```
MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name as n, COUNT(DISTINCT(C)) as c 
RETURN n,c
ORDER BY c DESC LIMIT 5
```

### 5. Show all users that are administrator on more than one machine
```
MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name as n, COUNT(DISTINCT(C)) as c 
WHERE c>1
RETURN n
ORDER BY c DESC
```

### 6. Show all users that are administrative on at least one machine, ranked by the number of machines they are admin on.
```
MATCH (u:User)
OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH u, COLLECT(c1) + COLLECT(c2) AS tempVar
UNWIND tempVar AS computers
RETURN u.name AS UserName,COUNT(DISTINCT(computers)) AS AdminRightCount
ORDER BY AdminRightCount DESC
```

### 7. This will return cross domain 'HasSession' relationships
```
MATCH p=((S:Computer)-[r:HasSession*1]->(T:User)) 
WHERE NOT S.domain = T.domain
RETURN p
```

### 8. Find all other Rights Domain Users shouldn't have
```
MATCH p=(m:Group)->[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) 
WHERE m.name STARTS WITH ‘DOMAIN USERS’ 
RETURN p
```

### 9. Show Kerberoastable high value targets
```
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
```


### 10. Show computers where Domain Admins are logged in
```
MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@EXAMPLE.COM"})
WITH n as DAaccount
MATCH (c:Computer)-[b:MemberOf]->(t:Group) WHERE NOT t.name = "DOMAIN CONTROLLERS@EXAMPLE.COM"
WITH c as NonDC
MATCH p = (NonDC)-[:HasSession]->(DAaccount)
```


### 11. Show groups with most localAdmin
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
or
```
MATCH (g:Group)
OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH g,COLLECT(c1) + COLLECT(c2) AS tempVar
UNWIND tempVar AS comps
RETURN g.name,COUNT(DISTINCT(comps)) AS compcount
ORDER BY compcount DESC
```

### 12. List of unique users with a path (no "GetChanges" path) to a Group tagged as "highvalue"
```
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name),u.enabled
order by u.name
```


### 13. Return all users which can rdp to any system, if they belong to adm or svr accounts
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

### 14. Show the number of _users_ that have admin rights on each computer, in descending order
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:AdminTo]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c)
WITH COLLECT(u1) + COLLECT(u2) AS tempVar,c
UNWIND tempVar AS admins
RETURN c.name AS computerName,COUNT(DISTINCT(admins)) AS adminCount
ORDER BY adminCount DESC
```

### 15. Stats percentage of enabled users that have a path to a high value group
```
MATCH (u:User {domain:'EXAMPLE.COM',enabled:True})
MATCH (g:Group {domain:'EXAMPLE.COM'})
WHERE g.highvalue = True
WITH g, COUNT(u) as userCount
MATCH p = shortestPath((u:User {domain:'EXAMPLE.COM',enabled:True})-[*1..]->(g))
RETURN toint(100.0 * COUNT(distinct u) / userCount)
```

### 16. Shows machines that allow delegation that arent DCs
```
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group)  
WHERE NOT t.name =~ "(?i)DOMAIN CONTROLLERS*."  
WITH c as NonDC  
MATCH (NonDC {unconstraineddelegation:true}) RETURN NonDC.name
```  

### 17. Groups with most local admin
```
MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g,COUNT(c) as expAdmin
RETURN g.name,expAdmin,expAdmin as totalAdmin
ORDER BY totalAdmin DESC
```


### 18. Find principles with DCSync rights
```
MATCH p=(n1)-[:MemberOf|GetChanges*1..]->(u:Domain {name: {result}}) WITH p,n1 
MATCH p2=(n1)-[:MemberOf|GetChangesAll*1..]->(u:Domain {name: {result}}) WITH p,p2 
MATCH p3=(n2)-[:MemberOf|GenericAll|AllExtendedRights*1..]->(u:Domain {name: {result}}) 
RETURN p,p2,p3
```


### 19. Find all Windows 7 computers
```
MATCH (c:Computer)
WHERE toUpper(c.operatingsystem) CONTAINS "Windows 7"
RETURN c
```


### 20. Find every user object where the "userpassword" attribute is populated. Neo4j web console only.
```
MATCH (u:User)
WHERE NOT u.userpassword IS null
RETURN u.name,u.userpassword
```


### 21. Find every user that doesn't require kerberos pre-authentication. Neo4j web console only.
```
MATCH (u:User {dontreqpreauth: true})
RETURN u.name
```


### 22. Return any group where the name of the group contains the string "SQL" followed by the string "ADMIN". This will match "SQL ADMINS" and will also match "SQL 2017 ADMINS". Neo4j web console only.
```
MATCH (g:Group)
WHERE g.name =~ '(?i).*SQL.*ADMIN.*'
RETURN g.name
```


### 23. Find every OU that contains the string "CITRIX".
```
MATCH (o:OU)
WHERE o.name =~ "(?i).*CITRIX.*"
RETURN o
```


### 24. Find all computers with sessions from users of a different domain (Looking for cross-domain compromise opportunities). Neo4j web console only.
```
MATCH (c:Computer)-[:HasSession]->(u:User {domain:'FOO.BAR.COM'})
WHERE NOT c.domain = u.domain
RETURN u.name,COUNT(c)
```


### 25. Find all users trusted to perform constrained delegation, return in order of the number of target computers. Neo4j web console only.
```
MATCH (u:User)-[:AllowedToDelegate]->(c:Computer)
RETURN u.name,COUNT(c)
ORDER BY COUNT(c) DESC
```


### 26. Return each OU in the database in order of the number of computers in that OU. Neo4j web console only.
```
MATCH (o:OU)-[:Contains]->(c:Computer)
RETURN o.name,o.guid,COUNT(c)
ORDER BY COUNT(c) DESC
```


### 27. Return the name of every computer in the database where at least one SPN for the computer contains the string "MSSQL". Neo4j web console only.
```
MATCH (c:Computer)
WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL")
RETURN c.name,c.serviceprincipalnames
ORDER BY c.name ASC
```


### 28. Find groups with both users and computers that belong to the group. Neo4j web console only.
```
MATCH (c:Computer)-[r:MemberOf*1..]->(groupsWithComps:Group)
WITH groupsWithComps
MATCH (u:User)-[r:MemberOf*1..]->(groupsWithComps)
RETURN DISTINCT(groupsWithComps) as groupsWithCompsAndUsers
```


### 29. Return each OU in the database that contains a Windows Server computer. Return rows where the columns are the name of the OU, the name of the computer, and the operating system of the computer. Neo4j web console only.
```
MATCH (o:OU)-[:Contains]->(c:Computer)
WHERE toUpper(o.name) STARTS WITH "WINDOWS SERVER"
RETURN o.name,c.name,c.operatingsystem
```


### 30. Find any computer that is NOT a domain controller that is trusted to perform unconstrained delegation. Neo4j web console only.
```
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH COLLECT(c1.name) AS domainControllers
MATCH (c2:Computer {unconstraineddelegation:true})
WHERE NOT c2.name IN domainControllers
RETURN c2.name,c2.operatingsystem
ORDER BY c2.name ASC
```


### 31. Find every instance of a computer account having local admin rights on other computers. Return in descending order of the number of computers the computer account has local admin rights on.
```
MATCH (c1:Computer)
OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer)
OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer)
WITH COLLECT(c2) + COLLECT(c3) AS tempVar,c1
UNWIND tempVar AS computers
RETURN c1.name,COUNT(DISTINCT(computers))
ORDER BY COUNT(DISTINCT(computers)) DESC
```


### 32. Find users who are not marked as "Sensitive and Cannot Be Delegated" that have Administrative access to a computer, and where those users have sessions on servers with Unconstrained Delegation enabled  
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

### 33. Gather the computers where the user is AdminTo and the computers with unconstrained delegation in lists (for better presentation and structure)
```
MATCH (u:User {sensitive:false})-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c1:Computer)
WITH u,c1
MATCH (c2:Computer {unconstraineddelegation:true})-[:HasSession]->(u)
RETURN u.name AS user,COLLECT(DISTINCT(c1.name)) AS AdminTo,COLLECT(DISTINCT(c2.name)) AS TicketLocation
ORDER BY user ASC
```

### 34. DOMAIN USERS is Admin of Computer 
```
MATCH p=(m:Group)-[r:AdminTo]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
```

### 35. Find Shortest Path from DOMAIN USERS to High Value Targets
```
MATCH (g:Group),(n {highvalue:true}),p=shortestPath((g)-[*1..]->(n)) 
WHERE g.name STARTS WITH 'DOMAIN USERS' 
RETURN p
```

### 36. Find ALL Path from DOMAIN USERS to High Value Targets
```
MATCH (g:Group) WHERE g.name STARTS WITH 'DOMAIN USERS'  MATCH (n {highvalue:true}),p=shortestPath((g)-[r*1..]->(n)) return p
```

### 37. Find all other right Domain Users shouldn’t have
```
MATCH p=(m:Group)-[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
```

### 38. Kerberoastable Accounts
```
MATCH (n:User)WHERE n.hasspn=true RETURN n
```

### 39. Kerberoastable Accounts member of High Value Group
```
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
```

### 40. Find WORKSTATIONS where Domain Users can RDP To
```
match p=(g:Group)-[:CanRDP]->(c:Computer) 
where g.name STARTS WITH 'DOMAIN USERS' AND NOT c.operatingsystem CONTAINS 'Server' 
return p
```

### 41. Find SERVERS where Domain Users can RDP To
```
match p=(g:Group)-[:CanRDP]->(c:Computer) 
where g.name STARTS WITH 'DOMAIN USERS' 
AND c.operatingsystem CONTAINS 'Server' 
return p
```

### 42. Top 10 Computers with Most Admins
```
MATCH (n:User),(m:Computer), (n)-[r:AdminTo]->(m) WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name='' WITH m, count(r) as rel_count order by rel_count desc LIMIT 10 MATCH p=(m)<-[r:AdminTo]-(n) RETURN p
```


### 43. How many High Value Target a Node can reach
```
MATCH p = shortestPath((n)-[*1..]->(m {highvalue:true}))
WHERE NOT n = m
RETURN DISTINCT(m.name),LABELS(m)[0],COUNT(DISTINCT(n))
ORDER BY COUNT(DISTINCT(n)) DESC
```
 
### 44. All DA Account Sessions
```
MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) 
MATCH p = (c:Computer)-[:HasSession]->(n) 
return p
```

### 45. DA Sessions to NON DC
```
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group) 
WHERE NOT t.name = "DOMAIN CONTROLLERS@TESTLAB.LOCAL"
WITH c as NonDC
MATCH p=(NonDC)-[:HasSession]->(n:User)-[:MemberOf]->
(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"})
RETURN DISTINCT (n.name) as Username, COUNT(DISTINCT(NonDC)) as Connexions
ORDER BY COUNT(DISTINCT(NonDC)) DESC
```

### 46. Count on how many non DC machines DA have sessions
```
MATCH (c:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH c.name as DomainControllers
MATCH p = (c2:Computer)-[:HasSession]->(u:User)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-512" AND NOT c2.name in DomainControllers
RETURN DISTINCT(u.name),COUNT(DISTINCT(c2))
ORDER BY COUNT(DISTINCT(c2)) DESC
```

### 47. EXCLUDE PATH (Edge) on the fly
```
MATCH (n:User),(m:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((n)-[r*1..]->(m)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
RETURN p
```


### 48. Set SPN to a User
```
MATCH (n:User {name:"JNOA00093@TESTLAB.LOCAL"}) SET n.hasspn=true
```


### 49. Add DOMAIN USERS as Admin to COMPxxxx
```
MERGE (n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}) WITH n MERGE (m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) WITH n,m MERGE (n)-[:AdminTo]->(m)
```

### 50. Test our New Relationship 
```
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
RETURN p
```
#### Might try to replace AdminTo by 1**

### 51. Delete a Relationship
```
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->
(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
DELETE r
```


### 52. Shortest Path from Domain Users to Domain Admins
```
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[r*1..]->(n)) 
return p
```


### 53. Shortest Path from Domain Users to Domain Admins (No AdminTo)
```
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[r*1..]->(n)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
return p
```


### 54. Find Admin Group not tagged highvalue
```
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
RETURN g.name
```

### 55. Set Admin Group as highvalue
```
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
SET g.highvalue=true
```

### 56. Remore Admin User highvalue
```
MATCH p=(g:Group {highvalue: true})<-[:MemberOf]-(u:User)
WHERE g.name CONTAINS "ADMIN"
SET g.highvalue = NULL
```

### 57. If you want to enumerate all available properties
``` 
Match (n:Computer) return properties(n)
```

### 58. List all computers with unconstraineddelegation
```
MATCH (c:Computer {unconstraineddelegation:true})
return c.name
```

### 59. Computer where the most users can RDP to 
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name,COUNT(DISTINCT(users))
ORDER BY COUNT(DISTINCT(users)) DESC
```

### 60. Average Length of Path
```
MATCH (g:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'})
MATCH p = shortestPath((u:User)-[r:AdminTo|MemberOf|HasSession*1..]->(g))
WITH EXTRACT(n in NODES(p) | LABELS(n)[0]) as pathNodes
WITH FILTER(x IN pathNodes WHERE x = "Computer") as filteredPathNodes
RETURN AVG(LENGTH(filteredPathNodes))
```

### 61. Count how many Users have a path to DA
```
MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
RETURN COUNT (DISTINCT(u))
```

### 62. Percent of Users that have a path to DA
```
OPTIONAL MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
OPTIONAL MATCH (uT:User)
WITH COUNT (DISTINCT(uT)) as uTotal, COUNT (DISTINCT(u)) as uHasPath
RETURN uHasPath / uTotal * 100 as Percent
```

### 63. Return users with shortest paths to high value targets by name
```
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name),u.enabled
order by u.name
```

### 64. Return labels i.e. group,username for a given SID
```
MATCH (n {objectsid:"S-1-5-21-971234526-3761234589-3049876599-500"}) RETURN labels(n)
```

### 65. Return top 10 users with most Derivative local admin rights
```
MATCH (u:User)
OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH COLLECT(c1) + COLLECT(c2) as tempVar,u
UNWIND tempVar AS computers
RETURN u.name,COUNT(DISTINCT(computers)) AS is_admin_on_this_many_boxes
ORDER BY is_admin_on_this_many_boxes DESC
```


### 66. Find all users WITHOUT a path to da  
```
MATCH (n:User),(m:Group {name:"DOMAIN ADMINS@CONTOSO.LOCAL"}) WHERE NOT EXISTS ((n)-[*]->(m)) RETURN n.name  
```
  
  
### 67. Find users that have never logged on AND their account is still active  
```
MATCH (n:User) WHERE n.lastlogontimestamp=-1.0 AND n.enabled=TRUE RETURN n.name ORDER BY n.name  
```
  
### 68. Find users with DCSync rights who are not members of DA
```
MATCH (n1)-[:MemberOf|GetChanges*1..]->(u:Domain {name: "TESTLAB.LOCAL"}) WITH n1,u 
MATCH (n1)-[:MemberOf|GetChangesAll*1..]->(u) WITH n1,u 
MATCH p = (n1)-[:MemberOf|GetChanges|GetChangesAll*1..]->(u) WHERE NOT (n1)-[:MemberOf*1..]->(:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) 
RETURN p
```

### 69. Show attack paths from X domain to Specific Domain
```
MATCH p=(n)-[r]->(m) WHERE NOT n.domain = m.domain RETURN p  
```
or specific domains  
```
MATCH p=(n {domain:"lab.local")-[r]->(m {domain:"test.local"}) RETURN p
```

### 70. Find all computers that are NOT dc's
```
MATCH (dc:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.name STARTS WITH('DOMAIN CONTROLLERS')
WITH COLLECT(dc) as dcs
MATCH (c:Computer) WHERE NOT c in dcs
RETURN COUNT(c)
```
or using objectID  
```
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectid ENDS WITH "-516"
WITH COLLECT(c1) as dcs
MATCH (c:Computer) WHERE NOT c in dcs
RETURN COUNT(c)
```

  
## References
Reading materials / References::  
https://github.com/BloodHoundAD/BloodHound/wiki  
https://github.com/Scoubi/BloodhoundAD-Queries  
https://github.com/porterhau5/BloodHound-Owned  
https://porterhau5.com/blog/extending-bloodhound-track-and-visualize-your-compromise/  
https://blog.cptjesus.com/posts/introtocypher#building-on-top   
  
