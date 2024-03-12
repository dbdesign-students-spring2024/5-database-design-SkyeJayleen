# Data Normalization and Entity-Relationship Diagramming

## Converting to Fourth Normal Form

### Adding Relevant Fields

First, I added five additional fields, *student_name*, *student_id*, *course_name*, *course_section* and *assignment_type* based on my assumptions that these fields will be relevant and helpful when examining the data.

| assignment_id | student_id | student_name | student_email | course_name | course_section | due_date | professor | assignment_type | assignment_topic | classroom | grade | relevant_reading | professor_email |
| :- | - | - | - | - | - | - | - | - | - | - | - | - | - |
| 1 | 1 | Pauline Vi | pauline@gmail.com | Database Design | 004 | 23.02.21 | Melvin | Data normalization | Quiz 1 | WWH 101 | 80 | Deumlich Chapter 3 | l.melvin@foo.edu |
| 2 | 7 | Anemone Kaiden | anekai@gmail.edu | Computer Programming | 007 | 18.11.21 | Logston | Single table queries | Midterm | 60FA 314  | 25 | Dümmlers Chapter 11 | e.logston@foo.edu |
| 1 | 4 | Earline Solomon | esol@hotmail.com | Database Design | 002 | 23.02.21 | Melvin | Data normalization | Quiz 1 | WWH 101 | 75 | Deumlich Chapter 3 | l.melvin@foo.edu |
| 5 | 2 | Kylan Ora | kyora@email.com | Computer Programming | 004 | 05.05.21 | Logston | Python and pandas | Quiz 2 | 60FA 314 | 92 | Dümmlers Chapter 14 | e.logston@foo.edu |
| 4 | 2 | Gwendolyn Tim | gwen@website.com | Excel Spreadsheets | 005 | 04.07.21 | Nevarez | Spreadsheet aggregate functions | Workshop | WWH 201 | 65 | Zehnder Page 87 | i.nevarez@foo.edu |
| ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

Then, I moved on to normalizing the table.

### First Normal Form

The data is given in a table with a fixed schema, which already satisfies the First Normal Form.

### Second and Third Normal Form

The Second Normal Form states that a non-key field must provide a fact about the entity uniquely identified by a primary key. The original data table does not satisfy the 2NF as it has no primary key identified. So first, I decided to identify the primary key(s).

Based on what I know about the given data and how it's usually handled, I selected the following fields as potential candidates for the primary key(s), as they seemed to serve well as unique identifiers of each objects described in the table:
* student_id
* assignment_id
* course_name
* course_section
* professor_email

I then attempted to categorize all of the fields in the table into these four potential primary keys(marked in **bold**), treating them as entities and the rest of the fields as attributes that each describe the primary keys. I am not drawing the Entity-Relationship Diagram yet, but I found the concept of referring to the primary key as an object being described by other fields helpful in deciding on what the primary key can be.

Information about the student was fairly straightforward:

* **student_id**
    * student_name
    * student_email

However, the other potential primary keys proved a little more difficult as they were closely dependent upon one another. ***assignment*** seemed specific to the combination of the course name and section, while also having its own attributes as well(*due_date*, *grade*, and so on).

I tried thinking of ***course_name*** and ***course_section*** as a composite key. This provides easy connection between the students' info and the rest of the dataset as well.

* **course_name + course_section**
    * professor
    * **professor_email**
    * classroom
    * **assignment_id**
        * assignment_type
        * assignment_topic
        * *due_date*
        * *grade*
        * relevant_readings
    * **student_id**
        * student_name
        * student_email


The fields marked in *italic* introduces some challenges to the process. I was also beginning to think about the Third Normal Form at this point, which does not allow a non-key field to describe another non-key field. The challenges were:

1. *professor_email* has an awkward placement as it is only relevant to *professor* rather than the composite key of ***course_name + course_section***. This can be fixed by creating another table dedicated to the information about each professors.

2. *due_date* is specific to a whopping four things: the professor, course name, course section, and the assignment, so it will require a separate table as well.

3. *grade* is specific to the combination of the student and the assignment. In order to make it comply to the 2NF, it would require a separate table with a composite key combining ***assignment_id*** and ***student_id*** as only then *grade* would be specific to the composite key.

From this exploration, I started building rudimentary skeletons for tables, the primary key marked with an asterik(*):
* Table 1 dedicated to student information:

| student_id* | student_name | student_email |
| :- | - | - |
| ... | ... | ... |

* Table 2 dedicated to professor information:

| professor_email* | professor |
| :- | - |
| ... | ... |

* Table 3 dedicated to the course_name-section specific assignment information:

| course_name* | course_section* | professor | assignment_id | classroom | assignment_type | assignment_topic | relevant_readings |
| :- | - | - | - | - | - | - | - |
| ... | ... | ... | ... | ... | ... | ... | ... |

* Table 4 dedicated to student-assignment specific grade information:

| student_id* | assignment_id* | grade |
| :- | - | - |
| ... | ... | ... |

* Table 5 dedicated to the specific due dates of assignments in specific sections of courses():

| course_name* | course_section* | assignment_id* | due_date |
| :- | - | - | - |
| ... | ... | ... | ... |

These are closer, but Table 3 does not satisfy the 2NF as some fields do not facts about the primary composite key. So I split that table into two:

* Table 3.1 consisting of just the specific course name-section:

| course_name* | course_section* | professor | classroom | assignment_id | 
| :- | - | - | - | - |
| ... | ... | ... | ... | ... |

* And Table 3.2 that describes the specific assignment:

| assignemnt_id* | assignemnt_type | assignment_topic | relevant_readings | 
| :- | - | - | - |
| ... | ... | ... | ... |

I went through all of the individual tables to double-check that they all met the requirements to satisfy 2NF and 3NF. Now let's move on to 4NF.

### Fourth Normal Form

The Fourth Normal Form states that the table must not contain more than one independent multivalued fact about an entity.

I identified the fields in each table that can have two or more multivalued facts.

**Table 1**: One student ID can only be attribute to a single student, who will only have one name(presumably) and one email(that is used in the school, at least). All good.

**Table 2**: Table 2 only has two rows, and is similar with Table 1 that each professor has only one email. All good.

**Table 3.1**: There is only one set professor for a specific combination of course + section. This is also true for classrooms. Multiple assignments can(and likely will) be assigned to a specific course section, but that is only one multivalued fact. Therefore, Table 3.1 also fits the 4NF. All good.

**Table 3.2**: 



Both assignment type(ex: "Quiz 1" in two different courses) and topic(ex: "Quiz 1" and "Workshop 1" being about the same topic) (and arguably relevant readings) can contain multiple values: we can split the table again into three different tables Table 3.2.1, 3.2.2. and 3.2.3 respectively about the assignment type, topic and relevant readings.

| assignemnt_id* | assignemnt_type |
| :- | - |
| ... | ... |

| assignemnt_id* | assignemnt_topic |
| :- | - |
| ... | ... |

| assignemnt_id* | relevant_readings |
| :- | - |
| ... | ... |

**Table 4**: Grade may contain multiple same values, 