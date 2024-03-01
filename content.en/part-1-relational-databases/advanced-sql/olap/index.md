---
title: 'OLAP'
weight: 6
references:
    videos:
        - youtube:NuVAgAgemGI
        - youtube:ul5Dk7bK5uA
    links:
        - https://www.techtarget.com/searchdatamanagement/definition/OLAP
        - https://www.javatpoint.com/what-is-olap
    books:
        - b1:
            title: Data Warehouses and OLAP
            url: https://www.google.co.in/books/edition/Data_Warehouses_and_OLAP/XFivorxZDm8C?hl=en&gbpv=1&dq=OLAP+book&printsec=frontcover
        - b2:
            title: OLAP Solution
            url: :https://www.google.co.in/books/edition/OLAP_Solutions/eskZA1CFdqMC?hl=en&gbpv=1&dq=OLAP+book&printsec=frontcover
---

# OLAP

An online analytical processing (OLAP) system is an interactive system that permits an analyst to view different summaries of multidimensional data. The word _online_ indicates that an analyst must be able to request new summaries and get responses online, within a few seconds, and should not be forced to wait for a long time to see the result of a query.

There are many OLAP products available, including some that ship with database products such as Microsoft SQL Server, and Oracle, and other standalone tools. The initial versions of many OLAP tools assumed that data is memory resident. Data analysis on small amounts of data can in fact be performed using spreadsheet applications, such as Excel. However, OLAP on very large amounts of data requires that data be resident in a database, and requires support from the database for efficient preprocessing of data as well as for online query processing. In this section, we study extensions of SQL to support such tasks.

## Online Analytical Processing

Consider an application where a shop wants to find out what kinds of clothes are popular. Let us suppose that clothes are characterized by their item name, color, and size, and that we have a relation _sales_ with the schema.

![Alt text](image-39.png)

**Figure 5.16** An example of _sales_ relation.

Suppose that _item name_ can take on the values (skirt, dress, shirt, pants), _color_ can take on the values (dark, pastel, white), _clothes size_ can take on values (small, medium, large), and _quantity_ is an integer value representing the total number of items of a given _{item name_, _color_, _clothes size }_. An instance of the _sales_ relation is shown in Figure 5.16.  

Statistical analysis often requires grouping on multiple attributes. Given a relation used for data analysis, we can identify some of its attributes as **measure** attributes, since they measure some value, and can be aggregated upon. For instance, the attribute _quantity_ of the _sales_ relation is a measure attribute, since it measures the number of units sold. Some (or all) of the other attributes of the relation are identified as **dimension attributes**, since they define the dimensions on which measure attributes, and summaries of measure attributes, are viewed. In the _sales_ relation, _item name_, _color_, and _clothes size_ are dimension attributes. (A more realistic version of the _sales_ relation would have additional dimensions, such as time and sales location, and additional measures such as monetary value of the sale.)

Data that can be modeled as dimension attributes and measure attributes are called **multidimensional data**.

To analyze the multidimensional data, a manager may want to see data laid out as shown in the table in Figure 5.17. The table shows total quantities for different combinations of _item name_ and _color_. The value of _clothes size_ is specified to be **all**, indicating that the displayed values are a summary across all values of _clothes size_ (that is, we want to group the “small”, “medium”, and “large” items into one single group.

The table in Figure 5.17 is an example of a **cross-tabulation** (or **cross-tab**, for short), also referred to as a **pivot-table**. In general, a cross-tab is a table derived from a relation (say _R_), where values for one attribute of relation _R_ (say _A_) form the row headers and values for another attribute of relation _R_ (say _B_) form the column header. For example, in Figure 5.17, the attribute _item name_ corresponds to _A_ (with values “dark”, “pastel”, and “white”), and the attribute _color_ corresponds to to _B_ (with attributes “skirt”, “dress”, “shirt”, and “pants”).

Each cell in the pivot-table can be identified by (_ai , b j_ ), where _ai_ is a value for _A_ and _b j_ a value for _B_. The values of the various cells in the pivot-table are derived from the relation _R_ as follows: If there is at most one tuple in _R_ with any (_ai , b j_ ) value, the value in the cell is derived from that single tuple (if any); for instance, it could be the value of one or more other attributes of the tuple. If there can be multiple tuples with an (_ai , b j_ ) value, the value in the cell must be derived

![Alt text](image-40.png)

**Figure 5.17** Cross tabulation of _sales_ by _item name_ and _color_.

![Alt text](image-41.png)

**Figure 5.18** Three-dimensional data cube.

by aggregation on the tuples with that value. In our example, the aggregation used is the sum of the values for attribute _quantity_, across all values for _clothes size_, as indicated by “_clothes size_: **all**” above the cross-tab in Figure 5.17. Thus, the

value for cell (skirt, pastel) is 35, since there are 3 tuples in the _sales_ table that meet that criteria, with values 11, 9, and 15.

In our example, the cross-tab also has an extra column and an extra row storing the totals of the cells in the row/column. Most cross-tabs have such summary rows and columns.

The generalization of a cross-tab, which is two-dimensional, to _n_ dimensions can be visualized as an _n_\-dimensional cube, called the **data cube**. Figure 5.18 shows a data cube on the _sales_ relation. The data cube has three dimensions, _item name_, _color_, and _clothes size_, and the measure attribute is _quantity_. Each cell is identified by values for these three dimensions. Each cell in the data cube contains a value, just as in a cross-tab. In Figure 5.18, the value contained in a cell is shown on one of the faces of the cell; other faces of the cell are shown blank if they are visible. All cells contain values, even if they are not visible. The value for a dimension may be **all**, in which case the cell contains a summary over all values of that dimension, as in the case of cross-tabs.

The number of different ways in which the tuples can be grouped for aggregation can be large. In the example of Figure 5.18, there are 3 colors, 4 items, and 3 sizes resulting in a cube size of 3 × 4 × 3 = 36. Including the summary values, we obtain a 4 × 5 × 4 cube, whose size is 80. In fact, for a table with _n_ dimensions, aggregation can be performed with grouping on each of the 2_n_ subsets of the _n_ dimensions.12

With an OLAP system, a data analyst can look at different cross-tabs on the same data by interactively selecting the attributes in the cross-tab. Each cross-tab is a two-dimensional view on a multidimensional data cube. For instance, the analyst may select a cross-tab on _item name_ and _clothes size_ or a cross-tab on _color_ and _clothes size_. The operation of changing the dimensions used in a cross-tab is called **pivoting**.

OLAP systems allow an analyst to see a cross-tab on _item name_ and _color_ for a fixed value of _clothes size_, for example, large, instead of the sum across all sizes. Such an operation is referred to as **slicing**, since it can be thought of as viewing a slice of the data cube. The operation is sometimes called **dicing**, particularly when values for multiple dimensions are fixed.

When a cross-tab is used to view a multidimensional cube, the values of dimension attributes that are not part of the cross-tab are shown above the crosstab. The value of such an attribute can be **all**, as shown in Figure 5.17, indicating that data in the cross-tab are a summary over all values for the attribute. Slicing/dicing simply consists of selecting specific values for these attributes, which are then displayed on top of the cross-tab.

OLAP systems permit users to view data at any desired level of granularity. The operation of moving from finer-granularity data to a coarser granularity (by means of aggregation) is called a **rollup**. In our example, starting from the data cube on the _sales_ table, we got our example cross-tab by rolling up on the attribute _clothes size_. The opposite operation—that of moving from coarser-granularity data to finer-granularity data—is called a **drill down**. Clearly, finer-granularity data cannot be generated from coarse-granularity data; they must be generated either from the original data, or from even finer-granularity summary data.

Analysts may wish to view a dimension at different levels of detail. For instance, an attribute of type **datetime** contains a date and a time of day. Using time precise to a second (or less) may not be meaningful: An analyst who is interested in rough time of day may look at only the hour value. An analyst who is interested in sales by day of the week may map the date to a day of the week and look only at that. Another analyst may be interested in aggregates over a month, or a quarter, or for an entire year.

The different levels of detail for an attribute can be organized into a **hierarchy**. Figure 5.19a shows a hierarchy on the **datetime** attribute. As another example, Figure 5.19b shows a hierarchy on location, with the city being at the bottom of the hierarchy, state above it, country at the next level, and region being the top level. In our earlier example, clothes can be grouped by category (for instance, menswear or womenswear); _category_ would then lie above _item name_ in our hierarchy on clothes. At the level of actual values, skirts and dresses would fall under the womenswear category and pants and shirts under the menswear category.

An analyst may be interested in viewing sales of clothes divided as menswear and womenswear, and not interested in individual values. After viewing the aggregates at the level of womenswear and menswear, an analyst may _drill down the hierarchy_ to look at individual values. An analyst looking at the detailed level may _drill up the hierarchy_ and look at coarser-level aggregates. Both levels can be displayed on the same cross-tab, as in Figure 5.20. 

![Alt text](image-42.png)

**Figure 5.19** Hierarchies on dimensions.

## Cross-Tab and Relational Tables

A cross-tab is different from relational tables usually stored in databases, since the number of columns in the cross-tab depends on the actual data. A change in the data values may result in adding more columns, which is not desirable for data storage. However, a cross-tab view is desirable for display to users. It is straightforward to represent a cross-tab without summary values in a relational form with a fixed number of columns. A cross-tab with summary rows/columns can be represented by introducing a special value **all** to represent subtotals, as in Figure 5.21. The SQL standard actually uses the **null** value in place of **all**, but to avoid confusion with regular null values, we shall continue to use **all**.

![Alt text](image-43.png)

**Figure 5.20** Cross tabulation of _sales_ with hierarchy on _item name_.  

![Alt text](image-44.png)

**Figure 5.21** Relational representation of the data in Figure 5.17.

Consider the tuples (skirt, **all**, **all**, 53) and (dress, **all**, **all**, 35). We have obtained these tuples by eliminating individual tuples with different values for _color_ and _clothes size_, and by replacing the value of _quantity_ by an aggregate—namely, the sum of the quantities. The value **all** can be thought of as representing the set of all values for an attribute. Tuples with the value **all** for the _color_ and _clothes size_ dimensions can be obtained by an aggregation on the _sales_ relation with a **group by** on the column _item name_. Similarly, a **group by** on _color_, _clothes size_ can be used to get the tuples with the value **all** for _item name_, and a **group by** with no attributes (which can simply be omitted in SQL) can be used to get the tuple with value **all** for _item name_, _color_, and _clothes size_.

Hierarchies can also be represented by relations. For example, the fact that skirts and dresses fall under the womenswear category, and the pants and shirts under the menswear category can be represented by a relation _itemcategory_(_item name_, _c_ategory). This relation can be joined with the _sales_ relation, to get a relation

that includes the category for each item. Aggregation on this joined relation allows us to get a cross-tab with hierarchy. As another example, a hierarchy on city can be represented by a single relation _city hierarchy_ (ID, _city_, _state_, _country_, _region_), or by multiple relations, each mapping values in one level of the hierarchy to values at the next level. We assume here that cities have unique identifiers, stored in the attribute ID, to avoid confusing between two cities with the same name, e.g., the Springfield in Missouri and the Springfield in Illinois.  

**OLAP IMPLEMENTATION**

The earliest OLAP systems used multidimensional arrays in memory to store data cubes, and are referred to as **multidimensional OLAP (MOLAP)** systems. Later, OLAP facilities were integrated into relational systems, with data stored in a relational database. Such systems are referred to as **relational OLAP (ROLAP)** systems. Hybrid systems, which store some summaries in memory and store the base data and other summaries in a relational database, are called **hybrid OLAP (HOLAP)** systems.

Many OLAP systems are implemented as client-server systems. The server contains the relational database as well as any MOLAP data cubes. Client systems obtain views of the data by communicating with the server.

A naı̈ve way of computing the entire data cube (all groupings) on a relation is to use any standard algorithm for computing aggregate operations, one grouping at a time. The naı̈ve algorithm would require a large number of scans of the relation. A simple optimization is to compute an aggregation on, say, (_item name_, _color_) from an aggregation (_item name_, _color_, _clothes size_), instead of from

the original relation. For the standard SQL aggregate functions, we can compute an aggregate with

grouping on a set of attributes _A_ from an aggregate with grouping on a set of attributes _B_ if _A_ ⊆ _B_; you can do so as an exercise (see Exercise 5.24), but note that to compute **avg**, we additionally need the **count** value. (For some nonstandard aggregate functions, such as median, aggregates cannot be computed as above; the optimization described here does not apply to such _non-decomposable_ aggregate functions.) The amount of data read drops significantly by computing an aggregate from another aggregate, instead of from the original relation. Further improvements are possible; for instance, multiple groupings can be computed on a single scan of the data.

Early OLAP implementations precomputed and stored entire data cubes, that is, groupings on all subsets of the dimension attributes. Precomputation allows OLAP queries to be answered within a few seconds, even on datasets that may contain millions of tuples adding up to gigabytes of data. However, there are 2_n_ groupings with _n_ dimension attributes; hierarchies on attributes increase the number further. As a result, the entire data cube is often larger than the original relation that formed the data cube and in many cases it is not feasible to store the entire data cube.

Instead of precomputing and storing all possible groupings, it makes sense to precompute and store some of the groupings, and to compute others on demand. Instead of computing queries from the original relation, which may take a very long time, we can compute them from other precomputed queries. For instance, suppose that a query requires grouping by (_item name_, _color_), and this has not been precomputed. The query result can be computed from summaries by (_item name_, _color_, _clothes size_), if that has been precomputed. See the bibliographical

notes for references on how to select a good set of groupings for precomputation, given limits on the storage available for precomputed results.  

## OLAP in SQL

Several SQL implementations, such as Microsoft SQL Server, and Oracle, support a **pivot** clause in SQL, which allows creation of cross-tabs. Given the _sales_ relation from Figure 5.16, the query:

**select** \* 
**from** _sales_ 
**pivot** (
**sum**(_quantity_) 
**for** _color_ **in** (’dark’,’pastel’,’white’
)
) 
**order by** _item name_;

returns the cross-tab shown in Figure 5.22. Note that the **for** clause within the **pivot** clause specifies what values from the attribute _color_ should appears as attribute names in the pivot result. The attribute _color_ itself is eliminated from the result, although all other attributes are retained, except that the values for the newly created attributes are specified to come from the attribute _quantity_. In case more than one tuple contributes values to a given cell, the aggregate operation within the **pivot** clause specifies how the values should be combined. In the above example, the _quantity_ values are summed up.

Note that the pivot clause by itself does not compute the subtotals we saw in the pivot table from Figure 5.17. However, we can first generate the relational representation shown in Figure 5.21, as outlined shortly, and then apply the pivot clause on that representation to get an equivalent result. In this case, the value **all** must also be listed in the **for** clause, and the **order by** clause needs to be modified to order **all** at the end.



**Figure 5.22** Result of SQL pivot operation on the _sales_ relation of Figure 5.16.  

![Alt text](image-46.png)

**Figure 5.23** Query result.

The data in a data cube cannot be generated by a single SQL query, using the basic **group by** constructs, since aggregates are computed for several different groupings of the dimension attributes. For this reason, SQL includes functions to form the grouping needed for OLAP. We discuss these below.

SQL supports generalizations of the **group by** construct to perform the **cube** and **rollup** operations. The **cube** and **rollup** constructs in the **group by** clause allow multiple **group by** queries to be run in a single query with the result returned as a single relation in a style similar to that of the relation of Figure 5.21.

Consider again our retail shop example and the relation:

_sales_ (_item name_, _color_, _clothes size_, _quantity_)

We can find the number of items sold in each item name by writing a simple **group by** query:

**select** _item name_, **sum**(_quantity_) 
**from** _sales_ 
**group by** _item name_;

The result of this query is shown in Figure 5.23. Note that this represents the same data as the last column of Figure 5.17 (or equivalently, the first row in the cube of Figure 5.18).

Similarly, we can find the number of items sold in each color, etc. By using multiple attributes in the **group by** clause, we can find how many items were sold with a certain set of properties. For example, we can find a breakdown of sales by item-name and color by writing:

**select** _item name_, _color_, **sum**(_quantity_) 
**from** _sales_ 
**group by** _item name_, _color_;

The result of this query is shown in Figure 5.24. Note that this represents the same data as is shown in the the first 4 rows and first 4 columns of Figure 5.17 (or equivalently, the first 4 rows and columns in the cube of Figure 5.18).

If, however, we want to generate the entire data cube using this approach, we would have to write a separate query for each of the following sets of attributes:  

![Alt text](image-47.png)

**Figure 5.24** Query result.

_{_ (_item name_, _color_, _clothes size_), (_item name_, _color_), (_item name_, _clothes size_), (_color_, _clothes size_), (_item name_), (_color_), (_clothes size_), () _}_

where () denotes an empty **group by** list. The **cube** construct allows us to accomplish this in one query:

**select** _item name_, color, _clothes size_, **sum**(_quantity_) **from** _sales_ **group by cube**(_item name_, _color_, _clothes size_);

The above query produces a relation whose schema is:

(_item name_, _color_, _clothes size_, **sum**(_quantity_))

So that the result of this query is indeed a relation, tuples in the result contain _null_ as the value of those attributes not present in a particular grouping. For example, tuples produced by grouping on _clothes size_ have a schema (_clothes size_, **sum**(_quantity_)). They are converted to tuples on (_item name_, _color_, _clothes size_,

**sum**(_quantity_)) by inserting _null_ for _item name_ and _color_. Data cube relations are often very large. The cube query above, with 3 possible

colors, 4 possible item names, and 3 sizes, has 80 tuples. The relation of Figure 5.21 is generated using grouping by _item name_ and _color_. It also uses **all** in place of _null_ so as to be more readable to the average user. To generate that relation in SQL, we arrange to substitute **all** for _null_. The query:

**select** _item name_, _color_, **sum**(_quantity_) **from** _sales_ **group by cube**(_item name_, _color_);  

**THE DECODE FUNCTION**

The **decode** function allows substitution of values in an attribute of a tuple. The general form of **decode** is:

**decode** (_value, match-1, replacement-1, match-2, replacement-2, . . ., match-N, replacement-N, default-replacement_);

It compares _value_ against the _match_ values and if a match is found, it replaces the attribute value with the corresponding replacement value. If no match succeeds, then the attribute value is replaced with the default replacement value.

The **decode** function does not work as we might like for null values because, as we saw in Section 3.6, predicates on nulls evaluate to **unknown**, which ultimately becomes **false**. To deal with this, we apply the **grouping** function, which returns 1 if its argument is a null value generated by a **cube** or **rollup** and 0 otherwise. Then the relation in Figure 5.21, with occurrences of **all** replaced by _null_, can be computed by the query:

**select decode**(**grouping**(_item name_), 1, ’all’, _item name_) **as** _item name_ **decode**(**grouping**(_color_), 1, ’all’, _color_) **as** _color_ **sum**(_quantity_) **as** _quantity_
**from** _sales_ 
**group by cube**(_item name_, _color_);

generates the relation of Figure 5.21 with nulls. The substitution of **all** is achieved using the SQL **decode** and **grouping** functions. The **decode** function is conceptually simple but its syntax is somewhat hard to read. See blue box for details.

The **rollup** construct is the same as the **cube** construct except that **rollup** generates fewer **group by** queries. We saw that **group by cube** (_item name_, _color_, _clothes size_) generated all 8 ways of forming a **group by** query using some (or all or none) of the attributes. In:

**select** _item name_, _color_, _clothes size_, **sum**(_quantity_) 
**from** _sales_ 
**group by rollup**(_item name_, _color_, _clothes size_);

**group by rollup**(_item name_, _color_, _clothes size_) generates only 4 groupings:

_{_ (_item name_, _color_, _clothes size_), (_item name_, _color_), (_item name_), () _}_

Notice that the order of the attributes in the **rollup** makes a difference; the final attribute (_clothes size_, in our example) appears in only one grouping, the penultimate (second last) attribute in 2 groupings, and so on, with the first attribute appearing in all groups but one (the empty grouping).  

Why might we want the specific groupings that are used in **rollup**? These groups are of frequent practical interest for hierarchies (as in Figure 5.19, for example). For the location hierarchy (_Region, Country, State, City_), we may want to group by _Region_ to get sales by region. Then we may want to “drill down” to the level of countries within each region, which means we would group by _Region, Country_. Drilling down further, we may wish to group by _Region, Country, State_ and then by _Region, Country, State, City_. The **rollup** construct allows us to specify this sequence of drilling down for further detail.

Multiple **rollup**s and **cube**s can be used in a single **group by** clause. For instance, the following query:

**select** _item name_, _color_, _clothes size_, **sum**(_quantity_) 
**from** _sales_ 
**group by rollup**(_item name_), **rollup**(_color_, _clothes size_);

generates the groupings:

_{_ (_item name_, _color_, _clothes size_), (_item name_, _color_), (_item name_), (_color_, _clothes size_), (_color_), () _}_

To understand why, observe that **rollup**(_item name_) generates two groupings, _{_(_item name_), ()_}_, and **rollup**(_color_, _clothes size_) generates three groupings, _{_(_color_, _clothes size_), (_color_), () _}_. The Cartesian product of the two gives us the six groupings shown.

Neither the **rollup** nor the **cube** clause gives complete control on the groupings that are generated. For instance, we cannot use them to specify that we want only groupings _{_(_color_, _clothes size_), (_clothes size_, _item name_)_}_. Such restricted groupings can be generated by using the **grouping** construct in the **having** clause; we leave the details as an exercise for you.

