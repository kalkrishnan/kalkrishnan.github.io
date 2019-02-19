---
layout: post
title: "Calling Stored procedures with JPA"
description: ""
category: "Java"
---

A lot of times we need to load entities into memory which contain a lot of nested relationships. Most of the time these nested relationships are lazy loaded which causes multiple queries being fired and causes performance issues. One solution to this to load the entity using fetches in hql but this can lead to another serious issue: [cartesian product problem](http://learningviacode.blogspot.com/2012/08/fetch-join-and-cartesian-product-problem.html).

A better way to solve this is to leave the heavy lifting to the DB using a stored procedure and call the stored procedure from JPA as below:


1.  Annotate Entity with @NamedStoredProcedureQuery:

```java
@NamedStoredProcedureQuery(
		name = "LOAD_CLAIM", 
		procedureName = "EDPS.SPEDPS_LOAD_CLAIM", 
		resultClasses = { EdpsClaimInformation.class, EdpsSubscriberHierarchicalLevel.class, EdpsBillingProviderHierarchicalLevel.class, EdpsOtherSubscriberInformation.class, EdpsFileHeader.class },
		parameters = {
		@StoredProcedureParameter(name = "claimInformationId", mode = ParameterMode.IN, type = Integer.class )
		}
)
```
The resultClasses are important to help Hibernate understand how to parse the results returned into the classes mentioned.

2. Call from DAO using entity Manager:

```java
StoredProcedureQuery loadClaimSP = this.getEntityManager().createNamedStoredProcedureQuery("LOAD_CLAIM");
		loadClaimSP.setParameter("claimInformationId",claimInformationId);
		List<Object> results = loadClaimSP.getResultList();
```

3. Iterate through the results to get the object needed. If the stored procedure returns multiple sql results, hibernate maps it into multiple entity objects(one instance, multiple references). The API does not provide a way to restrict this as it does with hql queries.

```java

	Object[] resultArray = (Object[]) results.get(0); //get the first result from the multiple duplicate results
			EdpsClaimInformation claimInformation = (EdpsClaimInformation) resultArray[0] ;// get the main entity, since the result will contain an array of all the entities which are part of the result classes. Only references, not multiple instances.
			return claimInformation;
```