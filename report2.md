

**​**

&#x20;                                                                  ​SQL INJECTION REPORT



​Project Title: SQL Injection Vulnerability Exploitation and Remediation

Target Environment: PortSwigger Web Security Academy Lab

Vulnerability Class: In-Band SQL Injection (SQLi)

​Executive Summary

In this lab, I identified, exploited, and remediated an In-Band SQL Injection vulnerability within a web application's product filtering feature. The vulnerability allowed an unauthorized user to manipulate database queries, bypassing application filters to access hidden internal records.

​Threat Analysis and Methodology

​Step 1: Identifying the Entry Point

While navigating the web application, I observed that product categories were filtered via URL parameters:

https://target-app.net/filter?category=Gifts

​Step 2: Probing for Vulnerability

I injected a single quote into the category parameter:

https://target-app.net/filter?category=Gifts'

The server returned a database syntax error, indicating that user inputs were being directly concatenated into backend SQL queries without proper sanitization.

​Step 3: Executing the Exploitation Payload



I crafted a SQL injection payload using a boolean logic bypass:

' OR 1=1--

Full exploited URL:

https://target-app.net/filter?category=Gifts' OR 1=1--

​How the exploit works:

​The single quote breaks out of the original string literal.

​OR 1=1 forces the database condition to evaluate to true for every database record.

​The double dash instructs the SQL parser to ignore the remainder of the developer's original query logic.

​Result: The application displayed all products from the database, including unreleased and private catalog items.

​Remediation and Code Fix

​Root Cause:

The application used vulnerable dynamic SQL string concatenation:

SELECT \* FROM products WHERE category = '$user\_input' AND released = 1;

​Recommended Security Fix:

To prevent SQL injection, dynamic string concatenation must be replaced with parameterized queries (prepared statements). Prepared statements ensure the database driver treats user input strictly as data rather than executable code.

​Secure Implementation Example (Prepared Statement):

PREPARE get\_products FROM 'SELECT \* FROM products WHERE category = ? AND released = 1';

SET @cat = 'Gifts';

EXECUTE get\_products USING @cat;

​Key Knowledge Gained

​Understood how SQL injection vulnerabilities manifest due to unsafe string concatenation.

​Mastered payload creation using boolean conditions and SQL comment operators.

​Demonstrated how parameterized queries completely eliminate SQL injection risks by separating query logic from data input

​Yo

