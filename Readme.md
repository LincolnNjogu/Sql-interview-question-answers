# Sql-interview-question-solutions
Answers to sql interview

--------------------------------------------------------------------------------
-- TABLES STRUCTURE
--------------------------------------------------------------------------------

NOTE: these are just extracts from bigger tables, to show their structure

  # table name: client 


|client_id|first_name|middle_name|last_name|date_of_birth|
|---------|----------|-----------|---------|-------------|
|33       |Susan     |Mapenzi    |Marigu   |1974-06-11   |
|35       |Paul      |           |Pogba    |1993-03-15   |
|36       |Hafsa     |Wangui     |Munga    |1987-05-07   |
|37       |Everlyne  |           |Maten'ge |1973-02-27   |
|38       |Barack    |           |Obama    |1961-08-04   |
|39       |Prudence  |Salim      |Okeyo    |1985-02-16   |
|40       |Rosemary  |Pauline    |Kinyua   |1977-01-27   |
|42       |Elizabeth |           |Mbaji    |1975-10-03   |
|43       |Johny     |Paul       |Orengo   |1971-07-29   |
|44       |Merceline |Lucy       |Njenga   |1982-04-21   | 

  # table name: loan
  
|loan_id|client_id|vehicle_id|principal_amount|submitted_on_date|
|-------|---------|----------|----------------|-----------------|
|75676  |40784    |24        |106500          |2019-05-02       |
|75659  |40760    |26        |108400          |2020-12-05       |
|75685  |40807    |27        |101500          |2019-05-02       |
|75657  |40796    |28        |271482          |2019-06-21       |
|75662  |40803    |29        |114400          |2019-05-02       |
|75660  |40737    |30        |95300           |2019-05-02       |
|75656  |40815    |31        |78500           |2019-05-02       |
|75666  |40834    |32        |111800          |2019-05-02       |
|75658  |40811    |33        |107050          |2019-05-02       |
|75663  |40840    |34        |101800          |2019-05-02       |

  # table name: vehicle
 
|vehicle_id|make    |model_name      |
|----------|--------|----------------|
|24        |Haojin  |HJ 150CC-11A    |
|26        |Honda   |Ace CB 125CC ES |
|27        |TVS     |HLX 125CC ES    |
|29        |TVS     |HLX 150CC X     |
|30        |TVS     |HLX 100CC KS    |
|31        |Haojin  |HJ 125CC-A      |
|32        |Boxer   |BM 150CC (4)    |   
|33        |Ferrari |Enzo 6000CC     |
|34        |Boxer   |BM 150cc-2      |
|35        |Boxer   |BM 150cc-3      |

--------------------------------------------------------------------------------
-- QUESTIONS
--------------------------------------------------------------------------------

Using the tables above, please write the SQL code that would answer each of the following questions (write the code below each of them).

1. -- Select all the clients called Paul in first_name or middle_name and who are more than 25 years old.
   -- In the results, create a column with the client's age in years. 
   -- Order them from older to younger.
   
**Answer:**

					SELECT first_name, middle_name,EXTRACT(years from AGE(date_of_birth)) as age 
					FROM client 
					WHERE EXTRACT(years from AGE(date_of_birth))>25
					AND
					first_name='Paul' OR middle_name='Paul'
					ORDER BY age DESC;
    
2. -- Add a column to the table from question (1) that contains the number of loans each customer made.
   -- If there is no loan, this column should show 0.
   
**Answer**

						ALTER TABLE client
						ADD COLUMN number_of_loans int 
						DEFAULT 0;*

						*UPDATE client
						SET number_of_loans= (SELECT COUNT(principal_amount)
						FROM loan where client.client_id=loan.client_id
						GROUP BY client_id);

3. -- Select the 100cc, 125cc and 150cc bikes from the vehicle table.
   -- Add an engine_size column to the output (that contains the engine size).
   
 **Answer**
 
						SELECT *,SPLIT_PART(model_name,' ',2) as engine_size
						FROM vehicle
						WHERE
						SPLIT_PARTmodel_name,' ',2) IN ('150CC','125CC','100CC');


4. -- Calculate the total principal_amount per client full name (one column that includes all the names for each client) and per vehicle make.

**Answer**

						SELECT CONCAT(first_name,' ',middle_name,' ',last_name) AS full_name,
						SUM(principal_amount),make
						FROM client
						LEFT JOIN loan ON client.client_id=loan.client_id
						LEFT JOIN vehicle ON vehicle.vehicle_id=loan.vehicle_id
						GROUP BY client.client_id,vehicle.make


5. -- Select the loan table and add an extra column that shows the chronological loan order for each client based on the submitted_on_date column: 
   -- 1 if it's the client's first sale, 2 if it's the client's second sale etc.
   -- Call it loan_order

**Answer**
