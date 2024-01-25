---
title: 'Authorization'
weight: 6
---

# 4.6 Authorization

We may assign a user several forms of authorizations on parts of the database. Authorizations on data include:

• Authorization to read data.

• Authorization to insert new data.

• Authorization to update data.

• Authorization to delete data.

Each of these types of authorizations is called a **privilege**. We may authorize the user all, none, or a combination of these types of privileges on specified parts of a database, such as a relation or a view.

When a user submits a query or an update, the SQL implementation first checks if the query or update is authorized, based on the authorizations that the user has been granted. If the query or update is not authorized, it is rejected.

In addition to authorizations on data, users may also be granted authorizations on the database schema, allowing them, for example, to create, modify, or drop relations. A user who has some form of authorization may be allowed to pass on (grant) this authorization to other users, or to withdraw (revoke) an authorization that was granted earlier. In this section, we see how each of these authorizations can be specified in SQL.

The ultimate form of authority is that given to the database administrator. The database administrator may authorize new users, restructure the database, and so on. This form of authorization is analogous to that of a **superuser**, administrator, or operator for an operating system.

## Granting and Revoking of Privileges

The SQL standard includes the privileges **select**, **insert**, **update**, and **delete**. The privilege **all privileges** can be used as a short form for all the allowable privileges. A user who creates a new relation is given all privileges on that relation automatically.

The SQL data-definition language includes commands to grant and revoke privileges. The **grant** statement is used to confer authorization. The basic form of this statement is:

**grant** _<_privilege list_\>_
**on** _<_relation name or view name_\>_
**to** _<_user/role list_\>_;

The _privilege list_ allows the granting of several privileges in one command. The notion of roles is covered later, in Section 4.6.2.

The **select** authorization on a relation is required to read tuples in the relation. The following **grant** statement grants database users Amit and Satoshi **select** authorization on the _department_ relation:

**grant select on** _department_ **to** _Amit_, _Satoshi_;

This allows those users to run queries on the _department_ relation. The **update** authorization on a relation allows a user to update any tuple

in the relation. The **update** authorization may be given either on all attributes of the relation or on only some. If **update** authorization is included in a **grant** statement, the list of attributes on which update authorization is to be granted optionally appears in parentheses immediately after the **update** keyword. If the list of attributes is omitted, the update privilege will be granted on all attributes of the relation.

This **grant** statement gives users Amit and Satoshi update authorization on the _budget_ attribute of the _department_ relation:

**grant update** (_budget_) **on** _department_ **to** _Amit_, _Satoshi_;

The **insert** authorization on a relation allows a user to insert tuples into the relation. The **insert** privilege may also specify a list of attributes; any inserts to the relation must specify only these attributes, and the system either gives each of the remaining attributes default values (if a default is defined for the attribute) or sets them to _null_.

The **delete** authorization on a relation allows a user to delete tuples from a relation.

The user name **public** refers to all current and future users of the system. Thus, privileges granted to **public** are implicitly granted to all current and future users.

By default, a user/role that is granted a privilege is not authorized to grant that privilege to another user/role. SQL allows a privilege grant to specify that the recipient may further grant the privilege to another user. We describe this feature in more detail in Section 4.6.5.  

It is worth noting that the SQL authorization mechanism grants privileges on an entire relation, or on specified attributes of a relation. However, it does not permit authorizations on specific tuples of a relation.

To revoke an authorization, we use the **revoke** statement. It takes a form almost identical to that of **grant**:

**revoke** _<_privilege list_\>_ 
**on** _<_relation name or view name_\>_
**from** _<_user/role list_\>_;

Thus, to revoke the privileges that we granted previously, we write

**revoke select on** _department_ **from** _Amit_, _Satoshi_; 
**revoke update** (budget) **on** _department_ **from** _Amit_, _Satoshi_;

Revocation of privileges is more complex if the user from whom the privilege is revoked has granted the privilege to another user. We return to this issue in Section 4.6.5.

## Roles

Consider the real-world roles of various people in a university. Each instructor must have the same types of authorizations on the same set of relations. Whenever a new instructor is appointed, she will have to be given all these authorizations individually.

A better approach would be to specify the authorizations that every instructor is to be given, and to identify separately which database users are instructors. The system can use these two pieces of information to determine the authorizations of each instructor. When a new instructor is hired, a user identifier must be allocated to him, and he must be identified as an instructor. Individual permissions given to instructors need not be specified again.

The notion of **roles** captures this concept. A set of roles is created in the database. Authorizations can be granted to roles, in exactly the same fashion as they are granted to individual users. Each database user is granted a set of roles (which may be empty) that she is authorized to perform.

In our university database, examples of roles could include _instructor_, _teaching assistant_, _student_, _dean_, and _department chair_.

A less preferable alternative would be to create an _instructor_ userid, and permit each instructor to connect to the database using the _instructor_ userid. The problem with this approach is that it would not be possible to identify exactly which instructor carried out a database update, leading to security risks. The use of roles has the benefit of requiring users to connect to the database with their own userid.

Any authorization that can be granted to a user can be granted to a role. Roles are granted to users just as authorizations are.  

Roles can be created in SQL as follows:

**create role** _instructor_;

Roles can then be granted privileges just as the users can, as illustrated in this statement:

**grant select on** _takes_ 
**to** _instructor_;

Roles can be granted to users, as well as to other roles, as these statements show:

**grant** _dean_ **to** Amit; 
**create role** _dean_; 
**grant** _instructor_ **to** _dean_; 
**grant** _dean_ **to** Satoshi;

Thus the privileges of a user or a role consist of:

• All privileges directly granted to the user/role.

• All privileges granted to roles that have been granted to the user/role.

Note that there can be a chain of roles; for example, the role _teaching assistant_ may be granted to all _instructors_. In turn the role _instructor_ is granted to all _dean_s. Thus, the _dean_ role inherits all privileges granted to the roles _instructor_ and to _teaching assistant_ in addition to privileges granted directly to _dean_.

When a user logs in to the database system, the actions executed by the user during that session have all the privileges granted directly to the user, as well as all privileges granted to roles that are granted (directly or indirectly via other roles) to that user. Thus, if a user Amit has been granted the role _dean_, user Amit holds all privileges granted directly to Amit, as well as privileges granted to _dean_, plus privileges granted to _instructor,_ and _teaching assistant_ if, as above, those roles were granted (directly or indirectly) to the role _dean_.

It is worth noting that the concept of role-based authorization is not specific to SQL, and role-based authorization is used for access control in a wide variety of shared applications.

## Authorization on Views

In our university example, consider a staff member who needs to know the salaries of all faculty in a particular department, say the Geology department. This staff member is not authorized to see information regarding faculty in other departments. Thus, the staff member must be denied direct access to the _instructor_ relation. But, if he is to have access to the information for the Geology department, he might be granted access to a view that we shall call _geo instructor_, consisting of only those _instructor_ tuples pertaining to the Geology department. This view can be defined in SQL as follows:

**create view** _geo instructor_ **as** 
(**select** \* 
**from** _instructor_ 
**where** _dept name_ \= ’Geology’);

Suppose that the staff member issues the following SQL query:

**select** \* 
**from** _geo instructor_;

Clearly, the staff member is authorized to see the result of this query. However, when the query processor translates it into a query on the actual relations in the database, it produces a query on _instructor_. Thus, the system must check authorization on the clerk’s query before it begins query processing.

A user who creates a view does not necessarily receive all privileges on that view. She receives only those privileges that provide no additional authorization beyond those that she already had. For example, a user who creates a view cannot be given **update** authorization on a view without having **update** authorization on the relations used to define the view. If a user creates a view on which no authorization can be granted, the system will deny the view creation request. In our _geo instructor_ view example, the creator of the view must have **select** authorization on the _instructor_ relation.

As we will see later, in Section 5.2, SQL supports the creation of functions and procedures, which may in turn contain queries and updates. The **execute** privilege can be granted on a function or procedure, enabling a user to execute the function/procedure. By default, just like views, functions and procedures have all the privileges that the creator of the function or procedure had. In effect, the function or procedure runs as if it were invoked by the user who created the function.

Although this behavior is appropriate in many situations, it is not always appropriate. Starting with SQL:2003, if the function definition has an extra clause **sql security invoker**, then it is executed under the privileges of the user who invokes the function, rather than the privileges of the **definer** of the function. This allows the creation of libraries of functions that can run under the same authorization as the invoker.

## Authorizations on Schema

The SQL standard specifies a primitive authorization mechanism for the database schema: Only the owner of the schema can carry out any modification to the schema, such as creating or deleting relations, adding or dropping attributes of relations, and adding or dropping indices.  

However, SQL includes a **references** privilege that permits a user to declare foreign keys when creating relations. The SQL **references** privilege is granted on specific attributes in a manner like that for the **update** privilege. The following **grant** statement allows user Mariano to create relations that reference the key _branch name_ of the _branch_ relation as a foreign key:

**grant references** (_dept name_) **on** _department_ **to** Mariano;

Initially, it may appear that there is no reason ever to prevent users from creating foreign keys referencing another relation. However, recall that foreignkey constraints restrict deletion and update operations on the referenced relation. Suppose Mariano creates a foreign key in a relation _r_ referencing the _dept name_ attribute of the _department_ relation and then inserts a tuple into _r_ pertaining to the Geology department. It is no longer possible to delete the Geology department from the _department_ relation without also modifying relation _r_ . Thus, the definition of a foreign key by Mariano restricts future activity by other users; therefore, there is a need for the **references** privilege.

Continuing to use the example of the _department_ relation, the references privilege on _department_ is also required to create a **check** constraint on a relation _r_ if the constraint has a subquery referencing _department_. This is reasonable for the same reason as the one we gave for foreign-key constraints; a check constraint that references a relation limits potential updates to that relation.

## Transfer of Privileges

A user who has been granted some form of authorization may be allowed to pass on this authorization to other users. By default, a user/role that is granted a privilege is not authorized to grant that privilege to another user/role. If we wish to grant a privilege and to allow the recipient to pass the privilege on to other users, we append the **with grant option** clause to the appropriate **grant** command. For example, if we wish to allow Amit the **select** privilege on _department_ and allow Amit to grant this privilege to others, we write:

**grant select on** _department_ **to** Amit **with grant option**;

The creator of an object (relation/view/role) holds all privileges on the object, including the privilege to grant privileges to others.

Consider, as an example, the granting of update authorization on the _teaches_ relation of the university database. Assume that, initially, the database administrator grants update authorization on _teaches_ to users _U_1, _U_2, and _U_3, who may in turn pass on this authorization to other users. The passing of a specific authorization from one user to another can be represented by an **authorization graph**. The nodes of this graph are the users.

Consider the graph for update authorization on _teaches_. The graph includes an edge _Ui_ → _Uj_ if user _Ui_ grants update authorization on _teaches_ to _Uj_ . The root of the graph is the database administrator. In the sample graph in Figure 4.10,  

![Alt text](image-36.png)

**Figure 4.10** Authorization-grant graph (_U_1_,U_2_, . . . , U_5 are users and DBA refers to the database administrator).

observe that user _U_5 is granted authorization by both _U_1 and _U_2; _U_4 is granted authorization by only _U_1.

A user has an authorization _if and only if_ there is a path from the root of the authorization graph (the node representing the database administrator) down to the node representing the user.

## Revoking of Privileges

Suppose that the database administrator decides to revoke the authorization of user _U_1\. Since _U_4 has authorization from _U_1, that authorization should be revoked as well. However, _U_5 was granted authorization by both _U_1 and _U_2\. Since the database administrator did not revoke update authorization on _teaches_ from _U_2, _U_5 retains update authorization on _teaches_. If _U_2 eventually revokes authorization from _U_5, then _U_5 loses the authorization.

A pair of devious users might attempt to defeat the rules for revocation of authorization by granting authorization to each other. For example, if _U_2 is initially granted an authorization by the database administrator, and _U_2 further grants it to _U_3\. Suppose _U_3 now grants the privilege back to _U_2\. If the database administrator revokes authorization from _U_2, it might appear that _U_2 retains authorization through _U_3\. However, note that once the administrator revokes authorization from _U_2, there is no path in the authorization graph from the root to either _U_2 or to _U_3\. Thus, SQL ensures that the authorization is revoked from both the users.

As we just saw, revocation of a privilege from a user/role may cause other users/roles also to lose that privilege. This behavior is called _cascading revocation_. In most database systems, cascading is the default behavior. However, the **revoke** statement may specify **restrict** in order to prevent cascading revocation:

**revoke select on** _department_ **from** Amit, Satoshi **restrict**;  

In this case, the system returns an error if there are any cascading revocations, and does not carry out the revoke action.

The keyword **cascade** can be used instead of **restrict** to indicate that revocation should cascade; however, it can be omitted, as we have done in the preceding examples, since it is the default behavior.

The following **revoke** statement revokes only the grant option, rather than the actual **select** privilege:

**revoke grant option for select on** _department_ **from** Amit;

Note that some database implementations do not support the above syntax; instead, the privilege itself can be revoked, and then granted again without the grant option.

Cascading revocation is inappropriate in many situations. Suppose Satoshi has the role of _dean_, grants _instructor_ to Amit, and later the role _dean_ is revoked from Satoshi (perhaps because Satoshi leaves the university); Amit continues to be employed on the faculty, and should retain the _instructor_ role.

To deal with the above situation, SQL permits a privilege to be granted by a role rather than by a user. SQL has a notion of the current role associated with a session. By default, the current role associated with a session is null (except in some special cases). The current role associated with a session can be set by executing **set role** _role name_. The specified role must have been granted to the user, else the **set role** statement fails.

To grant a privilege with the grantor set to the current role associated with a session, we can add the clause:

**granted by current role**

to the grant statement, provided the current role is not null. Suppose the granting of the role _instructor_ (or other privileges) to Amit is

done using the **granted by current role** clause, with the current role set to _dean_), instead of the grantor being the user Satoshi. Then, revoking of roles/privileges (including the role _dean_) from Satoshi will not result in revoking of privileges that had the grantor set to the role _dean_, even if Satoshi was the user who executed the grant; thus, Amit would retain the _instructor_ role even after Satoshi’s privileges are revoked.
