# OpenBudgets.eu data model validation

**URL:**

Pipeline fragment that validates additional [integrity constraints of the OpenBudgets.eu data model](https://github.com/openbudgets/data-model/tree/master/integrity-constraints) in fiscal datasets described using the data model.

## Input

| Type          | Description                    |
| ------------- | ------------------------------ |
| RDF data unit | DCV dataset                    |
| RDF data unit | Dataset's DSD                  |

## Output

| Type            | Description                                    |
| --------------- | ---------------------------------------------- |
| RDF data unit   | Description of integrity constraint violations |
| Files data unit | Validation report in HTML                      |

## Configuration

SPARQL 1.1 Graph store protocol connection to an RDF store to execute the validation must be provided in the [Graph store protocol](http://etl.linkedpipes.com/components/l-graphstoreprotocol) component.

## Prerequisites

The validated dataset must be in the DCV normal form. You can transform it to the normal form using the [DCV normalization pipeline fragment](https://github.com/openbudgets/pipeline-fragments/tree/master/dcv/dcv-normalization). The [OpenBudgets.eu data model](https://github.com/openbudgets/data-model) must be available in the named graph `http://data.openbudgets.eu/ontology` in the configured RDF store (uses <http://obeu.vse.cz:8890/sparql> by default). The OpenBudgets.eu data model can be loaded using [this pipeline fragment](https://github.com/openbudgets/pipeline-fragments/tree/master/obeu/load-obeu). 
