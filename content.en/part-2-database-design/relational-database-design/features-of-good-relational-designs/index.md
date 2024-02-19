---
title: 'Features of Good Relational Designs'
weight: 1
references:
    videos:
        - youtube:_0SO1ovwYiM
        - youtube:PYEqUCl3RBk
    links:
        - https://www.salesforce.com/eu/blog/what-is-a-relational-database/
        - https://web.mit.edu/11.521/www/lectures/lecture10/lec_data_design.html
    books:
        - b1:
            title: Relational Database Design and Implementation
            url: https://www.google.co.in/books/edition/Relational_Database_Design_and_Implement/O3Qdc2yb_JoC?hl=en&gbpv=0
        - b2:
            title: Relational Database Design
            url: https://www.google.co.in/books/edition/Relational_Database_Design_Clearly_Expla/TwtA-uouTQkC?hl=en&gbpv=0
---

# Features of Good Relational Designs

Our study of entity-relationship design in Chapter 7 provides an excellent starting point for creating a relational database design. We saw in Section 7.6 that it is possible to generate a set of relation schemas directly from the E-R design. Obviously, the goodness (or badness) of the resulting set of schemas depends on how good the E-R design was in the first place. Later in this chapter, we shall study precise ways of assessing the desirability of a collection of relation schemas. However, we can go a long way toward a good design using concepts we have already studied.

For ease of reference, we repeat the schemas for the university database in Figure 8.1.
classroom(building, room number,capacity)
department(dept name, building,budget)
course(course id, title, deptname, credits)
instructor(ID, name, dept name,salary)
section(course id, sec id,semester, year, building, roomnumber, time slot id)
teaches(ID, course id, sec id,semester, year)
student(ID, name, dept name, totcred)
takes(ID, course id, sec id,semester, year, grade)
advisor(s ID, i ID)
time slot(time slot id, day, start time, end time)
prereq(course id, prereq id)
**Figure 8.1** Schema for the university database.

**8.1.1 Design Alternative: Larger Schemas**

Now, let us explore features of this relational database design as well as some alternatives. Suppose that instead of having the schemas _instructor_ and _department_, we have the schema:

_inst dept_ (_ID_, _name_, _salary_, _dept name_, _building_, _budget_)

This represents the result of a natural join on the relations corresponding to _instructor_ and _department_. This seems like a good idea because some queries can be expressed using fewer joins, until we think carefully about the facts about the university that led to our E-R design.

Let us consider the instance of the _inst dept_ relation shown in Figure 8.2. Notice that we have to repeat the department information (“building” and “budget”) once for each instructor in the department. For example, the information about the Comp. Sci. department (Taylor, 100000) is included in the tuples of instructors Katz, Srinivasan, and Brandt.

It is important that all these tuples agree as to the budget amount since other- wise our database would be inconsistent. In our original design using _instructor_ and _department_, we stored the amount of each budget exactly once. This suggests that using _inst dept_ is a bad idea since it stores the budget amounts redundantly and runs the risk that some user might update the budget amount in one tuple but not all, and thus create inconsistency.

Even if we decided to live with the redundancy problem, there is still another problem with the _inst dept_ schema. Suppose we are creating a new department in the university. In the alternative design above, we cannot represent directly the information concerning a department (_dept name_, _building_, _budget_) unless that department has at least one instructor at the university. This is because tuples in the _inst dept_ table require values for _ID_, _name_, and _salary_. This means that we cannot record information about the newly created department until the first instructor 

![Alt text](image-28.png)

**Figure 8.2** The _inst dept_ table.

is hired for the new department. In the old design, the schema _department_ can handle this, but under the revised design, we would have to create a tuple with a null value for _building_ and _budget_. In some cases null values are troublesome, as we saw in our study of SQL. However, if we decide that this is not a problem to us in this case, then we can proceed to use the revised design.

## Design Alternative: Smaller Schemas

Suppose again that, somehow, we had started out with the schema _inst dept_. How would we recognize that it requires repetition of information and should be split into the two schemas _instructor_ and _department_?

By observing the contents of actual relations on schema _inst dept_, we could note the repetition of information resulting from having to list the building and budget once for each instructor associated with a department. However, this is an unreliable process. A real-world database has a large number of schemas and an even larger number of attributes. The number of tuples can be in the millions or higher. Discovering repetition would be costly. There is an even more fundamental problem with this approach. It does not allow us to determine whether the lack of repetition is just a “lucky” special case or whether it is a manifestation of a general rule. In our example, how would we _know_ that in our university organization, each department (identified by its department name) _must_ reside in a single building and must have a single budget amount? Is the fact that the budget amount for the Comp. Sci. department appears three times with the same budget amount just a coincidence? We cannot answer these questions without going back to the enterprise itself and understanding its rules. In particular, we would need to discover that the university requires that every department (identified by its department name) _must_ have only one building and one budget value.

In the case of _inst dept_, our process of creating an E-R design successfully avoided the creation of this schema. However, this fortuitous situation does not always occur. Therefore, we need to allow the database designer to specify rules such as “each specific value for _dept name_ corresponds to at most one _budget_” even in cases where _dept name_ is not the primary key for the schema in question. In other words, we need to write a rule that says “if there were a schema (_dept name_, _budget_), then _dept name_ is able to serve as the primary key.” This rule is specified as a **functional dependency**

_dept name_ → _budget_

Given such a rule, we now have sufficient information to recognize the problem of the _inst dept_ schema. Because _dept name_ cannot be the primary key for _inst dept_ (because a department may need several tuples in the relation on schema _inst dept_), the amount of a budget may have to be repeated.

Observations such as these and the rules (functional dependencies in partic- ular) that result from them allow the database designer to recognize situations where a schema ought to be split, or _decomposed_, into two or more schemas. It is not hard to see that the right way to decompose _inst dept_ is into schemas _instruc- tor_ and _department_ as in the original design. Finding the right decomposition is much harder for schemas with a large number of attributes and several functional dependencies. To deal with this, we shall rely on a formal methodology that we develop later in this chapter.

Not all decompositions of schemas are helpful. Consider an extreme case where all we had were schemas consisting of one attribute. No interesting rela- tionships of any kind could be expressed. Now consider a less extreme case where we choose to decompose the _employee_ schema (Section 7.8):

_employee_ (_ID_, _name_, _street_, _city_, _salary_)

into the following two schemas:

_employee1_ (_ID_, _name_) 
_employee2_ (_name_, _street_, _city_, _salary_)

The flaw in this decomposition arises from the possibility that the enterprise has two employees with the same name. This is not unlikely in practice, as many cultures have certain highly popular names. Of course each person would have a unique employee-id, which is why _ID_ can serve as the primary key. As an example, let us assume two employees, both named Kim, work at the university and have the following tuples in the relation on schema _employee_ in the original design:

(57766, Kim, Main, Perryridge, 75000) 
(98776, Kim, North, Hampton,67000)  

![Alt text](image-29.png)

Figure 8.3 shows these tuples, the resulting tuples using the schemas resulting from the decomposition, and the result if we attempted to regenerate the origi- nal tuples using a natural join. As we see in the figure, the two original tuples appear in the result along with two new tuples that incorrectly mix data values pertaining to the two employees named Kim. Although we have more tuples, we actually have less information in the following sense. We can indicate that a certain street, city, and salary pertain to someone named Kim, but we are unable to distinguish which of the Kims. Thus, our decomposition is unable to represent certain important facts about the university employees. Clearly, we would like to avoid such decompositions. We shall refer to such decompositions as being **lossy decompositions**, and, conversely, to those that are not as **lossless decom- positions**.

