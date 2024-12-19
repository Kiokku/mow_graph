- > https://www.learndatalogtoday.org/chapter/1
-
- ## Chapter 1 - Basic Queries
  background-color:: blue
  collapsed:: true
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
  background-color:: blue
  collapsed:: true
	- There can be many data patterns in a `:where` clause:
	- ```
	  [:find ?title
	   :where
	   [?e :movie/year 1987]
	   [?e :movie/title ?title]]
	  ```
		- the pattern variable `?e` is used in both data patterns.
		- the query engine requires `?e` to be bound to the [[#blue]]==same value== in each place.
	- > exercise 1. Find movie titles made in 1985
		- ```
		  [:find ?title
		   :where
		   [?m :movie/title ?title]
		   [?m :movie/year 1985]]
		  ```
	- > exercise 2. What year was "Alien" released?
		- ```
		  [:find ?year
		   :where
		   [?m :movie/title "Alien"]
		   [?m :movie/year ?year]]
		  ```
	- > exercise 3. Who directed RoboCop? You will need to use `[<movie-eid> :movie/director <person-eid>]` to find the director for a movie.
		- ```
		  [:find ?name
		   :where
		   [?m :movie/title "RoboCop"]
		   [?m :movie/director ?p]
		   [?p :person/name ?name]
		   ]
		  ```
	- >  exercise 4. Find directors who have directed Arnold Schwarzenegger in a movie.
		- ```
		  [:find ?name
		   :where
		   [?p :person/name "Arnold Schwarzenegger"]
		   [?m :movie/cast ?p]
		   [?m :movie/director ?d]
		   [?d :person/name ?name]]
		  ```
- ## Chapter 3 - Parameterized queries
  background-color:: blue
  collapsed:: true
	- ### query with input parameters
	  background-color:: pink
		- ```
		  [:find ?title
		   :in $ ?name
		   :where
		   [?p :person/name ?name]
		   [?m :movie/cast ?p]
		   [?m :movie/title ?title]]
		  ```
		- `:in` - [[#green]]==Two arguments:==
			- `$`: the database.
				- In query, each of these data patterns is actually a **5 tuple**, of the form:
				  `[<database> <entity-id> <attribute> <value> <transaction-id>]`
			- `?name`
	- ### Tuples
	  background-color:: pink
		- A tuple input is written as e.g. `[?name ?age]` and can be used when you want to destructure an input.
		- `:in $ [?name ?age]` = `:in $ ?name ?age`
	- ### Collections
	  background-color:: pink
		- Use collection destructuring to implement a kind of logical **or**.
		- ```
		  [:find ?title
		  // ... is needed
		   :in $ [?director ...]
		   :where
		   [?p :person/name ?director]
		   [?m :movie/director ?p]
		   [?m :movie/title ?title]]
		  ```
	- ### Relations
	  background-color:: pink
		- Relations - a set of tuples - are the most interesting and powerful of input types, since you can join external relations with the datoms in your database.
		- A relation with tuples `[movie-title box-office-earnings]`:
			- ```
			  [
			   ...
			   ["Die Hard" 140700000]
			   ["Alien" 104931801]
			   ["Lethal Weapon" 120207127]
			   ["Commando" 57491000]
			   ...
			  ]
			  ```
		- Use this data and the data in the database to find box office earnings for a particular director:
			- ```
			  [:find ?title ?box-office
			   :in $ ?director [[?title ?box-office]]
			   :where
			   [?p :person/name ?director]
			   [?m :movie/director ?p]
			   [?m :movie/title ?title]]
			  ```
		- Note that the `?box-office` pattern variable does not appear in any of the data patterns in the [[#red]]==`:where`== clause.
- ## Chapter 4 - More queries
  background-color:: blue
  collapsed:: true
	- ### Attributes
	  background-color:: pink
		- ```
		  [:find ?attr
		   :where
		   [?p :person/name]
		   [?p ?a]
		   [?a :db/ident ?attr]]
		  ```
		- `:db/ident` - get the actual keywords
	- ### Transactions
	  background-color:: pink
		- ```
		  [:find ?timestamp
		   :where
		   [?p :person/name "James Cameron" ?tx]
		   [?tx :db/txInstant ?timestamp]]
		  ```
		- `:db/txInstant` - the instant in time when the transaction was committed to the database.
- ## Chapter 5 - Predicates
  background-color:: blue
  collapsed:: true
	- **data patterns**: `[?m :movie/year ?year]`
	- [[#green]]==**predicate clauses**: `[(< ?year 1984 )]`==
	- `<、>、<=、>=、=、not=`
	- The predicate clause filters the result set to only include results for which the predicate returns a "truthy" (non-nil, non-false) value.
	- ```
	  [:find ?title
	   :where
	   [?m :movie/title ?title]
	   [?m :movie/year ?year]
	   [(< ?year 1984)]]
	  ```
- ## Chapter 6 - Transformation functions
  background-color:: blue
  collapsed:: true
	- **Transformation functions** are pure (= side-effect free) functions or methods which can be used in queries to [[#green]]==transform values== and bind their results to pattern variables.
	- ```
	  (defn age [birthday today]
	    (quot (- (.getTime today)
	             (.getTime birthday))
	          (* 1000 60 60 24 365)))
	  ```
	- with this function, we can now calculate the age of a person **inside the query itself**:
	- ```
	  [:find ?age
	   :in $ ?name ?today
	   :where
	   [?p :person/name ?name]
	   [?p :person/born ?born]
	   [(tutorial.fns/age ?born ?today) ?age]]
	  ```
	- ### Transformation function clause
	  background-color:: pink
		- `[(<fn> <arg1> <arg2> ...) <result-binding>]`
		- [[#green]]==**<result-binding>:**==
			- Scalar: `?age`
			- Tuple: `[?foo ?bar ?baz]`
			- Collection: `[?name ...]`
			- Relation: `[[?title ?rating]]`
		- > Note: transformation functions can't be nested.
			- [[#red]]==Wrong:== `[(f (g ?x)) ?a]`
			- [[#green]]==Right:== `[(g ?x) ?t]` `[(f ?t) ?a]`
-