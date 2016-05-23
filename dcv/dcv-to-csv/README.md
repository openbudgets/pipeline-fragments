# DCV to CSV transformation

**URL:** <https://github.com/openbudgets/pipeline-fragments/raw/master/dcv/dcv-to-csv/dcv_to_csv.jsonld>

Pipeline fragment that transforms [Data Cube Vocabulary](https://www.w3.org/TR/vocab-data-cube/) datasets into CSV tables using datasets' [data structure definitions](https://www.w3.org/TR/vocab-data-cube/#dsd) (DSD).

## Input

| Type          | Description   |
| ------------- | ------------- |
| RDF data unit | DCV dataset   |
| RDF data unit | Dataset's DSD |

## Output

| Type            | Description                    |
| --------------- | ------------------------------ |
| Files data unit | Dataset in CSV                 |

## Configuration

No configuration required.
