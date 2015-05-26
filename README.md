# Welcome to RaQueL

<p>RaQueL is a query language, through
which the robots use RoboBrain for various robotic applica-
tions. The RQL provides a rich set of retrieval functions and
programming constructs to perform complex traversals on the
RoboBrain graph.</p>

## Commands

* `fetch(Pattern)` - For querying a user specified pattern in RoboBrain graph.
* `SortBy(list, property)` - To sort the given list of nodes or relationships according to a given property(currently supported on for 'belief' property).
* `map` - Evaluates a lazy map on a list of nodes or relationships specified.
* `filter` - Evaluates a lazy map on a list of nodes or relationships specified.
* `user_defined_functions =lambda n: funcName(n)` - RaQueL supports user defined functions.
```python 
#this sample code defines a function to get affordances of objects in the list n. It can be called as affordances('knife')
affordances=lambda n:fetch("({handle :'\" + n + \"'})-[:`HAS_AFFORDANCE`]->(v)")
```


## Sample program 
* This program finds path which are 1 to 3 edges away from standing_human node to the phone node and then ranks these paths in ascending order by their beliefs
```python
paths =fetch("({handle:'standing_human'})-[e*1..3]->({handle:'phone'})")
SortBy(paths,'Belief')
```

### Example 1
* This RaQueL query returns all the objects which a 'standing human' can use.
```python
fetch("({handle:'standing_human'})-[:`CAN_USE`]->(v)")
```
* This RaQueL query returns all the materials which a 'wall' has.
```python
fetch("({handle:'wall'})-[:`HAS_MATERIAL`]->(v)")
```
### Example 2
* This program finds path which are 1 to 5 edges away from standing_human node to the phone node and then ranks these paths in ascending order by their beliefs.
```python
paths =fetch("({handle:'standing_human'})-[e*1..5]->({handle:'phone'})")
SortBy(paths,'Belief')
```
### Example 3
* This program finds objects which a 'sitting human' can use. Then maps them to their affordances using lazy evaluations. This program iterates overs 2 elements in the objects list.
```python
global objects
objects =fetch("({handle:'sitting_human'})-[:`CAN_USE`]->(V)")
affordances=lambda n:fetch("({handle :'\" + n + \"'})-[:`HAS_AFFORDANCE`]->(v)")
iter = imap( lambda u: (u) ,objects[1])
print iter.next()
print iter.next()
```
### Example 4
* This program finds a unique media represenation of 'tv'. 
```python
global entities, media
entities = lambda n: fetch("(v)-[:`HAS_MEDIA`]->({handle :'" + n + "'})")
media = lambda n:fetch("({handle :'" + n + "'})-[:`HAS_MEDIA`]->(v)")
ind_media = lambda a: ifilter(lambda u: len(entities(u)[1])==1,(media(a))[1])
iter1= ind_media('tv')	
print iter1.next()
```

