# FDPtoRDF

**URL:** <https://github.com/openbudgets/pipeline-fragments/raw/master/FDPtoRDF/FDPtoRDF.jsonld>

The **FDPtoRDF.jsonld** file is a [LinkedPipes] pipeline fragment. It takes a [Fiscal Data Package] .jsonld descriptor as input, transforms the FDP into RDF OBEU representation and stores the result into a predefined triple-store.

## Prerequisities
The pipeline uses [LinkedPipes component FDPtoRDF](https://github.com/opendatacz/lp-etl-components/tree/master/t-fdpToRdf), which is not part of LinkedPipes by default. You can deploy it into existing LinkedPipes installation by [downloading it](https://github.com/opendatacz/lp-etl-components/raw/master/t-fdpToRdf/deploy/t-fdpToRdf.zip) and extracting into `<Linked-pipes-directory>/deploy/jars/`. Restart LinkedPipes to load the component. The component needs LP-ETL version of commit [25e0bdd](https://github.com/linkedpipes/etl/commit/5b42e41a8986e93d092c7fff1e7d42c044a0873e) or later.

## Installation

1. **Import** [the pipeline](https://raw.githubusercontent.com/openbudgets/pipeline-fragments/master/FDPtoRDF/FDPtoRDF.jsonld) into LinkedPipes using the button in the bottom-right corner of the LinkedPipes UI. You can reference to [pipeline's GitHub URL](https://raw.githubusercontent.com/openbudgets/pipeline-fragments/master/FDPtoRDF/FDPtoRDF.jsonld) directly.
2. Open the pipeline in LinkedPipes and **configure the Graph Store Protocol component** (in the bottom-right corner of the pipeline, the node is highlighted by red). Follow the [component's configuration documentation](http://etl.linkedpipes.com/components/l-graphstoreprotocol) if needed and change the following to appropriate values of the triple-store where you want to store the results:
    - Repository type (Fuseki/Virtuoso/etc.)
    - Graph Store protocol endpoint
    - User name and password
3. Configure the **Files to SCP component** (also highlighted by red, just above the Graph Store Protocol component). Follow the [component's configuration documentation](http://etl.linkedpipes.com/components/l-filestoscp) if needed. The component can be disabled if not needed: by selecting the component node and clicking on the upper right button above it. This applies to the Graph Store Protocol as well.
4. Don't forget to click the **"apply changes"** and then **"save"** the whole pipeline (bottom-left button in the pipeline editor).

## Usage

The pipeline is executed through a HTTP POST request to its URL. The POST has to include either the FDP .jsonld descriptor or a .jsonld file with a URL of the .jsonld descriptor. The resulting RDF is stored into a triple-store as configured in the pipeline. The URL for pipeline execution looks like this:
    
    http://[your LinkedPipes server]/resources/executions?pipeline=http://[your LinkedPipes server]/resources/pipelines/created-[pipeline timestamp]
    
- **[your LinkedPipes server]** is the hostname and port where LinkedPipes are installed.
- **[pipeline timestamp]** can be found at the end of the pipeline URL when opened in the LinkedPipes editor.

For example, a pipeline which can be edited at the URL

    http://obeu.vse.cz:9080/#/pipelines/edit/canvas?pipeline=http:%2F%2Fobeu.vse.cz:9080%2Fresources%2Fpipelines%2Fcreated-1466690747879

can be executed through the following command using CURL:
```sh    
    curl -i -X POST -H "Content-Type: multipart/form-data" -F "input=@datapackage.jsonld" http://obeu.vse.cz:9080/resources/executions?pipeline=http://obeu.vse.cz:9080/resources/pipelines/created-1466690747879
```
The details about the execution can then be seen in the Executions view of LinkedPipes UI.

### Input
The pipeline uses a .jsonld FDP descriptor as the main input. It can be either POSTed to it directly, or in the form of a .jsonld file containing its URL.

**POSTing FDP descriptor directly:**
The first option of input of the pipeline is a .jsonld FDP descriptor file sent in the POST request (datapackage.jsonld in the CURL example above). The file must 
- be named 'datapackage.jsonld'
- correspond to the [FDP descriptor specification](http://fiscal.dataprotocols.org/spec/#descriptor--datapackagejson) and in addition to it:
- have the .jsonld extension
- all references to CSV data files (resources' paths) in it must be dereferencable URLs
- contain the following property in the root object:
```
    "@context": “http://schemas.frictionlessdata.io/fiscal-data-package.jsonld”,
```
*See [examples folder](documentation/examples) for example .jsonld descriptors.*

**POSTing package-url.jsonld with a URL of the FDP descriptor:** The second option is to POST a file named *'package-url.jsonld'* having the URL of the FDP descriptor as a value of the schema:url property. See the example below:
```json
{
  "@context": {
    "@vocab": "http://schema.org/"
  },
  "url": "http://protegeserver.cz/files/fdp-example-normalized/datapackage.jsonld"
}
```
The FDP descriptor file must fulfill the abovementioned requirements, except that 
- it can have arbitrary name
- references to CSV data files (resources' paths) in it can be either **absolute URLs** or **URLs relative** to the URL of the FDP descriptor ('http://protegeserver.cz/files/fdp-example-normalized/' in the example above)

See a [sample package-url.jsonld](samples/1/package-url.jsonld) for further details.

### Output

**To a triplestore:**
The pipeline stores the resulting RDF into a triple-store as configured in the Graph Store Protocol component (see Installation). By default, a new graph is created for the output and named according to the Data Package name, the IRI looks as follows:

    http://data.openbudgets.eu/resource/graph/[DataPackage name]
    
If the graph already exists, it is overwritten.

**To a filestore:** The pipeline can also store the resulting RDF in a file named according to the FDP package name. The file is sent through SCP protocol as configured in the Files to SCP component (see Installation).

For debug purposes, the output can also be downloaded through the Detail view of the Pipeline Execution UI in LinkedPipes, e.g. by displaying the "FDP to RDF" node output file.

## Further documentation

See the [developer's documentation](https://github.com/openbudgets/pipeline-fragments/blob/master/FDPtoRDF/documentation/FDPtoRDFdeveldocumentation.pdf) for details about the FDPtoRDF transformation process. Note that the latest changes in technical details of pipeline input and output are not reflected there, these are described in this file.

[LinkedPipes]: <http://etl.linkedpipes.com/> 
[Fiscal Data Package]: <http://fiscal.dataprotocols.org/>