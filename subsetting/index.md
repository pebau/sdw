Persistent Identifiers for Subsets of Data
==========================================
Datasets can be extremely large. Examples include meteorological records spanning decades and covering large geographical areas, satellite images and image timeseries, etc. Such datasets are very rarely processed ''en masse'', rather, an application will typically require a specific subset of the dataset, such as the temperature records for a particular location for the previous 24 hours, 1-year precipitation average for each location in some country, extraction of specific bands ("channels", "variables") from hyperspectral satellite images and multi-variable meteorological simulation output.

These subsets are typically accessed through an API that accepts HTTP GET requests and so the API call, or query, can be encoded directly in the URL thus:

`http://example.com/api/?sql= Blah blah - CGI-encoded SQL query`
`http:://acme.com/ows?SERVICE=WCS&VERSION=2.0&REQUEST=GetCoverage&COVERAGEID=myLilCov&SUBSET=Lat(10:20)&SUBSET=Long(30:40)&SUBSET=time('2015-12-31')`
`http:://acme.com/ows?SERVICE=WCS&VERSION=2.0&REQUEST=GetCoverage&COVERAGEID=myLilCovRANGESUBSET=red, green, blue`
`http:://acme.com/ows?SERVICE=WCPS&QUERY= for $i in (SentinelImage), $d in (DEM) return encode( case when $d > 0 then $i.red else 0 end, "image/tiff")`

In this sense, an API call can be treated like an identifier for a subset of a dataset. The problem is that the identifier is dependent on a specific implementation of a specific technology. If the API is updated, perhaps trivially as a new parameter is added, or substantially to move from an XML message-based to a more RESTful based system, the identifier is likely to no longer apply. Since it is expensive to maintain old systems, or to includes backwards compatibility in new systems, sooner or later, old APIs are likely to be turned off.

Therefore, a URI that includes an API call or query directly should be treated as emphemeral.

[PB: My recommendation: "Therefore, API calls or queries contained in a URI should strictly use open standards."]

To be persistent, a URI must be technology-neutral. An intermediate layer should be used to translate a URI into a query.

[PB: don't agree. This just camouflages the problem, and actually is not really technology-neutral either.]

The [Linked Data API](https://github.com/UKGovLD/linked-data-api "The Linked Data API") is a well documented and widely implemented method of doing this. It takes a simple URL as input and interprets that as a SPARQL query, returning the output in any number of formats through content negotiation. The aim here is to provide a non-SPARQL interface to Linked Data resources, but the effect is to create persistent URIs for subsets of large datasets. The imntermediate layer could be replaced with en entirely different technology and still return the same data. In a different interface, SPARQL  as the interface might be replaced with SQL, XQuery,  OGC WCPS, or similar, as long as it is an accepted, stable standard.

[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1 "OpenSearch") provides something similar, in that a simple URL is converted into an XML message that is posted to a search engine. A future version of OpenSearch could use an entirely different technology whilst the template URL remained unchanged. The OpenSearch URL makes use of the query string portion  (`http://search.example.com/?q=search%20term` for example) however, this is not interpreted directly and is not technology dependent. OGC offers a set of [geo-temporal extensions](http://www.opengis.net/doc/IS/opensearchgeo/1.0 "OpenSearch Geo and Time Extensions") to OpenSearch and these can potentially be considered as persistent identifiers, however, as the query string becomes longer and longer, the more the URL is tied to an existing data structure and implementation.

Notably, these two examples show how different interfaces serve different purposes and accomplish that with different interfaces: SPARQL is a poerful experts' language relying on ontologies whereas OpenSearch is a simple, keyword-based retrieval language specialized on metadata. As of today, there is no single accepted paradigm in science and technology, rather common sense is that different models coexist for the different purposes.

Persistent Structures
---------------------

Something about intended outcomes.

* `http://weather.example.com/temperature/uk/london` for the current temperature in London
* `http://weather.example.com/temperature/uk/london/2015-12-30/` for temperature readings in London for a specific day
* `http://weather.example.com/temperature/uk/london/2015-12-30/12:00/` for the temperature reading in London for a specific day at a specifc time

Implementation options
----------------------

Specifically, for geospatial (and temporal) data there are at least the following options:
* Mod Rewrite examples
* ISO SQL, with support for Simple Features (existing) and n-D arrays (in adoption process)
* OGC Web Coverage Service (WCS), allowing spatio-temporal subsetting on regular and irregular grids as well as point clouds
* OGC Web Coverage Processing Service (WCPS), allowing spatio-temporal subsetting, aggregation, fusion, and general processing on regular and irregular grids
* 
