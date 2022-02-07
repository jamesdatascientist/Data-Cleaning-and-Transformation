# data_cleaning_and_transformation
-- Retrieve the Server Name
SELECT @@SERVERNAME [Server Name]

-- Retrieve the databases
SELECT db_name() [Current Database]

SELECT * FROM sys.databases;

-- Select and activate the salesDB database
USE salesDB;

-- Retrieve the tables from the salesDB database
SELECT table_name from INFORMATION_SCHEMA.TABLES;

-- Drop transactions2 table if it exist
DROP TABLE transactions2;

-- Make a copy of the transactions table to do cleaning and transformation
SELECT *
INTO transactions2
FROM transactions;


-- Get the total records of transactions in the transaction2 table
SELECT COUNT(*) FROM transactions2;


-- Get the count of transactions based on currency used
SELECT currency, COUNT(*) [No. of Currency] FROM transactions2
GROUP BY currency ORDER BY 1;


-- HANDLING MISSING VALUES
-- Get the records of transactions where the sales_amount is zero or less 
-- This could be treated as missing values due to unsuccessful transactions
SELECT * FROM transactions2 WHERE sales_amount <=0;

-- Drop the records where the sales_amount is less than or equal to zero
SELECT COUNT(*) FROM transactions2 WHERE sales_amount <=0;
DELETE FROM transactions2 WHERE sales_amount <= 0;


-- Get the records of transactions where the sales_qty is zero or less
SELECT * FROM transactions2 WHERE sales_qty <= 0;


-- Remove duplicate values from the transaction records
SELECT DISTINCT * FROM transactions2
MINUS
SELECT DISTINCT * FROM transactions2 WHERE currency IN ('USD','NGN');


-- Retrieve the records where US Dollar is used for the transactions
SELECT * FROM transactions2 WHERE currency='USD';


-- Verify if the sales_amount column has any null value
SELECT * FROM transactions2 WHERE sales_amount IS NULL;


-- Verify if the sales_qty column has any null value
SELECT * FROM transactions2 WHERE sales_qty IS NULL;


-- Create a new column and transform the value of US Dollars into Nigerian Naira
ALTER TABLE dbo.transactions2 
ADD salesAmt Numeric DEFAULT NULL;

UPDATE dbo.transactions2
SET salesAmt = sales_amount


-- Verify the populated data in the new column
SELECT * FROM dbo.transactions2;


-- Transform the the USD currency value to equivalent NGN to normalize the salesAmt
-- (Multiply the salesAmt column by 415 since 1USD = NGN415 ) 
UPDATE transactions2 
SET salesAmt = salesAmt * 415
WHERE currency = 'USD';


-- Verify the changes in the transform records
SELECT * FROM dbo.transactions2;


-- Create a non duplicate transaction view
CREATE VIEW nonDuplicate_Txn
AS
SELECT DISTINCT * FROM transactions2;


-- Lets query the view created
SELECT * FROM nonDuplicate_Txn;
