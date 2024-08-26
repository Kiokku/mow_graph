- > https://www.learndatalogtoday.org/chapter/1
-
- ## Chapter 1 - Basic Queries
  background-color:: blue
	- ### datoms
		- A datom is a 4-tuple consisting of
			- `Entity ID`
			- `Attribute`
			- `Value`
			- `Transaction ID`
			- examples:
			- ```
			  [<e-id>  <attribute>      <value>          <tx-id>]
			  ...
			  [ 167    :person/name     "James Cameron"    102  ]
			  [ 234    :movie/title     "Die Hard"         102  ]
			  [ 234    :movie/year      1987               102  ]
			  [ 235    :movie/title     "Terminator"       102  ]
			  [ 235    :movie/director  167                102  ]
			  ...
			  
			  e.g. [?e :person/name ?p _]
			  ```
	- ### query
		- A **query** is represented as a [[#green]]==vector== starting with the keyword `:find` followed by one or more **pattern variables** (symbols starting with `?`, e.g. `?title`).
		- ```
		  [:find ?e
		   :where
		   [?e :person/name "Ridley Scott"]]
		  ```
		- this query finds all entity-ids that have the attribute `:person/name` with a value of `"Ridley Scott"`
- ## Chapter 2 - Data patterns
	- There can be many data patterns in a `:where` clause:
	- ```
	  [:find ?title
	   :where
	   [?e :movie/year 1987]
	   [?e :movie/title ?title]]
	  ```
		- the pattern variable `?e` is used in both data patterns.
		- the query engine requires `?e` to be bound to the [[#blue]]==same value== in each place.
	- > exercise 1: Find movie titles made in 1985
		-