##Source
https://github.com/openspending/fiscal-data-package-demos/tree/master/au-budget
Changes: 
- turned into JSON-LD adding the context
- 'mapping' renamed to 'model' (the example used older FDP version)
##Output
Seems fine, there are no dimension types specified, just a lot of data, so nothing to go wrong.
A small issue are whitespaces in column names - they work fine, but end up in the IRIs as original unencoded whitespaces.
