# Como hacer backups

``` sql
          USE master
          GO

          -- Backup completo
          -- El .bak es un archivo que contienen distintos backups, es solo un lugar donde se guardaran todos los backup, diferenciales, log, full
          BACKUP DATABASE NORTHWND
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH
          NAME = 'Backupcompleto_03_03_2025',
          DESCRIPTION = 'Backup completo de northwind'
          GO
          -- Hace un backup completo

          -- Backup diferencial
          BACKUP DATABASE Northwind
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH
          NAME = 'BackupDiferencial_04_03_2025',
          DESCRIPTION = 'Backup diferencial1 de northwind',
          DIFFERENTIAL -- Se agrega el tipo de backup

          -- Backup log
          -- Para hacer esto se necesita hacer con un tipo de recuperación FULL
          BACKUP LOG Northwind
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH
          NAME = 'BackupLog1',
          DESCRIPTION = 'Backup de logs de transacciones 1'

          -- Backup de solo copia
          BACKUP DATABASE Norhind
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH
          COPY_ONLY,
          NAME = 'Backup solo copia',
          DESCRIPTION = 'Backup de solo copia de base de datos'

          -- Backup de filegruop (Parciales)
          BACKUP DATABASE Northwind
          FILEGROUP = 'Primary'
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH
          NAME = 'NorthwindBackupParcial'

          -- Backup de la cola de log
          -- Se usa cuando la base de datos ya no funciona, no es muy común
          BACKUP LOG Northwind
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH
          RECOVERY,
          NAME = 'Backup_cola_log1',
          DESCRIPTION = 'Realiza un backup de cola de log'

```


``` sql
          -- Estrategias de Backups
          -- 1. Backup completos (FULL)
          -- 2. Completos  + Diferenciales
          -- 3. Completos + Diferenciales + Log de transacción

          -- Plan de estrategias de backups

          -- Backup completo
          BACKUP DATABASE Northwind
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH
          NAME = 'BackupCompleto_03_03_2025',
          DESCRIPTION = 'Primer backup completo'
          GO

          USE Northwind;
          INSERT INTO Customers(CustomerID, CompanyName, country) VALUES ('ABCD3', 'PEPSI', 'USA'),('ABCD4', 'COCA', 'PERU'),

          SELECT * FROM Customers WHERE CustomerID in ('ABCD3', 'ABCD4')

          -- Backup de Logs  
          BACKUP LOG Northwind
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH NAME = 'Backup_log_1',
          DESCRIPTION = 'LOG del 03/03/2025'

          -- Revisar el archivo .bak, visualiza los archivos
          RESTORE HEADERONLY
          FROM DISK = 'C:\Backups\backupNorthwind.bak'

          -- La posición es importante para restaurar las bases de datos

          -- Otros inserts
           USE Northwind;
          INSERT INTO Customers(CustomerID, CompanyName, country) VALUES ('ABCD4', 'Donas', 'USA'),('ABCD5', 'Papas', 'PERU'),

          -- Backup diferencial
          -- No se puede hacer un diferencial si no hay un full antes
          BACKUP DATABASE Northwind
          TO DISK = 'C:\Backups\backupNorthwind.bak'
          WITH 
          NAME = 'Backup_diferencial_1',
          DESCRIPTION = 'Backup diferencial'
          DIFFERENTIAL
          GO

          -- Eliminación de los registros para que funcione el log 2 y el diferencia
          DELETE CUSTOMERS WHERE CUSTOMERSID IN ('ABCD5', 'ABCD6');

          -- Backup de Logs  
                    -- Se recomienda hacer cada 15 min.
                    BACKUP LOG Northwind
                    TO DISK = 'C:\Backups\backupNorthwind.bak'
                    WITH NAME = 'Backup_log_2',
                    DESCRIPTION = 'LOG del 04/03/2025'

          -- Revisar el archivo .bak, visualiza los archivos
          RESTORE HEADERONLY
          FROM DISK = 'C:\Backups\backupNorthwind.bak'

          -- Restaurar el backups completos y logs
                    -- Restaura solo la base de datos completa
                    RESTORE DATABASE northwind
                    FROM DISK = 'C:\Backups\backupNorthwind.bak'
                    WITH FILE = 1,
                    RECOVERY 

          -- Restaura la base de datos y el log
                    -- Restaurar el backups completos
                    RESTORE DATABASE northwind
                    FROM DISK = 'C:\Backups\backupNorthwind.bak'
                    WITH FILE = 1,
                    NORECOVERY

                    -- Revisar el .bak

                    -- Restaurar el log 2
                    RESTORE LOG northwind
                    FROM DISK = 'C:\Backups\backupNorthwind.bak'
                    WITH FILE = 2,
                    NORECOVERY

                    -- Restaurar el log 4
                    RESTORE LOG northwind
                    FROM DISK = 'C:\Backups\backupNorthwind.bak'
                    WITH FILE = 2,
                    RECOVERY
```

Comentarios:
          => Los logs son transacciones que no se hicieron commit. Guardan los resultados de las sentencias.
          => SQL Server M. tiene un espacio para logs y cada vez que se cierra sesión o se hace un respaldo, se vacía.
          => NORECOVERY se usa cuando se van a restaurar varios backups, aparece en el gestor "restoring" hasta que se coloque el recovery