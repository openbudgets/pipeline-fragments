# DCV validation

**URL:**

Pipeline fragment that validates [Data Cube Vocabulary](https://www.w3.org/TR/vocab-data-cube/) (DCV) datasets using the [DCV integrity constraints](https://www.w3.org/TR/vocab-data-cube/#wf-rules).

## Input

| Type          | Description                    |
| ------------- | ------------------------------ |
| RDF data unit | DCV dataset                    |
| RDF data unit | Dataset's DSD                  |
| RDF data unit | Code lists used in the dataset |

## Output

| Type            | Description                                    |
| --------------- | ---------------------------------------------- |
| RDF data unit   | Description of integrity constraint violations |
| Files data unit | Validation report in HTML                      |

## Configuration

No configuration required.

## Prerequisites

The validated dataset must be in the DCV normal form. You can transform it to the normal form using the [DCV normalization pipeline fragment](https://github.com/openbudgets/pipeline-fragments/tree/master/dcv/dcv-normalization). If the validated pipeline reuses component properties defined by the core [OpenBudgets.eu data model](https://github.com/openbudgets/data-model), it must be available in the named graph `http://data.openbudgets.eu/ontology` in the configured RDF store (uses <http://obeu.vse.cz:8890/sparql> by default).
