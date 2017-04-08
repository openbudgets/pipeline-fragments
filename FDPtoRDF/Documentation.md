# FDP2RDF Developer's Documentation

## Overview of mappings from the FDP specification
Below, the FDP-to-RDF mappings implemented in the pipeline are shown on an example from http://fiscal.dataprotocols.org/spec/ as comments to parts that are mapped by the pipeline. More clarification can be brought by the examples further below. Only mapped parts of FDP specification are displayed - please compare it with the original specification to see what is not mapped.

```javascript
  "name": "Australia2014",
// -> used in URI of qb:DataSet

  "title": "Australian annual budget 2013-14",
// -> qb:DataSet dc:title

  "license": "cc-by 3.0",
// -> qb:DataSet dc-terms:license

  "countryCode": "au"
// -> qb:DataSet obeu-dimension:organizaton <cl-geo codelist URI like http://data.openbudgets.eu/resource/codelist/cl-geo/CZ>

  "profiles": {
    "fiscal": "*",
    "tabular": "*"
  },
// unused

  "author": "[?author]"
// -> dcat:contactPoint http://data.openbudgets.eu/resource/agent/[urlencoded([?author])] ;
//    dcterms:publisher http://data.openbudgets.eu/resource/agent/[urlencoded([?author])] .
//    http://data.openbudgets.eu/resource/agent/[urlencoded([?author])] foaf:name "[?author]";
//      vcard:fn "[?author]" .

  "description": "[?description]"
// -> dcterms:description "[?description]" 

  "granularity": "aggregated", 
// unused
  
  "fiscalPeriod": {
    "start": "1982-04-22",
    "end": "1983-04-21"
  },
// -> qb:DataSet obeu-dimension:fiscalPeriod
  
  "resources": [ /* ... */ ],
// used to dereference and download the CSV files

  "model": {

    "measures": {
    },
// -> subProperty of obeu-measure:amount; when multiple measures, multiple measure properties used for each qb:Observation

    "dimensions": {
    }
// -> mapped to OBEU dimension and attribute properties, see below
  }
```

```javascript
"measures": {
  "measure-name": {
// -> measure-name used in the QB measure property URI, further referenced as [qb:MeasureProperty]
    "source": "amount",
    
    "currency": "USD",
// -> [qb:MeasureProperty] obeu-attribute:currency <OKFGR code list value (https://github.com/openbudgets/Code-lists/tree/master/UnifiedViews/skosified/currencies)>
    
    "factor": 1,
// Used to multiply measure values when transformed
    
    "resource": "budget-2014-au",
// used to find measure values
    
    "direction": "expenditure",
// -> [qb:MeasureProperty] <http://schemas.frictionlessdata.io/fiscal-data-package#operationCharacter> obeu-operation:expenditure or obeu-operation:revenue
    
    "phase": "proposed",
// -> [qb:MeasureProperty] <http://schemas.frictionlessdata.io/fiscal-data-package#budgetPhase> obeu-budgetphase:draft, approved, revised, executed
    
    // OPTIONAL: Other properties allowed.
    // other properties are not mapped
  }
  //...
}
"dimensions": {

    "dimensionType": "datetime",
// -> obeu-dimension:date - requires xs:dateTime or date (xs:date yyyy-mm-dd) only, month only (yyyy-mm) and year only (yyyy) values on input, outputs <http://reference.data.gov.uk/id/gregorian-...> IRIs

    "dimensionType": "entity",
// -> subProperty of obeu-dimension:organization, schema:Organization values

    "dimensionType": "classification",
// -> subProperty of obeu-dimension:classification, skos values (if classificationType not provided)

    "dimensionType": "activity",
// -> subProperty of obeu-dimension:programmeClassification, skos values

    "dimensionType": "fact",
// -> subProperty of obeu:OptionalProperty, literal values

    "dimensionType": "location",
// -> subProperty of obeu-attribute:location, schema:Location values

    "dimensionType": "classification",
    "classificationType": "administrative",
// -> subProperty of obeu-dimension:administrativeClassification, skos values

    "dimensionType": "classification",
    "classificationType": "functional",
// -> subProperty of obeu-dimension:functionalClassification, skos values

    "dimensionType": "classification",
    "classificationType": "economic",
// -> subProperty of obeu-dimension:economicClassification, skos values

    "dimensionType": "other",
    // or dimensionType not defined
// -> subProperty of general qb:DimensionProperty, literal values
  }
```
## Example transformations of examples from FDP specification
Result of transforming http://fiscal.dataprotocols.org/examples/minimal/ with datapackage.json edited to .jsonld form (see https://github.com/openbudgets/pipeline-fragments/tree/master/FDPtoRDF/test/test4)
```
_:node1ao7d2qkrx1 qb:attribute obeu-attribute:currency ;
	qb:componentAttachment qb:MeasureProperty .

_:node1ao7d2qkrx2 qb:componentAttachment qb:DataSet ;
	qb:dimension obeu-dimension:fiscalPeriod .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> a qb:DataStructureDefinition ;
	qb:component _:node1ao7d2qkrx1 , _:node1ao7d2qkrx2 , _:node1ao7d2qkrx3 , _:node1ao7d2qkrx4 .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> a qb:DataSet ;
	<http://purl.org/dc/elements/1.1/title> "My OpenSpending Data Package" ;
	qb:structure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> ;
	qb:observation <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/11af4c37-162a-4840-b9bb-f20e95174421> .

_:node1ao7d2qkrx3 qb:measure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> .
<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> obeu-attribute:currency <http://data.openbudgets.eu/codelist/currency/USD> ;
	a rdf:Property , qb:MeasureProperty ;
	rdfs:subPropertyOf obeu-measure:amount .

_:node1ao7d2qkrx4 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> .
<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> a rdf:Property , qb:DimensionProperty .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/11af4c37-162a-4840-b9bb-f20e95174421> a qb:Observation ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> 10000.0 ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> "01/01/2015" ;
	qb:dataSet <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> .
```
Result of transforming http://fiscal.dataprotocols.org/examples/labels-and-hierarchies/ with datapackage.json changed to .jsonld (see https://github.com/openbudgets/pipeline-fragments/tree/master/FDPtoRDF/test/test5)
```
<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> a qb:DataStructureDefinition ;
	qb:component _:node1aogj1q3vx1 , _:node1aogj1q3vx2 , _:node1aogj1q3vx3 , _:node1aogj1q3vx4 , _:node1aogj1q3vx5 .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> a qb:DataSet ;
	<http://purl.org/dc/elements/1.1/title> "My OpenSpending Data Package" ;
	qb:structure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> ;
	qb:observation <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/b592c8bb-0191-477b-83e1-1a992ccfc33e> .

_:node1aogj1q3vx3 qb:measure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> obeu-attribute:currency <http://data.openbudgets.eu/codelist/currency/USD> ;
	a rdf:Property , qb:MeasureProperty ;
	rdfs:subPropertyOf obeu-measure:amount .

_:node1aogj1q3vx4 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> a rdf:Property , qb:DimensionProperty .

_:node1aogj1q3vx5 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/classification> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/classification> a rdf:Property , qb:DimensionProperty .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/level1_id/09> a skos:Concept ;
	skos:inScheme <http://data.openbudgets.eu/resource/my-openspending-datapackage/codelist/classification/> ;
	skos:prefLabel "Education" ;
	skos:notation "09" .

<http://data.openbudgets.eu/resource/my-openspending-datapackage/codelist/classification/> a skos:ConceptScheme .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/level2_id/0901> a skos:Concept ;
	skos:inScheme <http://data.openbudgets.eu/resource/my-openspending-datapackage/codelist/classification/> ;
	skos:prefLabel "Higher Education" ;
	skos:broader <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/level1_id/09> ;
	skos:notation "0901" .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/b592c8bb-0191-477b-83e1-1a992ccfc33e> a qb:Observation ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> 10000.0 ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> "01/01/2015" ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/classification> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/level2_id/0901> ;
	qb:dataSet <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> .
```
Result of transforming http://fiscal.dataprotocols.org/examples/entities-denormalized/ with an appropriate .jsonld (see https://github.com/openbudgets/pipeline-fragments/tree/master/FDPtoRDF/test/test6). 
Notice that the date values are not transformed. That is because the dimension is annotated as "dateTime", but the values are not xs:date or xs:dateTime compliant.
```
<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> a qb:DataStructureDefinition ;
	qb:component _:node1aogk0o33x1 , _:node1aogk0o33x2 , _:node1aogk0o33x3 , _:node1aogk0o33x4 , _:node1aogk0o33x5 , _:node1aogk0o33x6 .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> a qb:DataSet ;
	<http://purl.org/dc/elements/1.1/title> "My OpenSpending Data Package" ;
	qb:structure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> ;
	qb:observation <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/83ccf098-390e-4aaa-b299-c06b49d7f514> , <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/9a373ce3-7d24-4c46-8557-cca4d5b5076a> .

_:node1aogk0o33x3 qb:measure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> obeu-attribute:currency <http://data.openbudgets.eu/codelist/currency/USD> ;
	a rdf:Property , qb:MeasureProperty ;
	rdfs:subPropertyOf obeu-measure:amount .

_:node1aogk0o33x4 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payor> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payor> a rdf:Property , qb:DimensionProperty ;
	rdfs:subPropertyOf obeu-dimension:organization .

_:node1aogk0o33x5 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> a rdf:Property , qb:DimensionProperty ;
	rdfs:subPropertyOf obeu-dimension:organization .

_:node1aogk0o33x6 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> a rdf:Property , qb:DimensionProperty ;
	rdfs:subPropertyOf obeu-dimension:date .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/1> a <http://schema.org/Organization> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/id> "1" ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/title> "Acme 1" .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payor/2> a <http://schema.org/Organization> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/id> "2" ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/title> "Acme 2" .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/83ccf098-390e-4aaa-b299-c06b49d7f514> a qb:Observation ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> 10000.0 ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payor> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payor/2> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/1> ;
	qb:dataSet <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/9a373ce3-7d24-4c46-8557-cca4d5b5076a> a qb:Observation ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> 20000.0 ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payor> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payor/2> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/1> ;
	qb:dataSet <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> .
```
Result of transforming https://github.com/openbudgets/pipeline-fragments/blob/master/FDPtoRDF/test/test7/datapackage.jsonld which was created from http://fiscal.dataprotocols.org/examples/entities-normalized/ by fixing column naming bugs and changing date values to xs:date compliant.
```
_:node1aoh2a4bkx1 qb:attribute obeu-attribute:currency ;
	qb:componentAttachment qb:MeasureProperty .

_:node1aoh2a4bkx2 qb:componentAttachment qb:DataSet ;
	qb:dimension obeu-dimension:fiscalPeriod .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> a qb:DataStructureDefinition ;
	qb:component _:node1aoh2a4bkx1 , _:node1aoh2a4bkx2 , _:node1aoh2a4bkx3 , _:node1aoh2a4bkx5 , _:node1aoh2a4bkx6 .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> a qb:DataSet ;
	<http://purl.org/dc/elements/1.1/title> "My OpenSpending Data Package" ;
	qb:structure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage> ;
	qb:observation <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/e0c0bb29-e21e-43c2-8332-ec24b33cd96a> , <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/72bc8f1b-5876-430f-b3f0-0e52d3e3d7ad> .

_:node1aoh2a4bkx3 qb:measure <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> obeu-attribute:currency <http://data.openbudgets.eu/codelist/currency/USD> ;
	a rdf:Property , qb:MeasureProperty ;
	rdfs:subPropertyOf obeu-measure:amount .

_:node1anvnho20x19143 <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/1> .

_:node1anvnho20x19144 <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/2> .

_:node1aoh2a4bkx5 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> a rdf:Property , qb:DimensionProperty ;
	rdfs:subPropertyOf obeu-dimension:organization .

_:node1aoh2a4bkx6 qb:dimension <http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> .

<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> a rdf:Property , qb:DimensionProperty ;
	rdfs:subPropertyOf obeu-dimension:date .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/1> a <http://schema.org/Organization> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/description> "They are the first acme company" ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/id> "1" ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/title> "Acme 1" .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/2> a <http://schema.org/Organization> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/description> "They are the sceond acme company" ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/id> "2" ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/partialproperty/title> "Acme 2" .

<http://reference.data.gov.uk/id/gregorian-day/2015-01-01> a time:Interval .

<http://reference.data.gov.uk/id/gregorian-day/2015-01-02> a time:Interval .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/e0c0bb29-e21e-43c2-8332-ec24b33cd96a> a qb:Observation ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> 10000.0 ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/1> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> <http://reference.data.gov.uk/id/gregorian-day/2015-01-01> ;
	qb:dataSet <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> .

<http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/observation/72bc8f1b-5876-430f-b3f0-0e52d3e3d7ad> a qb:Observation ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/measure/amount> 20000.0 ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/payee> <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage/payee/1> ;
	<http://data.openbudgets.eu/ontology/dsd/my-openspending-datapackage/dimension/date> <http://reference.data.gov.uk/id/gregorian-day/2015-01-02> ;
	qb:dataSet <http://data.openbudgets.eu/resource/dataset/my-openspending-datapackage> .
```

