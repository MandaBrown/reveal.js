# The Data Warehouse

<pre><!-- Using pre instead of backticks because I can't make backticks stop syntax highlighting. -->
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

             +–––––––––––––+               +–––––––––––––––––+
             |             |    ETL        |                 |
             |             +––––––––––––––>|                 |
             |   Apangea   |               |    Data         |
             |             |               |    Warehouse    |
             |             |<-–––––––––––––+                 |
             |             |    Reports    |                 |
             +–––––––––––––+               +–––––––––––––––––+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>

---

###  What's a data warehouse,
#### and why do we have one?


A **data warehouse** is a data structure that is optimized for data distribution.


- It makes reporting easier.
- There are performance gains.
- It adds a measure of reliability.


<img src='http://v1.memecaptain.com/9d85c7.jpg' alt='SHARDING!' />

---

### Where in our application is data being provided by the data warehouse?

<pre>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

             +*************+               +–––––––––––––––––+
             *             *    ETL        |                 |
             *             +––––––––––––––>|                 |
             *   Apangea   *               |    Data         |
             *             *               |    Warehouse    |
             *             *<-–––––––––––––+                 |
             *             *    Reports    |                 |
             +*************+               +–––––––––––––––––+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>


#### Dashboards

<img src='http://www.evernote.com/shard/s17/sh/bf6f09d9-4358-47a5-9457-dc660ad62a1d/bd43fcc5eed0b811b7af6bc5b468f053/res/2b70173b-3dfe-4a7d-9268-1fbce1ee6722/skitch.png' alt='Teacher Dashboard' height=400px />


#### Overview Report

<img src='http://www.evernote.com/shard/s17/sh/de09d94e-a536-414d-b370-beaa15b847a2/e46ae3ad36adc1556a4d80db33a842c0/res/24583c27-6a59-42eb-ac07-ee29e013ef73/skitch.png' alt='Overview Report' />


#### The Individual Student Pathway Report is **not** driven by warehouse data

<img src='http://www.evernote.com/shard/s17/sh/263d4aac-00f8-4096-95eb-601bccd66b7b/699f57320f85f45e1480ab51c251458c/res/8e1dd001-557b-4ea0-9985-61a54c1a3a08/skitch.png' alt='Individual Student Pathway Report' />


#### Outside of Apangea, the Ad-Hoc Portal and TTM Dashboards

<img src='http://www.evernote.com/shard/s17/sh/c55ed20d-72dd-497e-857a-939f5e11406e/0f4575429ba9a77fae9c707cd025c68a/res/15b74635-0f7e-41c7-94aa-d46d2ca03fc3/skitch.png' alt='Ad-Hoc Portal' height=400px />

---

### What does the data in the data warehouse look like?

<pre>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

             +–––––––––––––+               +*****************+
             |             |    ETL        *                 *
             |             +––––––––––––––>*                 *
             |   Apangea   |               *    Data         *
             |             |               *    Warehouse    *
             |             |<-–––––––––––––+                 *
             |             |    Reports    *                 *
             +–––––––––––––+               +*****************+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

</pre>