---
title: 'Integrity Constraints'
weight: 4
---

# Integrity Constraints

Integrity constraints ensure that changes made to the database by authorized users do not result in a loss of data consistency. Thus, integrity constraints guard against accidental damage to the database.

Examples of integrity constraints are:

• An instructor name cannot be _null_.

• No two instructors can have the same instructor ID.

• Every department name in the _course_ relation must have a matching department name in the _department_ relation.

• The budget of a department must be greater than $0.00.

In general, an integrity constraint can be an arbitrary predicate pertaining to the database. However, arbitrary predicates may be costly to test. Thus, most database systems allow one to specify integrity constraints that can be tested with minimal overhead.

We have already seen some forms of integrity constraints in Section 3.2.2. We study some more forms of integrity constraints in this section. In Chapter 8, we study another form of integrity constraint, called **functional dependencies**, that is used primarily in the process of schema design.

Integrity constraints are usually identified as part of the database schema design process, and declared as part of the **create table** command used to create relations. However, integrity constraints can also be added to an existing relation by using the command **alter table** _table-name_ **add** _constraint_, where _constraint_ can be any constraint on the relation. When such a command is executed, the system first ensures that the relation satisfies the specified constraint. If it does, the constraint is added to the relation; if not, the command is rejected.

## Constraints on a Single Relation

We described in Section 3.2 how to define tables using the **create table** command. The **create table** command may also include integrity-constraint statements. In addition to the primary-key constraint, there are a number of other ones that can be included in the **create table** command. The allowed integrity constraints include

• **not null**

• **unique**

• **check**(_<_predicate_\>_)

We cover each of these types of constraints in the following sections.

## Not Null Constraint

As we discussed in Chapter 3, the null value is a member of all domains, and as a result is a legal value for every attribute in SQL by default. For certain attributes, however, null values may be inappropriate. Consider a tuple in the _student_ relation where _name_ is _null_. Such a tuple gives student information for an unknown student; thus, it does not contain useful information. Similarly, we would not want the department budget to be _null_. In cases such as this, we wish to forbid null values, and we can do so by restricting the domain of the attributes _name_ and _budget_ to exclude null values, by declaring it as follows:

_name_ **varchar**(20) **not null** 
_budget_ **numeric**(12,2) **not null**

The **not null** specification prohibits the insertion of a null value for the attribute. Any database modification that would cause a null to be inserted in an attribute declared to be **not null** generates an error diagnostic.

There are many situations where we want to avoid null values. In particular, SQL prohibits null values in the primary key of a relation schema. Thus, in our university example, in the _department_ relation, if the attribute _dept name_ is declared as the primary key for _department_, it cannot take a null value. As a result it would not need to be declared explicitly to be **not null**.

## Unique Constraint

SQL also supports an integrity constraint:

**unique** (_Aj1,Aj2_, . . . , Ajm_)

The **unique** specification says that attributes _Aj1 _, Aj2_, . . . , Ajm_ form a candidate key; that is, no two tuples in the relation can be equal on all the listed attributes. However, candidate key attributes are permitted to be _null_ unless they have explicitly been declared to be **not null**. Recall that a null value does not equal any other value. (The treatment of nulls here is the same as that of the **unique** construct defined in Section 3.8.4.)

## The check Clause

When applied to a relation declaration, the clause **check**(_P_) specifies a predicate _P_ that must be satisfied by every tuple in a relation.

A common use of the **check** clause is to ensure that attribute values satisfy specified conditions, in effect creating a powerful type system. For instance, a clause **check** (_budget_ \> 0) in the **create table** command for relation _department_ would ensure that the value of **budget** is nonnegative.

As another example, consider the following:

**create table** _section_ 
(_course id_ **varchar** (8), 
_sec id_ **varchar** (8), 
_semester_ **varchar** (6), 
_year_ **numeric** (4,0), 
_building_ **varchar** (15),
_room number_ **varchar** (7), 
_time slot id_ **varchar** (4), **primary key** (_course id_, _sec id_, _semester_, _year_), 
**check** (_semester_ **in** (’Fall’, ’Winter’, ’Spring’, ’Summer’)));

Here, we use the **check** clause to simulate an enumerated type, by specifying that _semester_ must be one of ’Fall’, ’Winter’, ’Spring’, or ’Summer’. Thus, the **check** clause permits attribute domains to be restricted in powerful ways that most programming-language type systems do not permit.

The predicate in the **check** clause can, according to the SQL standard, be an arbitrary predicate that can include a subquery. However, currently none of the widely used database products allows the predicate to contain a subquery.  

## Referential Integrity

Often, we wish to ensure that a value that appears in one relation for a given set of attributes also appears for a certain set of attributes in another relation. This condition is called **referential integrity**.

Foreign keys can be specified as part of the SQL **create table** statement by using the **foreign key** clause, as we saw earlier in Section 3.2.2. We illustrate foreign-key declarations by using the SQL DDL definition of part of our university database, shown in Figure 4.8. The definition of the _course_ table has a declaration “**foreign key** (_dept name_) **references** _department_”. This foreign-key declaration specifies that for each course tuple, the department name specified in the tuple must exist in the _department_ relation. Without this constraint, it is possible for a course to specify a nonexistent department name.

More generally, let _r_~1~ and _r_~2~ be relations whose set of attributes are _r_~1~ and _r_~2~, respectively, with primary keys _K_1 and _K_2\. We say that a subset  of _r_~2~ is a **foreign key** referencing _K_1 in relation _r_~1~ if it is required that, for every tuple _t_~2~  in _r_~2~, there must be a tuple _t_~1~ in _r_~1~ such that _t_~1~_.K_1 = _t_~2~ _._.

Requirements of this form are called **referential-integrity constraints**, or **subset dependencies**. The latter term arises because the preceding referentialintegrity constraint can be stated as a requirement that the set of values on  in _r_~2~ must be a subset of the values on _K_1 in _r_~1~\. Note that, for a referential-integrity constraint to make sense,  and _K_1 must be compatible sets of attributes; that is, either  must be equal to _K_1, or they must contain the same number of attributes, and the types of corresponding attributes must be compatible (we assume here that  and _K_1 are ordered). Unlike foreign-key constraints, in general a referential integrity constraint does not require _K_1 to be a primary key of _r_~1~; as a result, more than one tuple in _r_~1~ can have the same value for attributes _K_1.

By default, in SQL a foreign key references the primary-key attributes of the referenced table. SQL also supports a version of the **references** clause where a list of attributes of the referenced relation can be specified explicitly. The specified list of attributes must, however, be declared as a candidate key of the referenced relation, using either a **primary key** constraint, or a **unique** constraint. A more general form of a referential-integrity constraint, where the referenced columns need not be a candidate key, cannot be directly specified in SQL. The SQL standard specifies other constructs that can be used to implement such constraints; they are described in Section 4.4.7.

We can use the following short form as part of an attribute definition to declare that the attribute forms a foreign key:

_dept name_ **varchar**(20) **references** _department_

When a referential-integrity constraint is violated, the normal procedure is to reject the action that caused the violation (that is, the transaction performing the update action is rolled back). However, a **foreign key** clause can specify that if a delete or update action on the referenced relation violates the constraint, then,  

**create table** _classroom_ 
(_building_ **varchar** (15), 
_room number_ **varchar** (7),
_capacity_ **numeric** (4,0),
**primary key** (_building_, _room number_))

**create table** _department_ 
(_dept name_ **varchar** (20), 
_building_ **varchar** (15) 
_budget_ **numeric** (12,2)**check** (_budget_ \> 0), 
**primary key** (_dept name_))

**create table** _course_ 
(_course id_ **varchar** (8),
_title_ **varchar** (50), 
_dept name_ **varchar** (20),
_credits_ **numeric** (2,0) 
**check** (_credits_ \> 0), 
**primary key** (_course id_), 
**foreign key** (_dept name_) **references** _department_)

**create table** _instructor_ 
(_ID_ **varchar** (5), 
_name_ **varchar** (20), **not null** 
_dept name_ **varchar** (20), 
_salary_ **numeric** (8,2), **check** (_salary_ \> 29000), 
**primary key** (_ID_), 
**foreign key** (_dept name_) **references** _department_)

**create table** _section_ 
(_course id_ **varchar** (8), 
_sec id_ **varchar** (8), 
_semester_ **varchar** (6), **check** (_semester_ **in**
(’Fall’, ’Winter’, ’Spring’, ’Summer’), 
_year_ **numeric** (4,0), **check** (_year_ \> 1759 and _year_ < 2100) 
_building_ **varchar** (15), 
_room number_ **varchar** (7), 
_time slot id_ **varchar** (4), 
**primary key** (_course id_, _sec id_, _semester_, _year_), 
**foreign key** (_course id_) **references** _course_, 
**foreign key** (_building_, _room number_) **references** _classroom_)

**Figure 4.8** SQL data definition for part of the university database.

instead of rejecting the action, the system must take steps to change the tuple in the referencing relation to restore the constraint. Consider this definition of an integrity constraint on the relation _course_:  

**create table** _course_ ( _. . ._
**foreign key** (_dept name_) **references** _department_ 
**on delete cascade** 
**on update cascade**,
_. . ._ );

Because of the clause **on delete cascade** associated with the foreign-key declaration, if a delete of a tuple in _department_ results in this referential-integrity constraint being violated, the system does not reject the delete. Instead, the delete “cascades” to the _course_ relation, deleting the tuple that refers to the department that was deleted. Similarly, the system does not reject an update to a field referenced by the constraint if it violates the constraint; instead, the system updates the field _dept name_ in the referencing tuples in _course_ to the new value as well. SQL also allows the **foreign key** clause to specify actions other than **cascade**, if the constraint is violated: The referencing field (here, _dept name_) can be set to _null_ (by using **set null** in place of **cascade**), or to the default value for the domain (by using **set default**).

If there is a chain of foreign-key dependencies across multiple relations, a deletion or update at one end of the chain can propagate across the entire chain. An interesting case where the **foreign key** constraint on a relation references the same relation appears in Practice Exercises 4.9. If a cascading update or delete causes a constraint violation that cannot be handled by a further cascading operation, the system aborts the transaction. As a result, all the changes caused by the transaction and its cascading actions are undone.

Null values complicate the semantics of referential-integrity constraints in SQL. Attributes of foreign keys are allowed to be _null_, provided that they have not otherwise been declared to be **not null**. If all the columns of a foreign key are nonnull in a given tuple, the usual definition of foreign-key constraints is used for that tuple. If any of the foreign-key columns is _null_, the tuple is defined automatically to satisfy the constraint.

This definition may not always be the right choice, so SQL also provides constructs that allow you to change the behavior with null values; we do not discuss the constructs here.

## Integrity Constraint Violation During a Transaction

Transactions may consist of several steps, and integrity constraints may be violated temporarily after one step, but a later step may remove the violation. For instance, suppose we have a relation _person_ with primary key _name_, and an attribute _spouse_, and suppose that _spouse_ is a foreign key on _person_. That is, the constraint says that the _spouse_ attribute must contain a name that is present in the _person_ table. Suppose we wish to note the fact that John and Mary are married to each other by inserting two tuples, one for John and one for Mary, in the above relation, with the spouse attributes set to Mary and John, respectively. The insertion of the first tuple would violate the foreign-key constraint, regardless of which of the two tuples is inserted first. After the second tuple is inserted the foreign-key constraint would hold again.

To handle such situations, the SQL standard allows a clause **initially deferred** to be added to a constraint specification; the constraint would then be checked at the end of a transaction, and not at intermediate steps. A constraint can alternatively be specified as **deferrable**, which means it is checked immediately by default, but can be deferred when desired. For constraints declared as deferrable, executing a statement **set constraints** _constraint-list_ **deferred** as part of a transaction causes the checking of the specified constraints to be deferred to the end of that transaction.

However, you should be aware that the default behavior is to check constraints immediately, and many database implementations do not support deferred constraint checking.

We can work around the problem in the above example in another way, if the _spouse_ attribute can be set to _null_: We set the spouse attributes to _null_ when inserting the tuples for John and Mary, and we update them later. However, this technique requires more programming effort, and does not work if the attributes cannot be set to _null_.

## Complex Check Conditions and Assertions

The SQL standard supports additional constructs for specifying integrity constraints that are described in this section. However, you should be aware that these constructs are not currently supported by most database systems.

As defined by the SQL standard, the predicate in the **check** clause can be an arbitrary predicate, which can include a subquery. If a database implementation supports subqueries in the **check** clause, we could specify the following referential-integrity constraint on the relation _section_:

**check** (_time slot id_ **in** (**select** _time slot id_ **from** _time slot_))

The **check** condition verifies that the _time slot id_ in each tuple in the _section_ relation is actually the identifier of a time slot in the _time slot_ relation. Thus, the condition has to be checked not only when a tuple is inserted or modified in _section_, but also when the relation _time slot_ changes (in this case, when a tuple is deleted or modified in relation _time slot_).

Another natural constraint on our university schema would be to require that every section has at least one instructor teaching the section. In an attempt to enforce this, we may try to declare that the attributes (_course id_, _sec id_, _semester_, _year_) of the _section_ relation form a foreign key referencing the corresponding attributes of the _teaches_ relation. Unfortunately, these attributes do not form a candidate key of the relation _teaches_. A check constraint similar to that for the _time slot_ attribute can be used to enforce this constraint, if check constraints with subqueries were supported by a database system.

Complex **check** conditions can be useful when we want to ensure integrity of data, but may be costly to test. For example, the predicate in the **check** clause 

**create assertion** _credits earned constraint_ **check** 
(**not exists** (**select** ID
**from** _student_ 
**where** _tot cred <>_ (**select sum**(_credits_) 
**from** _takes_ **natural join** _course_ 
**where** _student_._ID_\= _takes_._ID_
**and** _grade_ **is not null and** _grade<>_ ’F’ )

**Figure 4.9** An assertion example.

would not only have to be evaluated when a modification is made to the _section_ relation, but may have to be checked if a modification is made to the _time slot_ relation because that relation is referenced in the subquery.

An **assertion** is a predicate expressing a condition that we wish the database always to satisfy. Domain constraints and referential-integrity constraints are special forms of assertions. We have paid substantial attention to these forms of assertions because they are easily tested and apply to a wide range of database applications. However, there are many constraints that we cannot express by using only these special forms. Two examples of such constraints are:

• For each tuple in the _student_ relation, the value of the attribute _tot cred_ must equal the sum of credits of courses that the student has completed successfully.

• An instructor cannot teach in two different classrooms in a semester in the same time slot.1

An assertion in SQL takes the form:

**create assertion** _<_assertion-name_\>_ **check** _<_predicate_\>_;

In Figure 4.9, we show how the first example of constraints can be written in SQL. Since SQL does not provide a “for all _X_, _P_(_X_)” construct (where _P_ is a predicate), we are forced to implement the constraint by an equivalent construct, “not exists _X_ such that not _P_(_X_)”, that can be expressed in SQL.

We leave the specification of the second constraint as an exercise. When an assertion is created, the system tests it for validity. If the assertion is valid, then any future modification to the database is allowed only if it does not cause that assertion to be violated. This testing may introduce a significant amount of overhead if complex assertions have been made. Hence, assertions should be used with great care. The high overhead of testing and maintaining assertions has led some system developers to omit support for general assertions, or to provide specialized forms of assertion that are easier to test.

1We assume that lectures are not displayed remotely in a second classroom! An alternative constraint that specifies that “an instructor cannot teach two courses in a given semester in the same time slot” may not hold since courses are sometimes cross-listed; that is, the same course is given two identifiers and titles.  

Currently, none of the widely used database systems supports either subqueries in the **check** clause predicate, or the **create assertion** construct. However, equivalent functionality can be implemented using triggers, which are described in Section 5.3, if they are supported by the database system. Section 5.3 also describes how the referential integrity constraint on _time slot id_ can be implemented using triggers.

