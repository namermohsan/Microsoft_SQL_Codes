```
CREATE TABLE payment_table (
  payment_id INT PRIMARY KEY,
  payment INT,
  total_payment INT,
  remaining_debt INT,
  payment_date DATE DEFAULT CONVERT(date, GETDATE())
);
```

**To get the full date with time optianally you can use:
Payment_date DATETIME DEFAULT GETDATE()**

```
create TRIGGER payment_trigger
ON payment_table
AFTER INSERT
AS
BEGIN
  UPDATE payment_table
  SET total_payment = i.payment * i.payment_id
  FROM payment_table p
  INNER JOIN inserted i ON p.payment_id = i.payment_id;
END;
```

```
create TRIGGER debt_trigger
ON payment_table
AFTER UPDATE
AS
BEGIN
  UPDATE payment_table
  SET remaining_debt = 1500 + 1500 * 20/100 - i.total_payment
  FROM payment_table p
  INNER JOIN inserted i ON p.payment_id = i.payment_id;
END;
```
```
create trigger ALERT
ON payment_table
AFTER INSERT, UPDATE
AS
BEGIN
    IF EXISTS (SELECT * from INSERTED where remaining_debt < 0)
	BEGIN
	   RAISERROR('You have paid the Debt', 16 , 1)
	   ROLLBACK TRANSACTION;
	   END
END;
```