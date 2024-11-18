# Library Management System using SQL Project

## Project Overview

Project Title: Library Management System<br>
Database: Library_project_db<br>

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.This project demonstrates database management and analysis capabilities for a multi-branch library system.

## Objectives
1. Set up the Library Management System Database: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.<br>
2. CRUD Operations: Perform Create, Read, Update, and Delete operations on the data.<br>
3. SQL Queries: Develop complex queries to analyze and retrieve specific data.

## Project Structure
1. Database Setup
* Database Creation: Created a database named Library_project_db.<br>
* Table Creation: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

      CREATE DATABASE Library_project_db;

      DROP TABLE IF EXISTS branch;
      CREATE TABLE branch
      (
                  branch_id VARCHAR(10) PRIMARY KEY,
                  manager_id VARCHAR(10),
                  branch_address VARCHAR(30),
                  contact_no VARCHAR(15)
      );
      
      
      -- Create table "Employee"
      DROP TABLE IF EXISTS employees;
      CREATE TABLE employees
      (
                  emp_id VARCHAR(10) PRIMARY KEY,
                  emp_name VARCHAR(30),
                  position VARCHAR(30),
                  salary DECIMAL(10,2),
                  branch_id VARCHAR(10),
                  FOREIGN KEY (branch_id) REFERENCES  branch(branch_id)
      );
      
      
      -- Create table "Members"
      DROP TABLE IF EXISTS members;
      CREATE TABLE members
      (
                  member_id VARCHAR(10) PRIMARY KEY,
                  member_name VARCHAR(30),
                  member_address VARCHAR(30),
                  reg_date DATE
      );
      
      
      
      -- Create table "Books"
      DROP TABLE IF EXISTS books;
      CREATE TABLE books
      (
                  isbn VARCHAR(50) PRIMARY KEY,
                  book_title VARCHAR(80),
                  category VARCHAR(30),
                  rental_price DECIMAL(10,2),
                  status VARCHAR(10),
                  author VARCHAR(30),
                  publisher VARCHAR(30)
      );



      -- Create table "IssueStatus"
      DROP TABLE IF EXISTS issued_status;
      CREATE TABLE issued_status
      (
                  issued_id VARCHAR(10) PRIMARY KEY,
                  issued_member_id VARCHAR(30),
                  issued_book_name VARCHAR(80),
                  issued_date DATE,
                  issued_book_isbn VARCHAR(50),
                  issued_emp_id VARCHAR(10),
                  FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
                  FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id),
                  FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn) 
      );
      
      
      
      -- Create table "ReturnStatus"
      DROP TABLE IF EXISTS return_status;
      CREATE TABLE return_status
      (
                  return_id VARCHAR(10) PRIMARY KEY,
                  issued_id VARCHAR(30),
                  return_book_name VARCHAR(80),
                  return_date DATE,
                  return_book_isbn VARCHAR(50),
                  FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
      );

2. CRUD Operations
* Create: Inserted sample records into the books table.<br>
* Read: Retrieved and displayed data from various tables.<br>
* Update: Updated records in the employees table.<br>
* Delete: Removed records from the members table as needed.


Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

    insert into books(isbn,book_title,category,rental_price,status,author,publisher)
    values('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')

Task 2: Update an Existing Member's Address

    update members
    set member_address ='125 Oak St'
    where member_id ='C101';
    select*from members;

Task 3: Delete a Record from the Issued Status Table 
Objective: Delete the record with issued_id = 'IS121' from the issued_status table.

    select*
    from issued_status
    where issued_id = 'IS121'

    delete 
    from issued_status
    where issued_id = 'IS121'

Task 4: Retrieve All Books Issued by a Specific Employee 
Objective: Select all books issued by the employee with emp_id = 'E101'.

    select*
    from employees
    where emp_id = 'E101'

Task 5: List Members Who Have Issued More Than One Book 
Objective: Use GROUP BY to find members who have issued more than one book.

    select
        ist.issued_emp_id,
         e.emp_name
        -- COUNT(*)
    from issued_status as ist
    join
    employees as e
    on e.emp_id = ist.issued_emp_id
    group by 1, 2
    having count(ist.issued_id) > 1


Task 6: Create Summary Tables:
Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

    create table book_counts
    as 
    select 
    	b.isbn,
    	b.book_title,
    	count(ist.issued_id) as No_isssued	
    from books as b
    join issued_status as ist
    on ist.issued_book_isbn=b.isbn
    group by 1,2;

    select * from book_counts;


Task 7. Retrieve All Books in a Specific classic Category:

    select*
    from books
    where category='Classic'

Task 8: Find Total Rental Income by Category:

    select 	
    	category,
    	sum(rental_price),
    	count(*)
    from books
    group by 1

Task 9 List Members Who Registered in the Last 180 Days:

    select*
    from members
    where reg_date >= current_date - interval '180 days'

Task 10 List Employees with Their Branch Manager's Name and their branch details:

    select
    	e.*,
    	b.manager_id as Manager_id,
    	e.emp_name as Employee
    	
    from employees as e
    join branch as b
    on b.branch_id=e.branch_id
    join employees as e2
    on b.manager_id=e2.emp_id

Task 11. Create a Table of Books with Rental Price Above a Certain Threshold 7USD:

    create table Rental_price as
    
    select*
    from books
    where rental_price > 7;
    
    select*from Rental_price


Task 12: Retrieve the List of Books Not Yet Returned

    select
    	distinct ist.issued_book_name
    from issued_status ist
    left join return_status as r
    on ist.issued_id=r.issued_id
    where r.return_id is null;
    
    select*from issued_status

Task 13: Find Employees with the Most Book Issues Processed
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.

    select
    	e.emp_name,
    	b.*,
    	count(ist.issued_id) as No_issued_ID
    from issued_status ist
    join employees e
    on e.emp_id=ist.issued_emp_id
    
    join branch b
    on b.branch_id=e.branch_id
    group by 1,2


Task 14: CTAS: Create a Table of Active Members
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 2 months.

    create table Active_members as
    
    select*from members
    where member_id in (
    					select
    							distinct issued_member_id
    					from issued_status
    					where issued_date >= current_date - interval '2 month');
    
    
    select*from Active_members;

Task 15: Branch Performance Report
Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

    select
    		b.branch_id,
    		b.manager_id,
    		count(ist.issued_id) as Issued_ID,
    		count(rs.return_id) as Return_ID,
    		sum(bs.rental_price) as Rental_Price
    from issued_status as ist
    
    join employees as e
    on e.emp_id=ist.issued_emp_id
    
    join branch as b
    on b.branch_id=e.branch_id
    
    left join return_status as rs
    on rs.issued_id=ist.issued_id

    join books as bs
    on bs.isbn=ist.issued_book_isbn
    
    group by 1,2;

# Conclusion
This Library Management System SQL project successfully demonstrates a comprehensive approach to managing and analyzing library operations. Through the implementation of 15 complex SQL queries and various analytical techniques, the project achieved
    


