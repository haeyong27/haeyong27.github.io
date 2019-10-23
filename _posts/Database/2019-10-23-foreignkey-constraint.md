---
layout: post
title:  "Foreignkey Constraint"
date:   2019-10-23
categories: Database
tag: [Database]
---

# foreign key constraint
[SQL FOREIGN KEY Constraint](https://www.w3schools.com/sql/sql_foreignkey.asp)

> A FOREIGN KEY is a key used to link two tables together. A FOREIGN KEY is a field (or collection of fields) in one table that refers to the PRIMARY KEY in another table.

FK는 두 테이블을 연결한다. FK는 다른 테이블의 PK를 참조한다. 

> The table containing the foreign key is called the child table, and the table containing the candidate key is called the referenced or parent table.

FK가 있는 테이블은 child table, candidate key를 가지고 있는 테이블은 parent table이라고 한다. 

> The FOREIGN KEY constraint also prevents invalid data from being inserted into the foreign key column, because it has to be one of the values contained in the table it points to.

foreign key constraint는 유효하지 않은 데이터가 foreign key column에 삽입되는것을 방지합니다. 삽입하려는 값이 FK가 가리키고 있는 테이블에 있는 값들 중 하나여야 하기 때문입니다.  
