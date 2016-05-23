# Monetary value normalization

**URL:** 

This pipeline fragment normalizes monetary amounts using average yearly exchange rates and implicit deflators based on GDP, both of which are sourced from [Eurostat](http://eurostat.linked-statistics.org).

The pipeline supports DCV datasets described using the [OpenBudgets.eu data model](https://github.com/openbudgets/data-model). It is limited to datasets that represent their temporal dimension using the `obeu-dimension:date` (or its subproperty) with objects conforming to `xsd:date` or `obeu-dimension:fiscalYear` (or its subproperty) with objects drawn from the `http://reference.data.gov.uk` namespace. 

## Input

| Type          | Description                    |
| ------------- | ------------------------------ |
| RDF data unit | DCV dataset                    |
| RDF data unit | Dataset's DSD                  |

## Output

| Type            | Description        |
| --------------- | ------------------ |
| RDF data unit   | Normalized dataset | 

## Configuration

Country of each dataset must be provided manually using the SPARQL 1.1 `VALUES`. The country must be identified by the IRI from the [LinkedStatistics Geo](http://eurostat.linked-statistics.org/dic/geo) code list. 

```sparql
PREFIX ls-geo:         <http://eurostat.linked-statistics.org/dic/geo#>

[...]

VALUES (?dataset ?country) {
  (<http://data.openbudgets.eu/resource/list-of-beneficiaries-eu-funds/cz/2007-2013/dataset/paid> ls-geo:CZ)
}
```

Country is often not explicit in data, but instead it is implicitly linked via the organization that spent the normalized amount. In most datasets, there is a single organization operating in a single country, so it is possible to provide the country manually. 

## Prerequisites

The validated dataset must be in the DCV normal form. You can transform it to the normal form using the [DCV normalization pipeline fragment](https://github.com/openbudgets/pipeline-fragments/tree/master/dcv/dcv-normalization). If the validated pipeline reuses component properties defined by the core [OpenBudgets.eu data model](https://github.com/openbudgets/data-model), it must be available in the named graph `http://data.openbudgets.eu/ontology` in the configured RDF store (uses <http://obeu.vse.cz:8890/sparql> by default). The pipeline requires the `tec00033` and `nama_10_gdp` datasets to be available.
