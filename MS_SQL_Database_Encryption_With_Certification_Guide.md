--- Encrypting backup of SQL: (Note: we can do a whole database encryption or encrypting individual columns)

----Note: the database master key is used to protect the primary key of the certificate. 


--- we need to start creating a certificate and encryption key,
   --- creating the master key:


       USE Master;
       GO
       CREATE MASTER KEY ENCRYPTION BY PASSWORD = '12345';
    
   --- Creating the certificate:


       USE MASTER;
       GO
       CREATE CERTIFICATE TEST_CERT with subject = 'TEST encryption Key';

  --- At this point we need to backup the certificate:


      USE MASTER;
      BACKUP CERTIFICATE TEST_CERT
      TO FILE = 'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\TEST_CERT.cer'
      WITH PRIVATE KEY ( FILE = 'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\private key.pvk',
                         ENCRYPTION BY PASSWORD = '12345');
      GO 


  --- Creating database encryption key 
      
      USE Database_Name;
      GO
      CREATE DATABASE ENCRYPTION KEY
      WITH ALGORITHM = AES_256
      ENCRYPTION BY SERVER CERTIFICATE TEST_CERT;   




   --- To Backup the database :
      
      USE MASTER;
      GO
      BACKUP DATABASE Database_Name
      TO DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\Database_Name.bak'
      WITH COMPRESSION, ENCRYPTION(ALGORITHM = AES_256, SERVER CERTIFICATE = TEST_CERT),
      STATS = 10
      GO

---- Note: To drop the database:

     USE MASTER;
     GO
     DROP DATABASE Database_Name;


Wishing you all the very best,
Nameer
