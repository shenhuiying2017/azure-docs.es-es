## <a name="next-steps"></a>Pasos siguientes

Después de habilitar la integración de Azure Key Vault, puede habilitar el cifrado de SQL Server en la máquina virtual de SQL. En primer lugar, tendrá que crear una clave asimétrica en el almacén de claves y una clave simétrica dentro de SQL Server en la máquina virtual. A continuación, podrá ejecutar las instrucciones de T-SQL para habilitar el cifrado de las bases de datos y las copias de seguridad.

Hay varias formas de cifrado que puede aprovechar:

* [Cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Copias de seguridad cifradas](https://msdn.microsoft.com/library/dn449489.aspx)
* [Cifrado de nivel de columna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Los siguientes scripts de Transact-SQL proporcionan ejemplos para cada una de estas áreas.

### <a name="prerequisites-for-examples"></a>Requisitos previos para los ejemplos

Cada ejemplo se basa en los dos requisitos previos: una clave asimétrica de Key Vault denominada **CONTOSO_KEY** y una credencial creada por la característica de integración de AKV denominada **Azure_EKM_TDE_cred**. Los siguientes comandos Transact-SQL configuran estos requisitos previos para ejecutar los ejemplos.

``` sql
USE master;
GO

sp_configure [show advanced options], 1;
GO
RECONFIGURE;
GO

-- Enable EKM provider
sp_configure [EKM provider enabled], 1;
GO
RECONFIGURE;

--create provider
CREATE CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov
FROM FILE = 'C:\Program Files\SQL Server Connector for Microsoft Azure Key Vault\Microsoft.AzureKeyVaultService.EKM.dll';
GO

--create credential
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--must have sysadmin
ALTER LOGIN [TDE_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'keytestvault',  --key name
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Cifrado de datos transparente (TDE) 

1. Cree un inicio de sesión de SQL Server que el motor de base de datos usará para TDE, después agréguele la credencial.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN TDE_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN TDE_Login
   ADD CREDENTIAL Azure_EKM_TDE_cred;
   GO
   ```

1. Cree la clave de cifrado de base de datos que se usará para TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Copias de seguridad cifradas

1. Cree un inicio de sesión de SQL Server que el motor de base de datos usará para cifrar las copias de seguridad, después agréguele la credencial.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN Backup_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN Backup_Login
   ADD CREDENTIAL Azure_EKM_Backup_cred ;
   GO
   ```

1. Realice una copia de seguridad de la base de datos especificando el cifrado con la clave asimétrica guardada en el almacén de claves.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Cifrado de nivel de columna (CLE)

Este script crea una clave simétrica protegida por la clave asimétrica en el almacén de claves y, a continuación, usa la clave simétrica para cifrar los datos de la base de datos.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Recursos adicionales

Para más información sobre cómo usar estas características de cifrado, vea [Uso de la administración extensible de claves con las características de cifrado de SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Tenga en cuenta que en los pasos descritos en este artículo se supone que ya tiene SQL Server en ejecución en una máquina virtual de Azure. De lo contrario, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Para otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [Información general de SQL Server en máquinas virtuales de Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).