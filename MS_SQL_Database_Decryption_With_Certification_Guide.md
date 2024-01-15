---- To restore the encrypted database on a different Server we need to move the backup file, the certificate, the private key to that server (or somewhere accessible)

       ---- We need to create the same master key encryption on this server
            
             USE MASTER;
             GO
             CREATE MASTER KEY ENCRYPTION BY PASSWORD = '12345';
       
      ---- Then we need to restore the certification:

            USE Master;
            CREATE CERTIFICATE TEST_CERT
            FROM FILE = 'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\TEST_CERT.cer'
            WITH PRIVATE KEY (FILE = 'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\private key.pvk',
            DECRYPTION BY PASSWORD = '12345');
          
     --- To restore the database

          USE MASTER;
          GO
          RESTORE DATABASE Database_Name
          FROM DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\Database_Name.bak'
          WITH REPLACE, STATS = 10;


             ------ NOTE: If desired to use Asymmetric key: 
              USE MASTER;
              GO
              CREATE ASYMMETRIC KEY SomeKey
              WITH ALGORITHM = RSA_2048
              ENCRYPTION BY PASSWORD = '12345';


------------------------------------------------------------------------------------------------------------------------

----- Note: To undo the changes we have made to the SQL server so far:

--- 1- we start by deleting the database encryption key (because the certification is bond to this certificate)
      USE Database_Name;
      GO
      DROP DATABASE ENCRYPTION KEY


--- 2- To drop the certification we created:
       USE MASTER;
       GO
       DROP CERTIFICATE TEST_CERT

--- 3- Now we can drop the master key by running:
       USE Master;
       GO
       DROP MASTER KEY;

--- Please note that we still have a copy of the encrypted database backup, the certificate, and the private key stored in the mentioned location.

--- Additional Considerations:
--- It's important to note that when restoring an encrypted backup to a different instance, the original certificate used when the backup was taken should be available on that instance


--- The process of creating an asymmetric key as an alternative to using a backup is also mentioned in the document, which provides additional flexibility in managing encryption keys.

Wishing you all the very best,
Nameer