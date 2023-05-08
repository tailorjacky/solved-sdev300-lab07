Download Link: https://assignmentchef.com/product/solved-sdev300-lab07
<br>
<strong>WAMP Apps </strong>




<strong>Overview </strong>

This lab walks you through using Windows, Apache, MySQL and PHP (WAMP) to create simple, yet very powerful PHP applications connected to a MySQL database. For developers using Linux, the acronym becomes LAMP (Windows (W) is replaced by Linux (L)).  The basics of inserting, updating, deleting and selecting from MySQL using PHP forms will be provided. Some “bad” security practices that lead to SQL injection vulnerabilities will be exposed as well as some techniques to mitigate these issues. Injection, and particularly, SQL injection continues to plague web applications that use any programming language and a database. Caution and best practices must be used starting in the initial design of an application to eliminate these significant software vulnerabilities.

<strong>Learning Outcomes: </strong>

At the completion of the lab you should be able to:

<ol>

 <li>Create least privilege MySQL accounts for the Web connection</li>

 <li>Insert data into a MySQL database using PHP forms</li>

 <li>Query existing data in a MySQL database using PHP forms</li>

 <li>Delete data from a MySQL database using PHP forms</li>

 <li>Update data in a MySQL database using PHP forms</li>

 <li>Identify and use best practices to eliminate SQL injection</li>

</ol>

<strong> </strong>

<strong>Lab Submission Requirements: </strong>

After completing this lab, you will submit a word (or PDF) document that meets all of the requirements in the description at the end of this document. In addition, your associated files should be submitted. You should submit multiple files in a zip file.

<strong> </strong>

<strong>Virtual Machine Account Information </strong>

Your Virtual Machine has been preconfigured with all of the software you will need for this class. You have connected to this machine in the previous labs. Reconnect again using the Remote Desktop connection, your Administrator username and password.




We will first use a technique very susceptible to SQL injection and then a better approach using prepared statements. Note, the first technique is what <strong>NOT</strong> to do. It is provided so you can easily identify this issue in future code.

In addition, when connecting to a database through a Web interface, the connection credentials should never be from the root account. If your Web connection becomes compromised and the account had root privileges to your database, the hacker now has root privileges to the database as well. The <strong>least privilege</strong> rule should always be adhered to when assigning user privileges.

To make sure the root account is not used for web connections, we need to create a couple of new

MySQL users and a new database. One user will be the owner of the new database and have most privileges. The other user will be a restricted user with access to only the tables and the privileges for those tables that are needed. Other courses (e.g. SDEV 350) discuss database roles and privileges in more depth. For this course, just a few statements will be used so that you can follow and model for additional activities for this lab.




<ol>

 <li>Assuming you have already launched and logged into your SDEV AMI from your remote desktop, As shown in figure 1, open your command prompt and change to the BitnamiWampStack7.1.16-0mysqlbin directory and enter the following command:</li>

</ol>

mysql –u root –p

When prompted enter the following mysql root password:

sdev300vm99







<em>Figure 1 Launch a  MySQL connection </em>







<ol start="2">

 <li>To display the available databases type the following at the mysql prompt:</li>

</ol>

show databases;

The following SQL statements can be run at the MySQL command prompt to create a database named wamp along with a privileged and non-privileged user. We will be adding more grant statements as additional functionality is required for the non-privileged user.

create database wamp;

— create a user called wamp_owner for the host machine

— Grant all privileges to this user

— select password(‘wamp4umuc’) Note: Remove this!

— Need to grant *.* or would not have the FILE and other global permissions

GRANT ALL PRIVILEGES ON *.* TO <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="780f19150827170f161d0a3814171b191410170b0c">[email protected]</a> IDENTIFIED BY PASSWORD ‘*D7168872D493E342B59B692FF863431330897967’;




— select password(‘user4wamp’) Note: Remove this!

GRANT SELECT ON wamp.students TO <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="d6a1b7bba689a3a5b3a496bab9b5b7babeb9a5a2">[email protected]</a> IDENTIFIED BY PASSWORD

‘*E090926B474658359CB983C65B768047DF3A55BE’;




Several comments are warranted to better understand these SQL statements:

<ol>

 <li>These statements should be run as the root user. The root user has all privileges and will be able to create databases and users.</li>

 <li>You can create any database with the statement create database databasename;</li>

 <li>Privileges are grants to specific database and tables using the database.tablename syntax. For example students grants privileges to the students table. Using wamp.* grants the privilege to all tables in the wamp database.</li>

 <li>Common privileges to grant include SELECT, INSERT, UPDATE and DELETE. Only grant what is needed.</li>

 <li>Privileges are granted to a user at a specific host (or machine). Since we are running on the localhost (which by the way is <strong>not </strong>a recommended best practice), the <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="7601171b062903051304361a1915171a1e190502">[email protected]</a> will work. However; if the database is on another machine, you would use that host or IP name.</li>

</ol>

For example, <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="34435559446b4147514674050d061a051a051a05061a">[email protected]</a>

<ol>

 <li>The passwords are encrypted using the select password (”) For example, typing select password (‘user4wamp’) will return the encrypted version displayed above.</li>

</ol>

Figure 2 shows running of the script listed above to create the database and users on the SDEV AMI.




<em>Figure 2 Create a new database and users </em>

Now, that the new database and users have been created, you should connect as the wamp_owner to create the tables and connect as the wamp_user to test the connectivity for the non-privileged user.

For example, to connect at the wamp_owner, (assuming the MySQL console is not running), enter the following statement

mysql –u wamp_owner –p

When prompted enter the wamp4umuc password as shown in figure 3.

Recall you can type “exit” to exit the root session so you can login back in as the wamp_owner.




<em>Figure 3 Logging in as the wamp_owner </em>




<ol start="3">

 <li>To use the newly created wamp database, type use wamp; followed by show tables; as shown in figure 4.</li>

</ol>




You may already have some tables in your database. If so, the names of those tables would be displayed. If not, you would see Empty set.




<em>Figure 4 show the tables in the wamp database </em>

<ol start="4">

 <li>Create a Students table in the wamp database, if one does not already exist:</li>

</ol>

— Create a student table

CREATE TABLE Students (

PSUsername varchar(30) primary key,

FirstName varchar(30),

LastName varchar(30),

EMail varchar(60) );




If you already have a Students table and it doesn’t match this one, you can delete the current one and then add the new one. Recall to delete a table you type:

drop table table_name;

Substitute the name of the table you want to drop with table_name. For example:

drop table Students;




Figure 5 shows the results of creating the Students table in the wamp database.




<em>Figure 5 Creating the Students table </em>




<ol start="5">

 <li>Next, we will insert a couple of records into the Students table from the MySQL command shell. These records will be used to demonstrate we are actually correctly connecting with the MySQL database table later in this lab.</li>

</ol>

To insert a two records into the students table, copy and paste the following insert statements and run them at the MySQL command shell. Be sure you are still connected to MySQL and are using the wamp database. (See figure 6.)

insert into Students values (‘mjones14’, ‘Mary’, ‘Jones’,’<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="92fff3e0ebbcf8fdfcf7e1d2e1e6e7f6f7fce6bce7ffe7f1bcf7f6e7">[email protected]</a>’);

insert into Students values (‘gsmith294’, ‘George’, ‘Smith’,’<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="81e6e4eef3e6e4aff2ece8f5e9c1f2f5f4e5e4eff5aff4ecf4e2afe4e5f4">[email protected]</a>’);







<em>Figure 6 Insert Records into the Students table </em>




<ol start="6">

 <li>Next we will create the PHP code that will display the data in the Students table.</li>

</ol>

Several files are part of this project including StudentApp.html, SelectStudent.php, DBClasses.php, DBConnect.php, DBQueries.php, and Utils.php. These files are found in the wamp.zip attachment. Copy the file to the SDEV AMI, unzip and place the wamp folder into your htdocs folder. You should review and tinker with all aspects of the code to become comfortable with the functionality.

The location and overall function of each of the files is as follows:

<ol>

 <li>html – Landing page for the application allowing a user to select from one of many options. Location : wamp/</li>

 <li>php – PHP script providing display of all records in the Students table. Location: wamp/</li>

 <li>php – PHP script supporting the Students and Parameters objects. Location: wamp/Includes</li>

 <li>php – PHP script providing connection to the database. Location: wamp/Includes</li>

 <li>php – PHP script providing the SQL query functions. Location: wamp/Includes</li>

 <li>php – PHP script providing file utility and other needed functions. Location: wamp/Includes</li>

</ol>

As we add on the additional functionality, each PHP file will contain similar functionality yet specific to the database query. For example, when we add the Insert functionality, the Insert SQL functions will be placed in the DBQueries.php file. If we need to add additional classes, they would be added to the DBClasses.php file. This design allows for queries and similar functionality to be easily located for debugging and documentation.

Also, notice the use of the require_once() PHP code.  For example,

require_once(‘Includes/DBConnect.php’);        in the DBQueries.php file allows access to the functions found in the DBConnect.php file. This is useful for code reuse but can be tricky as you need to make sure you include the correct and all files needed.

Once unzipped, and placed correctly, you will see the wamp folder in your htdocs folder as shown in figure 7.




<em>Figure 7 The wamp application resides in the htdocs folder </em>

Verify you have the correct folder structure or your application will not run as expected. The wamp folder should be directly under the  htdocs. As shown in figure, if you click on the wamp folder you should see 2 folders: Includes and parms and two files: StudentApp.html and SelectStudent.php. We will be adding to these folders throughout this lab. But for now, that is the structure and the contents.







<em>Figure 8 Initial Contents of the wamp folder </em>




To run the application, open the Firefox browser on your SDEV AMI and type in localhost/wamp/StudentApp.html

As shown in figures 8, the html page will provide several options for PHP links. Note at this point, only the DisplayStudent.php is functional.




<em>Figure 9 StudentApp Landing Page </em>

Clicking on the Display Students link will show the two students in our database. (See figure 10).




<em>Figure 10 Student Table Contents Display </em>

If you are receiving a database connectivity error message, you probably didn’t set-up the database properly. Go back to Create database steps shown on page to verify each of those steps were performed.




<ol start="7">

 <li>Next, we will create the PHP code that will provide a form and response for entering data into the database table.<strong> Note, this code has SQL injection issues and should not be used as a model for any programming project.</strong> We will correct the code soon, but it is important to see what incorrect, or unsafe code looks like.</li>

</ol>




To make our code more efficient, we made several additions to the other files to allow for the additional SQL functions while still using the existing classes and database connectivity.

In addition, we have to update the MySQL database permissions to allow the wamp_user to be able to Insert data into the Students table. <strong>Without this permission, the user will not be able to update the table through the Web Interface. </strong>

The SQL statement granting this permission must be run as the root user at the MySQL command prompt:

GRANT INSERT ON wamp.Students TO <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="45322428351a3036203705292a2624292d2a3631">[email protected]</a> IDENTIFIED BY PASSWORD ‘*E090926B474658359CB983C65B768047DF3A55BE’;

The following functions were added to the DBQueries.php file:

function insertStudent ($student)

{




// Connect to the database

$mysqli = connectdb();




$firstname = $student-&gt;getFirstname();

$lastname = $student-&gt;getLastname();

$wsname = $student-&gt;getPsusername();

$email = $student-&gt;getEmail();




// Now we can insert

$Query = “INSERT INTO Students

(Psusername, firstName,lastName,eMail)

VALUES (‘$wsname’,’$firstname’, ‘$lastname’, ‘$email’ )”;




$Success=false;

if ($result = $mysqli-&gt;query($Query)) {

$Success=true;

}

$mysqli-&gt;close();




return $Success;

}




function countStudent ($student)

{

// Connect to the database

$mysqli = connectdb();

$firstname = $student-&gt;getFirstname();

$lastname = $student-&gt;getLastname();

$wsname = $student-&gt; getPsusername();

$email = $student-&gt;getEmail();







// Define the Query

// For Windows MYSQL String is case insensitive        $Myquery = “SELECT count(*) as count from Students           where Psusername=’$wsname'”;




if ($result = $mysqli-&gt;query($Myquery))

{

/* Fetch the results of the query */

while( $row = $result-&gt;fetch_assoc() )

{

$count=$row[“count”];

}




/* Destroy the result set and free the memory used for it */

$result-&gt;close();

}




$mysqli-&gt;close();




return $count;




}




The revised files that include the revisions to the DBQueries.php file and the StudentInsert.php are included in an attachment named wamp2.zip. You should copy that file to the SDEV AMI and test the insert functionality.

Figures 11, 12 and 13 show the results of testing the wamp2 zip file on the SDEV AMI. Notice, once a new record is entered in the database, it can be successfully displayed with the “Display Students” option.




<em>Figure 11 Inserting a new Student record </em>







<em>Figure 12 Successful Reply after Adding Record </em>




<em>Figure 13 Displaying the new Student Record </em>




Note the following code in the DBQueries.php file assumes you have honest users.




$Query = “INSERT INTO Students

(Psusername, firstName,lastName,eMail)

VALUES (‘$wsname’,’$firstname’, ‘$lastname’, ‘$email’ )”;







<ol start="8">

 <li>Next, we replace this statement with a prepared statements to help mitigate the SQL injection in the insertStudent function:</li>

</ol>

function insertStudent ($student)

{




// Connect to the database

$mysqli = connectdb();




$firstname = $student-&gt;getFirstname();

$lastname = $student-&gt;getLastname();

$wsname = $student-&gt;getPsusername();

$email = $student-&gt;getEmail();




// Add Prepared Statement

$Query = “INSERT INTO Students

(Psusername,firstName,lastName,eMail)

VALUES (?,?,?,?)”;







$stmt = $mysqli-&gt;prepare($Query);




$stmt-&gt;bind_param(“ssss”, $wsname,$firstname,$lastname,$email);$stmt-

&gt;execute();







$stmt-&gt;close();

$mysqli-&gt;close();




return true;

}




Note the bind statement is using “ssss” representing 4 strings. Other options include i for integer and d for double. We will use the prepared statement in the remaining examples to mitigate SQL Injection. This essentially prevents the system from processing extra characters if a user attempts to inject those into your query.

Note the functionality doesn’t change as a user can still insert new student records and display them as before.

Now that we have a form to Insert and Select data, we can continue to expand and add the delete and update functionality. The attached wamp3.zip contains the new functions for DeleteStudent and UpdateStudent function calls within the DBQueries.php file. Note, the other files are unchanged and further demonstrate the importance of both good and secure design for your code.







<ol start="9">

 <li>To prepare for the delete and update functionality, we need to add the privileges to the wamp.Students table. Enter the following command at the MySQL prompt on your SDEV AMI to ensure the proper permissions are grants to the Students table. Be sure to use the root user of the MySQL account when granting this permission.</li>

</ol>




GRANT Delete,Update ON wamp.Students TO <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="6c1b0d011c33191f091e2c00030f0d0004031f18">[email protected]</a> IDENTIFIED BY PASSWORD ‘*E090926B474658359CB983C65B768047DF3A55BE’;




<ol start="10">

 <li>Once the permissions are set, copy the wamp3.zip folder to your SDEV AMI and unzip and place the contents in a wamp3 folder in the htdocs folder. Be sure the directory structure is correct as previously discussed when you copied the wamp and wamp2.zip folders to the SDEV AMI.</li>

</ol>

As always, you should review and tinker with all aspects of the code to become comfortable with the functionality. The pattern of use and functionality is the same. Note that the DBQueries.php file make use of prepared statements for all queries. Some additional hyperlinks were also added to the code to easily return to the StudentApp from the response screens.

Envision how you could modify this template for adding additional tables and queries to those tables for an application of your choice.

Figures 14 and 15 shows the results of using the Update functionality on the SDEV AMI.




<em>Figure 14 Updating a record </em>







<em>Figure 15 Response after Updating Record </em>




Figures 16 and 17 show the results of using the Delete functionality on the SDEV AMI.







<em>Figure 16 Deleting a record</em>




<em>Figure 17 Response after deleting a record</em>

<strong> </strong>

<strong>Lab submission details: </strong>

For your lab for this week, you will create a WAMP application using the SDEV AMI.  The application should satisfy the following requirements:

<ol>

 <li>Allows a user to register and login to an application that will be developed in the future.</li>

 <li>When registering the user should provide a unique username and password. The password should be at least 8 characters in length and should include at least one upper case letter, one number and one special character.</li>

 <li>When logging into the application, if the username and password are correct, a simple ”Welcome to the SDEV 300 Application” screen should be displayed.</li>

 <li>The application should allow the user to update their username and password.</li>

 <li>The application should allow the user to delete their account.</li>

</ol>




Design requirements include the following:

<ol>

 <li>User data must be stored in a MySQL table. Additional data (e.g. Firstname, Lastname and other fields) may be stored as well but is not required. .</li>

 <li>Your design should be secure and at a minimum ensure no SQL injection is possible, passwords are encrypted in the database and only the least amount of privileges are given to database and web users for them to perform their functions.</li>

 <li>Take the time to design the flow of your forms, such that they flow logically within your application and are presented in an attractive easy-to-use Web interface.</li>

 <li>Create screen captures showing the successful running of your application from registering a user to updating and deleting user accounts. Each screen capture should be fully described.</li>

</ol>

For your deliverables, you should submit a winzip file containing your word document (or PDF file) with screen captures and detailed descriptions of the application running successfully along with your SQL script file and all PHP files. (Hint: Use the templates provided to you but change the functionality as required)

Include your full name, class number and section, professor name and date in the document.




<strong>Grading Rubric: </strong>

<table width="624">

 <tbody>

  <tr>

   <td width="126"><strong>Attribute </strong></td>

   <td width="498"><strong>Meets </strong></td>

  </tr>

  <tr>

   <td width="126">WAMP App</td>

   <td width="498"><strong>75 points </strong>Allows a user to register and login to an application. (20 points) When registering the user should provide a unique username and password. The password should be at least 8 characters in length and should include at least one upper case letter, one number and one special character.  (15 points) When logging into the application, if the username and password are correct, a simple  ”Welcome to the SDEV 300 Application” screen should be displayed.  (10 points)</td>

  </tr>

  <tr>

   <td width="126"> </td>

   <td width="498"> The application should allow the user to update their username and password.  (15 points) The application should allow the user to delete their account. (15 points) <strong>Does not use the SDEV AMI. (-100)</strong><strong>Does not use WAMP stack (-100) </strong><strong> </strong></td>

  </tr>

  <tr>

   <td width="126">Documentation and submission</td>

   <td width="498"><strong>25 points </strong>Submits a winzip file containing your word document (or PDF file) with screen captures and detailed descriptions of the application running successfully along with your SQL script file and all PHP files.  (20 points) Include your full name, class number and section, professor name and date in the document and Your report is well-organized and clearly written. (5 points)</td>

  </tr>

 </tbody>

</table>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>