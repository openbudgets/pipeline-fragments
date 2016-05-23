# Load ECU/EUR exchange rates versus national currencies

**URL:** <https://github.com/openbudgets/pipeline-fragments/raw/master/monetary_value_normalization/preparation/load-tec00033/load_tec00033.jsonld>

Pipeline fragment to load the [ECU/EUR exchange rates versus national currencies](http://ec.europa.eu/eurostat/web/products-datasets/-/tec00033) (`tec00033`) dataset to a local RDF store into the named graph `http://eurostat.linked-statistics.org/data/tec00033`.

## Input

No input required.

## Output

No output.

## Configuration

SPARQL 1.1 Graph Store protocol connection for an RDF store configured in the [Graph store protocol](http://etl.linkedpipes.com/components/l-graphstoreprotocol) component.
