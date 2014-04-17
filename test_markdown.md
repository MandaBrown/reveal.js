### All About The
## Data Warehouse

<pre><!-- Using pre instead of backticks because I can't make backticks stop syntax highlighting. -->
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

               +–––––––––––––+               +–––––––––––––––––+
               |             |    ETL        |                 |
               |             +––––––––––––––>|                 |
               |   Apangea   |               |    Data         |
               |             |               |    Warehouse    |
               |             |<-–––––––––––––+                 |
               |             |    Reports    |                 |
               +–––––––––––––+               +–––––––––––––––––+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>


### What's a data warehouse,
#### and why do we have one?

<img src='https://www.evernote.com/shard/s17/sh/02e999a0-0259-4f8e-8c88-278226fde62c/90148a96fa9c25d35f005e6b9a0da1e6/res/4bba156f-2e54-431f-abca-a609f33f0a56/skitch.png?resizeSmall&width=832' alt='data_warehouse_definition' />

- Ease of Reporting
- Performance
- Sharding


### What is the data model like?

- Facts
- Dimensions
  - SCD


- Star Schema
- Snowflake



### Where does ours live?

- Repo
- Hi, Data Warehouse!
<img src='https://www.evernote.com/shard/s17/sh/90740861-456b-4ac2-aaeb-ac16c1584b24/3b94c3c6f1c93fdf3f8594de72bd672e/res/b364ae8d-5ee3-4632-ac2d-b8169edf94ba/skitch.png?resizeSmall&width=832' alt='Warehouse Status'/>

- How to run and test it
  - http://localhost:3000/status

---

## Apangea

<pre><!-- Using pre instead of backticks because I can't make backticks stop syntax highlighting. -->
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

               +*************+               +–––––––––––––––––+
               *             *    ETL        |                 |
               *             +––––––––––––––>|                 |
               *   Apangea   *               |    Data         |
               *             *               |    Warehouse    |
               *             *<-–––––––––––––+                 |
               *             *    Reports    |                 |
               +*************+               +–––––––––––––––––+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>

---

## ETL

<pre><!-- Using pre instead of backticks because I can't make backticks stop syntax highlighting. -->
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

               +–––––––––––––+               +–––––––––––––––––+
               |             |    ETL        |                 |
               |             +**************>|                 |
               |   Apangea   |               |    Data         |
               |             |               |    Warehouse    |
               |             |<-–––––––––––––+                 |
               |             |    Reports    |                 |
               +–––––––––––––+               +–––––––––––––––––+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>


### What does ETL stand for?

- **E**xtract, **T**ransform, **L**oad


### ActiveWarehouse-Etl

- [GitHub repo][activewarehouse-etl_repo]

- What is it?
- Initial Load
- Incremental Load
  - Extracts from apangea (sharded or master) based on max id (or date, depending) to file in the same directory as the ctl files (attempt_fact.txt)
    - OR - Runs from file of that name in that location already
    - uses a csv parser to process tab-delimited data
    - we provide source_columns

  - Serialize original row to store original values (at least in attempt) **Processors**
  - Transforms **Transformers**
    - Match ids to warehouse versions of ids (foreign key lookups for date (SCDResolvers, attempt date))

  - checks if unresolved
    - checks if SCD resolvers worked (fields have a value)
    - if not, record is unresolved
    - can also pass in your own method that needs to return a map of unresolved fields
    - dumps record into unresolved_records in etl database

  - After processing (transformers & processors), file is dumped to tmp/<table_name>_etl.csv
  - That file is bulk imported into the warehouse database
  - attempt_fact.txt is deleted (tmp csv is left behind, but overwritten every time)

  - persists the max id that was processed


- How does this happen in production
  - Scalr Jobs


- Control Files
  - General structure
  - Setup to Extract

- Databases
  - ETL
  - Warehouse

- unresolved records


### Aggregation

- Dependent Facts

---

## Data Warehouse

<pre><!-- Using pre instead of backticks because I can't make backticks stop syntax highlighting. -->
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

               +–––––––––––––+               +*****************+
               |             |    ETL        *                 *
               |             +––––––––––––––>*                 *
               |   Apangea   |               *    Data         *
               |             |               *    Warehouse    *
               |             |<-–––––––––––––+                 *
               |             |    Reports    *                 *
               +–––––––––––––+               +*****************+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>

---

## Reports

<pre><!-- Using pre instead of backticks because I can't make backticks stop syntax highlighting. -->
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

               +–––––––––––––+               +–––––––––––––––––+
               |             |    ETL        |                 |
               |             +––––––––––––––>|                 |
               |   Apangea   |               |    Data         |
               |             |               |    Warehouse    |
               |             |<**************+                 |
               |             |    Reports    |                 |
               +–––––––––––––+               +–––––––––––––––––+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>

---

Reloading the warehouse

---

# The End


#### ♡ This presentation was made with... ♡
- [reveal.js](https://github.com/hakimel/reveal.js/)
- [MarkDown](https://help.github.com/articles/github-flavored-markdown)
- [AsciiFlow](http://asciiflow.com/)


[activewarehouse-etl_documentation]:(https://github.com/activewarehouse/activewarehouse-etl/wiki/Documentation)
[activewarehouse-etl_repo]:(https://github.com/activewarehouse/activewarehouse-etl)
☆
➤
