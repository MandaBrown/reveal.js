ToDo: Come up with a better name. 'Data Warehousing for Fun and Profit'?
ToDo: Replace ~ lines with something a little bit more regular markdown friendly.

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

- Facts - ours
- Dimensions - ours
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

Databases & Testing

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


- Initial Load
  - reload_reporting
    - clean_reporting
    - load_dimensions - can do only new
    - load_facts


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


    - queue job
      - ProcessEtlWorker.perform
        - run_etl
          - goes through control files & processes them
          - incremental_etl.ebf
          ```ruby
          run 'classroom_dimension.ctl'
          run 'customer_dimension.ctl'
          run 'district_dimension.ctl'
          run 'empty_district_dimension.ctl'
          run 'math_lesson_dimension.ctl'
          run 'pathway_dimension.ctl'
          run 'school_dimension.ctl'
          run 'student_dimension.ctl'
          run 'teacher_dimension.ctl'
          run 'donateable_dimension.ctl'
          run 'standard_dimension.ctl'

          run 'attempt_fact.ctl'
          run 'donation_fact.ctl'
          run 'hint_view_fact.ctl'
          run 'lesson_completed_fact.ctl'
          run 'live_help_fact.ctl'
          run 'placement_test_completed_fact.ctl'
          ```
        - aggregate

        ```ruby
          def aggregate
            AttemptByDateStudentClassroomAggregate.aggregate!
            LessonLeaderboardAggregate.aggregate_incremental!
            PointLeaderboardAggregate.aggregate_incremental!
            ClassroomAggregator.new.aggregate
            StudentDeleter.new.delete_students!
            LiveHelpFact.aggregate_incremental!
            LessonProjector.new.project_lessons!
            ProjectsArraysToTables.project_all!
          end
          ```


    - Aggregate data
    ```ruby
    facts = [
      HintViewFact,
      PlacementTestCompletedFact,
      LiveHelpFact,
      LessonCompletedFact,
      AttemptFact,
    ]

    # We are truncating and then aggregating here to get around an issue
    # involving data not getting aggregated for that day after this runs.
    # We could speed it up by removing the truncation, but we'd need to be
    # smart about aggregating data already in the agg table for the same date
    # as this rake task is being run.
    ```
      - truncate aggregates
      - aggregate all


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
