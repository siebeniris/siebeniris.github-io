# Conflict Event Knowledge Graph 

## RDF/S Model
### Overall Schema

![](images/ConflictEventKG.jpg)

:computer: [Codes](https://github.com/siebeniris/ConflictEventKG)

 📂 [Data](https://shrtm.nu/6ybL)

:clipboard:  [Documentation](images/documentation.html)
## Analysis

* Correlation Analysis of the number of detected entities and the number of detected events
 
![](images/results_regression.png)

![](images/plot_regression.png)

* Distribution of the event detection confidence score
![](images/distplot.png)


## Sparql Queries


Prefixes
```
prefix dc: <http://purl.org/dc/elements/1.1/> 
prefix nee: <http://www.ics.forth.gr/isl/oae/core#> 
prefix owl: <http://www.w3.org/2002/07/owl#> 
prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> 
prefix rdf: <https://www.w3.org/1999/02/22-rdf-syntax-ns#> 
prefix cekg: <https://siebeniris.github.io/ConflictEventKG/>
prefix sem: <http://semanticweb.cs.vu.nl/2009/11/sem/> 
prefix xml: <http://www.w3.org/XML/1998/namespace> 
prefix xsd: <http://www.w3.org/2001/XMLSchema#>
prefix sioc: <http://rdfs.org/sioc/ns#>
prefix time: <http://www.w3.org/2006/time#>
prefix schema: <http://schema.org/>
prefix sioc_t: <http://rdfs.org/sioc/types#>
prefix dcterms: <http://purl.org/dc/terms/>
prefix cidoc-crm: <https://cidoc-crm.org/html/cidoc_crm_v7.1.1.html#>
prefix eventKG-s: <http://eventKG.l3s.uni-hannover.de/schema/> 
```


* The following query shows the events with the highest popularity and with role and actors of the events.

```sparql
select  ?date  ?EventDescription ?RoleType ?Actor (count(?tweet) as ?NumOfTweets) 
where {
?tweet cekg:detected ?event.
?event dcterms:description ?EventDescription;
            sem:hasBeginTimeStamp ?date.
?relation rdf:object ?event; sem:roleType ?roletype; rdf:subject ?actor.
?actor rdfs:label ?Actor.
?roletype rdfs:label ?RoleType.

} 
GROUP BY ?date ?EventDescription ?RoleType ?Actor
ORDER BY DESC(?NumOfTweets)
LIMIT 10
```

**Result**

![](images/events_actors_roles_nrOfTweets.png)
