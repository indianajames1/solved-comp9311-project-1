Download Link: https://assignmentchef.com/product/solved-comp9311-project-1
<br>
<strong> </strong>

<h1>1. Aims</h1>

This project aims to give you practice in

<ul>

 <li>reading and understanding a moderately large relational schema (MyMyUNSW)</li>

 <li>implementing SQL queries and views to satisfy requests for information</li>

 <li>implementing SQL functions to aid in satisfying requests for information</li>

</ul>

The goal is to build some useful data access operations on the MyMyUNSW database. A theme of this project is “dirty data”. As I was building the database, using a collection of reports from UNSW’s information systems and the database for the academic proposal system (MAPPS), I discovered that there were some inconsistencies in parts of the data (e.g. duplicate entries in the table for UNSW buildings, or students who were mentioned in the student data, but had no enrolment records, and, worse, enrolment records with marks and grades for students who did not exist in the student data). I removed most of these problems as I discovered them, but no doubt missed some. Some of the exercises below aim to uncover such anomalies; please explore the database and let me know if you find other anomalies.

<ol start="2">

 <li><strong>How to do this project: </strong></li>

</ol>

<ul>

 <li>read this specification carefully and completely</li>

 <li>familiarize yourself with the database <strong>schema</strong> (description, SQL schema,  summary)</li>

 <li>make a private directory for this project, and put a copy of the <strong>sql</strong> template there</li>

 <li>you <strong>must</strong> use the create statements in <strong>sql</strong> when defining your solutions</li>

 <li>look at the expected outputs in the expected_qX tables loaded as part of the <strong>sql</strong> file</li>

 <li>solve each of the problems below, and put your completed solutions into <strong>sql</strong></li>

 <li>check that your solution is correct by verifying against the example outputs and by using the check_qX() functions</li>

 <li>test that your <strong>sql</strong> file will load <em>without error</em> into a database containing just the original</li>

</ul>

MyMyUNSW data

<ul>

 <li>double-check that your <strong>sql</strong> file loads in a <em>single pass</em> into a database containing just the original MyMyUNSW data</li>

 <li>submit the project via give.</li>

</ul>

<h1>3. Introduction</h1>

All Universities require a significant information infrastructure in order to manage their affairs. This typically involves a large commercial DBMS installation. UNSW’s student information system sits behind the MyUNSW web site. MyUNSW provides an interface to a PeopleSoft enterprise management system with an underlying Oracle database. This back-end system (Peoplesoft/Oracle) is often called NSS.

UNSW has spent a considerable amount of money ($80M+) on the MyUNSW/NSS system, and it handles much of the educational administration plausibly well. Most people gripe about the quality of the MyUNSW interface, but the system does allow you to carry out most basic enrolment tasks online.

Despite its successes, however, MyUNSW/NSS still has a number of deficiencies, including:

<ul>

 <li>no waiting lists for course or class enrolment</li>

 <li>no representation for degree program structures</li>

 <li>poor integration with the UNSW Online Handbook</li>

</ul>

The first point is inconvenient, since it means that enrolment into a full course or class becomes a sequence of trial-and-error attempts, hoping that somebody has dropped out just before you attempt to enrol and that no-one else has grabbed the available spot.

The second point prevents MyUNSW/NSS from being used for three important operations that would be extremely helpful to students in managing their enrolment:

<ul>

 <li>finding out how far they have progressed through their degree program, and what remains to be completed</li>

 <li>checking what are their enrolment options for next semester (e.g. get a list of suggested courses)</li>

 <li>determining when they have completed all of the requirements of their degree program and are eligible to graduate</li>

</ul>

NSS contains data about student, courses, classes, pre-requisites, quotas, etc. but does not contain any representation of UNSW’s degree program structures. Without such information in the NSS database, it is not possible to do any of the above three. So, in 2007 the COMP9311 class devised a data model that could represent program requirements and rules for UNSW degrees. This was built on top of an existing schema that represented all of the core NSS data (students, staff, courses, classes, etc.). The enhanced data model was named the MyMyUNSW schema.

The MyMyUNSW database includes information that encompasses the functionality of NSS, the UNSW Online Handbook, and the CATS (room allocation) database. The MyMyUNSW data model, schema and database are described in a separate document.

<h1>4. Setting Up</h1>

To install the MyMyUNSW database under your Grieg server, simply run the following two commands:

$ <strong>createdb proj1</strong>

$ <strong>psql proj1 –f /home/cs9311/web/17s1/proj/proj1/mymyunsw.dump</strong>

If you’ve already set up PLpgSQL in your template1 database, you will get one error message as the database starts to load:

psql:mymyunsw.dump:<em>NN</em>: ERROR:  language “plpgsql” already exists

You can ignore this error message, but any other occurrence of ERROR during the load needs to be investigated.

If everything proceeds correctly, the load output should look something like:

SET

SET

SET

SET SET

psql:mymyunsw.dump:NN: ERROR:  language “plpgsql” already exists

… if PLpgSQL is not already defined,

… the above ERROR will be replaced by CREATE LANGUAGE

SET

SET

SET

CREATE TABLE

CREATE TABLE

… a whole bunch of these

CREATE TABLE

ALTER TABLE

ALTER TABLE

… a whole bunch of these

ALTER TABLE

Apart from possible messages relating to plpgsql, you should get no error messages. The database loading should take less than 60 seconds on Grieg, assuming that Grieg is not under heavy load. (If you leave your project until the last minute, loading the database on Grieg will be considerably slower, thus delaying your work even more. The solution: at least load the database Right Now, even if you don’t start using it for a while.) (Note that the mymyunsw.dump file is 50MB in size; copying it under your home directory or your /srvr directory is not a good idea).

If you have other large databases under your PostgreSQL server on Grieg or you have large files under your /srvr/YOU/ directory, it is possible that you will exhaust your Grieg disk quota. In particular, you will not be able to store two copies of the MyMyUNSW database under your Grieg server. The solution: remove any existing databases before loading your MyMyUNSW database.

If             you         are         running                PostgreSQL         at            home,   the         file proj1.tar.gz contains                 copies   of            the files: mymyunsw.dump, proj1.sql to get you started. You can grab the check.sql separately, once it becomes available. If you copy proj1.tar.gz to your home computer, extract it, and perform commands analogous to the above, you should have a copy of the MyMyUNSW database that you can use at home to do this project.

A useful thing to do initially is to get a feeling for what data is actually there. This may help you understand the schema better, and will make the descriptions of the exercises easier to understand.

Look at the schema. Ask some queries. Do it now.

Examples …

$ <strong>psql proj1</strong>

… PostgreSQL welcome stuff … proj1=# <strong>d</strong>

… look at the schema …

proj1=# <strong>select * from Students;</strong> … look at the Students table …

proj1=# <strong>select p.unswid,p.name from People p join Students s on (p.id=s.id);</strong> … look at the names and UNSW ids of all students … proj1=# <strong>select p.unswid,p.name,s.phone from People p join Staff s on (p.id=s.id);</strong> … look at the names, staff ids, and phone #s of all staff …

proj1=# <strong>select count(*) from Course_Enrolments;</strong> … how many course enrolment records …

proj1=# <strong>select * from dbpop();</strong> … how many records in all tables …

proj1=# <strong>select * from transcript(3197893);</strong> … transcript for student with ID 3197893 … proj1=# … etc. etc. etc.

proj1=# <strong>q </strong>




You will find that some tables (e.g. Books, Requirements, etc.) are currently unpopulated; their contents are not needed for this project. You will also find that there are a number of views and functions defined in the database (e.g. dbpop() and transcript() from above), which may or may not be useful in this project.

<strong>Summary on Getting Started </strong>

To set up your database for this project, run the following commands in the order supplied:

$ <strong>createdb  proj1</strong>

$ <strong>psql  proj1  -f  /home/cs9311/web/17s1/proj/proj1/mymyunsw.dump</strong>  $ <strong>psql  proj1</strong>

… run some checks to make sure the database is ok

$ <strong>mkdir  <em>Project1Directory</em></strong>

… make a working directory for Project 1

$ <strong>cp  /home/cs9311/web/17s1/proj/proj1/proj1.sql  <em>Project1Directory </em></strong>




The only error messages produced by these commands should be those noted above. If you omit any of the steps, then things will not work as planned.

<strong>Notes </strong>

<strong>Read these</strong> before you start on the exercises:

<ul>

 <li>the marks reflect the relative difficulty/length of each question</li>

 <li>use the supplied <strong>sql</strong> template file for your work</li>

 <li>you may define as many additional functions and views as you need, provided that (a) the definitions in proj1.sql are preserved, (b) you follow the requirements in each question on what you are allowed to define</li>

 <li>make sure that your queries would work on any instance of the MyMyUNSW schema; don’t customize them to work just on this database; we may test them on a different database instance</li>

 <li>do not assume that any query will return just a single result; even if it phrased as “most” or</li>

</ul>

“biggest”, there may be two or more equally “big” instances in the database

<ul>

 <li>you are not allowed to use <strong><em>limit </em></strong>in answering any of the queries in this project</li>

 <li>when queries ask for people’s names, use the Person.name field; it’s there precisely to produce displayable names</li>

 <li>when queries ask for student ID, use the People.unswid field; the People.id field is an internal numeric key and of no interest to anyone outside the database</li>

 <li>unless specifically mentioned in the exercise, the order of tuples in the result does not matter; it can always be adjusted using order by. In fact, our check.sql will order your results automatically for comparison.</li>

 <li>the precise formatting of fields within a result tuple <strong>does</strong> matter; e.g. if you convert a number to a string using to_char it may no longer match a numeric field containing the same value, even though the two fields may look similar</li>

 <li>develop queries in stages; make sure that any sub-queries or sub-joins that you’re using actually work correctly before using them in the query for the final view/function</li>

 <li>You can define either SQL views OR SQL functions to answer the following questions.</li>

</ul>

<span style="text-decoration: line-through">An important note related to marking: </span>

<ul>

 <li><span style="text-decoration: line-through">make sure that your queries are reasonably efficient (defined as taking &lt; 30 seconds to run) </span></li>

 <li><span style="text-decoration: line-through">use psql’s timing feature to check how long your queries are taking; they must each take less than 30000 ms </span></li>

 <li><span style="text-decoration: line-through">queries that are too slow will be <strong>penalized by half of the mark for that question</strong>, even if </span><u>they give the correct result </u></li>

</ul>

Each question is presented with a brief description of what’s required. If you want the full details of the expected output, take a look at the expected_qX tables supplied in the checking script.

<h1>5. Tasks</h1>

<strong> </strong>

To facilitate the semi-auto marking, please pack all your SQL solutions into view or function as defined in each problem (see details from the solution template we provided).

<h2>Q1(2 marks)</h2>

Define a SQL view Q1(unswid,name) that gives all the buildings that have more than 30 rooms.

Each tuple in the view should contain the following:

<ul>

 <li>the id of the building (unswid field)</li>

 <li>the name of the building (name field)</li>

</ul>










<h2>Q2 (2 marks)</h2>

Define a SQL view Q2(name,faculty,phone,starting) which gives the details about all of the current Dean of Faculty. Each tuple in the view should contain the following:

<ul>

 <li>his/her name (use the name field from the People table)</li>

 <li>the faculty (use the longname field from the OrgUnits table)</li>

 <li>his/her phone number (use the phone field from Staff table)</li>

 <li>the date when he/she was appointed (use the starting field from the Affiliations</li>

</ul>

table)

Since there is some dirty-looking data in the Affiliations table, you will need to ensure that you return only legitimate Deans of Faculty. Apply the following filters together:

<ul>

 <li>only choose people whose role is exactly ‘Dean’</li>

 <li>only choose organisational units whose type is actually ‘Faculty’</li>

</ul>

<strong>Note: </strong>Every current Dean has null value in the Affiliations.ending field.

<strong> </strong>

<strong> </strong>

<h2>Q3 (2 marks)</h2>

Define a SQL view Q3(status, name, faculty, starting) that finds out among all of

the current Dean of Faculty, the one who has served the longest time and the one who has served the shortest time. Each tuple in the view should contain the following:

<ul>

 <li>status ( ‘Longest serving’ or ‘Shortest serving’, cast to text)</li>

 <li>his/her name (use the name field from the People table)</li>

 <li>the faculty (use the longname field from the OrgUnits table)</li>

 <li>the date that he/she was appointed (use the starting date from the Affiliations table) <strong>Note</strong>: You are allowed to use the SQL view Q2 you created. It is possible that there are more than one longest serving Dean of Faculty because multiple Dean of Faculty might be appointed at the same time. Similarly, it is possible that there are more than one shortest serving Dean of Faculty.</li>

</ul>







<h2>Q4 (2 marks)</h2>

It was claimed that having both uoc and eftsload in the Subjects table was unnecessary, since one could be determined from the other (e.g., uoc/48 == eftsload). We could test this by calculating the ratio of uoc/eftsload for all subjects to see whether they adhere to the uoc/eftsload ratio being constant. Since the eftsload values in data files are truncated to three decimal places, some of the ratios won’t be exact; to allow for this, take only one decimal place in your ratio calculations.

Define an SQL view to produce a list of distinct uoc/eftsload values and the number of subjects which has each value, as follows: create or replace view Q4(ratio, nsubjects) as …

<strong>Note:</strong> use numeric(4,1) to restrict the precision of the ratios, and be careful about typecasting between integers and reals. You can ignore any subjects with eftsload values that are either NULL or zero.







<h2>Q5 (3 marks)</h2>

Define SQL views Q5a(num), Q5b(num) and Q5c(num), which give the number of, respectively

<ul>

 <li>international students enrolled in 10S1 in Software Engineering (SENGA1) stream</li>

 <li>local students enrolled in 10S1 in the Computer Science (3978) degree</li>

 <li>all students enrolled in 10S1 in degrees offered by Faculty of Engineering</li>

</ul>







<h2>Q6 (2 marks)</h2>

On the webpages for class details, UNSW wants to display course names as a combination of course code (e.g. COMP9311) and course name (e.g. Database Systems). Define a SQL function that takes as parameter a UNSW course code and returns text that concatenates code (use the code field from the Subjects table), a single white space and name (use the name field from the Subjects table),  e.g. COMP9311 Database Systems. The function should be defined as follows:

create or replace function Q6(text) returns text







<h2>Q7 (3 marks)</h2>

Database Systems course admins would like to know that for each semester, whether the number of students enrolled in is increasing or decreasing compared with the previous semester. Define a SQL view Q7(year, term, perc_growth) that will help them to monitor the percentage of growth of students enrolled in each semester. Each tuple in the view should contain the following:

l    the year (Semesters.year) of the semester

<h3>         l    the term (Semesters.term)</h3>

l    the fraction (number of students enrolled in this course this semester /number of students enrolled in this course last semester) as numeric(4,2)

Database Systems has value ‘Database Systems’ in the Subjects.name field. You can find the information about all the course offerings for a given subject from Courses. You should compute the number of enrolled students for a course offering from the table Course_enrolments. You should do the fraction calculation using floating point values to achieve the highest precision.

<strong>Note</strong>:

(1). There are two subjects that share the same name “Database Systems”, and we do not distinguish them for the purpose of this question. In consequence, you may find more than one courses for a single semester. In such case, please count students enrolled in them together.

(2). Please do not assume that newer semesters always have Semesters.id greater than older semesters. You should determine which semester comes earlier by comparing

Semesters.starting field. And we define “last semester” as the most recent semester among all the past semesters. The first semester in history do not have its “last semester”. Please do not include it in your results.







<h2>Q8 (3 marks)</h2>

UNSW is welcoming more and more students each year, and as a result is running out of teaching facilities. Therefore, UNSW wants to drop the least popular subjects. Define a SQL view

Q8(subject) that lists the least popular subjects. A subject is one of the least popular subjects if the subject has at least 20 course offerings, and there are less than 20 distinct students enrolled (via the Course_enrolments table) in each of the most recent 20 course offerings of the subject. Each tuple in the view should contain the following:

l    the subject code (Subjects.code field) and name (Subjects.name field), in the format e.g. COMP9311 Database Systems

<strong>Note:</strong> Some course offerings have no students enrolled in. It appears in Courses, but not in Course_enrolments. Excluding such course offerings will result in incorrect results.







<h2>Q9 (2 marks)</h2>

To understand the students’ performance, Database Systems course admins would like to know that for each year, the pass rate for semester 1 and semester 2. Define a SQL view Q9(year, s1_pass_rate, s2_pass_rate) that gives a list of years when courses of Database Systems are offered, along with the pass rate for both semester 1 and semester 2. Each tuple in the view should contain the following:

<ul>

 <li>the year (year field in the format of the last 2 digits (i.e., 17 for 2017))</li>

 <li>semester 1 pass rate (number of students who have mark &gt;= 50 / number of students who have actually received a mark, i.e.</li>

</ul>

<h3>Course_enrolments.mark &gt;= 0) as numeric(4,2)</h3>

l   semester 2 pass rate as numeric(4,2) <strong>Note</strong>:

(1). We only consider the students who actually receive a mark for the course taken. You may use Course_enrolments.mark &gt;= 0 to retrieve a list of valid students.

(2). You may find some of the views you have created for Q7 is helpful for this question, in such case, you may reuse the views you have created for Q7.







<h2>Q10 (4 marks)</h2>

The head of school once heard a rumour that there is a set of CSE subjects that are extremely hard to learn. Subjects in this set have their subject codes starting with “COMP93”, and are offered in every major semester (i.e., S1 and S2) from 2002 (inclusive) to 2013 (inclusive). To understand how hard these subjects are, the head of school requested a list of students who failed every subject in the set. We say a student fails a subject if he/she had received a mark &lt; 50 for any course offering of the subject. Define a SQL view Q10(zid, name) for the head of school. Each tuple in the view should contain the following:

<ul>

 <li>zid (‘z’ concatenate with unswid field)</li>

 <li>student name (taken from the name field) <strong>Note:</strong></li>

</ul>

(1). For a given subject, the number of course offerings a student can enrol in is greater than or equal to one. For example, one may fail the course offering of a subject in 03S1, and then be re-enrolled in another course offering of the same subject in 04S1.

(2). Assume there are two subjects in the set, A and B. We only count students who failed both A and B, but not students who failed either A or B.







Note: if your database contains any views or functions that are not available in a file somewhere, you should put them into a file before you drop the database.

If your code does not load without errors, fix it and repeat the above until it does.

You must ensure that your proj1.sql file will load correctly (i.e. it has no syntax errors and it contains all of your view definitions in the correct order). If I need to manually fix problems with your proj1.sql file in order to test it (e.g. change the order of some definitions), you will be fined via half of the mark penalty for each problem.





