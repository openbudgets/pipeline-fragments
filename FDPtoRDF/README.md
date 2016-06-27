# FDPtoRDF

This is a [LinkedPipes] pipeline fragment. It takes a [Fiscal Data Package] (FDP) .jsonld descriptor as input, transforms the FDP into RDF OBEU representation and stores the result into a predefined triple-store.

## Installation

1. **Import** [the pipeline] into LinkedPipes using the button in the bottom-right corner of the LinkedPipes UI. You can reference to pipeline's GitHub URL directly.
2. Open the pipeline in LinkedPipes and **configure the Graph Store Protocol component** (in the bottom-right corner of the pipeline, the node is highlighted by red). Follow the [component's configuration documentation](http://etl.linkedpipes.com/components/l-graphstoreprotocol) if needed and change the following to appropriate values of the triple-store where you want to store the results:
    - Repository type (Fuseki/Virtuoso/etc.)
    - Graph Store protocol endpoint
    - User name and password
3. Don't forget to click the **"apply changes"** and then **"save"** the whole pipeline (bottom-left button in the pipeline editor).

## Usage

The pipeline is executed through a HTTP POST request to its URL. The POST has to include the FDP .jsonld descriptor. The resulting RDF is stored into a triple-store as configured in the pipeline. The URL for pipeline execution looks like this:
    
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

The input of the pipeline is a .jsonld FDP descriptor file sent in the POST request (datapackage.jsonld in the CURL example above). The file must 
- correspond to the [FDP descriptor specification](http://fiscal.dataprotocols.org/spec/#descriptor--datapackagejson) and in addition to it:
- have the .jsonld extension
- all references to CSV data files (resources' paths) in it must be dereferencable URLs
- contain the following property in the root object:
```
    "@context": “http://schemas.frictionlessdata.io/fiscal-data-package.jsonld”,
```
### Output

The pipeline stores the resulting RDF into a triple-store as configured in the Graph Store Protocol component (see Installation). By default, the results are added into an RDF graph with the following IRI:

    http://data.openbudgets.eu/fdp-datasets
    
*(The next version of the pipeline should support creating a new graph named according to the datapackage name.)*

For debug purposes, the output can be also downloaded through the Detail view of the Pipeline Execution UI in LinkedPipes, e.g. by displaying the "RDF representation of the FDP in a .ttl file" node output file.

## Further documentation

See the developer's documentation for details about the FDPtoRDF transformation process. Note that the latest changes in technical details of pipeline input and output are not reflected there, these are described in this file.

[LinkedPipes]: <http://etl.linkedpipes.com/> 
[Fiscal Data Package]: <http://fiscal.dataprotocols.org/>
