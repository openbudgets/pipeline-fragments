# Cache fiscal years from reference.data.gov.uk

**URL:** <https://github.com/openbudgets/pipeline-fragments/raw/master/caching/cache-reference.data.gov.uk/cache_reference.data.gov.uk.jsonld>

Pipeline fragment to cache the descriptions of fiscal years reused from the `reference.data.gov.uk` namespace to an RDF store into the named graph `http://reference.data.gov.uk`.

## Input

No input required.

## Output

No output.

## Configuration

SPARQL 1.1 Graph Store protocol connection for an RDF store configured in the [Graph store protocol](http://etl.linkedpipes.com/components/l-graphstoreprotocol) component.
