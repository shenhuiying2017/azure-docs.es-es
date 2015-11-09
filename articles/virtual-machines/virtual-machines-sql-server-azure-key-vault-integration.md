<properties 
	pageTitle="Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure"
	description="Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con Almacén de claves de Azure. Este tema explica cómo usar la integración de Almacén de claves de Azure con máquinas virtuales de SQL Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure

## Información general
SQL Server tiene varias características de cifrado, como el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), el [cifrado de nivel de columna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), y la [copia de seguridad cifrada](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de cifrado requieren administrar y almacenar las claves criptográficas que se usan para el cifrado. El servicio de Almacén de claves de Azure (AKV) está diseñado para mejorar la seguridad y la administración de estas claves en una ubicación segura y con gran disponibilidad. El [conector de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use estas claves de Almacén de claves de Azure.

Si se ejecuta SQL Server con equipos locales, hay una serie de [pasos a seguir para tener acceso a Almacén de claves de Azure desde el equipo de SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Pero para SQL Server en las máquinas virtuales de Azure, puede ahorrar tiempo usando la característica *Integración de Almacén de claves de Azure*. Con algunos cmdlets de Azure PowerShell para habilitar esta característica, puede automatizar la configuración necesaria para que una máquina virtual de SQL tenga acceso a su Almacén de claves.

Cuando se habilita esta característica, automáticamente se instala el conector de SQL Server, se configura el proveedor EKM para obtener acceso a Almacén de claves de Azure y se crea la credencial para que pueda tener acceso a su almacén. Si examinamos los pasos descritos en la documentación local que se mencionó anteriormente, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que aún tiene que hacer manualmente es crear el Almacén de claves y las claves. Desde allí, se automatiza toda la configuración de la máquina virtual de SQL. Cuando esta característica haya completado el programa de instalación, puede ejecutar instrucciones de T-SQL para empezar a cifrar sus bases de datos o copias de seguridad como lo haría normalmente.

## Preparación para la integración de AKV
Para usar la Integración de Almacén de claves de Azure para configurar la máquina virtual de SQL Server, hay varios requisitos previos:

1.	[Instalar Azure Powershell](#install-azure-powershell)
2.	[Crear un directorio de Azure Active Directory](#create-an-azure-active-directory)
3.	[Creación de un Almacén de claves](#create-a-key-vault)

Las secciones siguientes describen estos requisitos previos y la información que necesita recopilar para ejecutar más adelante los cmdlets de PowerShell.

### Azure PowerShell
Asegúrese de que ha instalado el SDK más reciente de Azure PowerShell. Para obtener más información, vea [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

### Creación de un directorio de Azure Active Directory
En primer lugar, tiene que tener un [directorio de Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) en la suscripción. Entre otras muchas ventajas, esto le permite conceder permiso para Almacén de claves a determinados usuarios y aplicaciones.

A continuación, registre una aplicación con AAD. Esto le dará una cuenta de entidad de servicio que tenga acceso a su Almacén de claves, algo que la máquina virtual va a necesitar. En el artículo sobre Almacén de claves de Azure, puede encontrar estos pasos en la sección [Registro de una aplicación con Azure Active Directory](../key-vault/key-vault-get-started.md#register), o bien puede ver los pasos con capturas de pantalla en la sección sobre **obtener una identidad para la aplicación** de [esta entrada de blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Antes de completar estos pasos, tenga en cuenta que durante este registro tiene que recopilar la siguiente información que necesitará más adelante cuando habilite la integración de Almacén de claves de Azure en la máquina virtual de SQL.

- Después de agregar la aplicación, busque el **IDENTIFICADOR DE CLIENTE** en la pestaña de **CONFIGURACIÓN**. ![ID de cliente de Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	El identificador de cliente se asigna posteriormente al parámetro **$spName** (nombre de entidad de seguridad de servicio) en el script de PowerShell para habilitar la integración de Almacén de claves de Azure. 
- Además, durante estos pasos al crear la clave, copie el secreto de la clave tal como se muestra en la captura de pantalla siguiente. Este secreto de clave se asigna posteriormente al parámetro **$spSecret** parámetro (secreto de entidad de servicio) en el script de PowerShell. ![Secreto de Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- Tiene que autorizar este nuevo identificador de cliente para que tenga los siguientes permisos de acceso: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign**, y **verify**. Esto se hace con el cmdlet [AzureKeyVaultAccessPolicy Set](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx). Para encontrar más información consulte [Autorización de la aplicación para que use la clave o el secreto](../key-vault/key-vault-get-started.md#authorize).

### Creación de un Almacén de claves
Para poder usar Almacén de claves de Azure para guardar las claves que se utilizarán para el cifrado en la máquina virtual, tiene que tener acceso a un Almacén de claves. Si no ha configurado ya su Almacén de claves, cree uno siguiendo los pasos que se mencionan en el tema [Introducción al Almacén de claves de Azure](../key-vault/key-vault-get-started.md). Antes de completar estos pasos, tenga en cuenta que durante esta configuración tiene que recopilar la alguna información que necesitará más adelante cuando habilite la integración de Almacén de claves de Azure en la máquina virtual de SQL.

Cuando llegue al paso Creación de un Almacén de claves, tenga en cuenta la propiedad **vaultUri** devuelta, que es la dirección URL del Almacén de claves. En el ejemplo proporcionado en este paso, que se muestra a continuación, el nombre del Almacén de claves es ContosoKeyVault, por lo tanto, su dirección URL será https://contosokeyvault.vault.azure.net/.

![Secreto de Azure Active Directory](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
La URL del Almacén de claves se asigna posteriormente al parámetro **$akvURL** en el script de PowerShell para habilitar la integración de Almacén de claves de Azure.

## Configuración de la integración de AKV
Use PowerShell para configurar la integración de Almacén de claves de Azure. Las secciones siguientes proporcionan una visión general de los parámetros necesarios y, a continuación, un script de PowerShell de ejemplo.

### Parámetros de entrada
En la tabla siguiente se enumeran los parámetros necesarios para ejecutar el script de PowerShell en la sección siguiente.

|Parámetro|Descripción|Ejemplo|
|---|---|---|
|**$akvURL**|**La dirección URL del Almacén de claves**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nombre de entidad de servicio**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Secreto de entidad de servicio**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**Nombre de credencial**: la integración de AKV crea una credencial en SQL Server, permitiendo el acceso de la máquina virtual al Almacén de claves. Elija un nombre para esta credencial.|"mycred1"|
|**$vmName**|**Nombre de máquina virtual**: el nombre de una máquina virtual de SQL creada anteriormente.|"myvmname"|
|**$serviceName**|**Nombre de servicio**: nombre del servicio en la nube que está asociado a la máquina virtual de SQL.|"mycloudservicename"|

### Habilitación de la integración de AKV con PowerShell
El cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** crea un objeto de configuración para la característica de integración de Almacén de claves de Azure. **AzureVMSqlServerExtension Set** configura esta integración con el parámetro **KeyVaultCredentialSettings**. Los pasos siguientes muestran cómo usar estos comandos.

1. En Azure PowerShell, configure primero los parámetros de entrada con los valores específicos como se describe en las secciones anteriores de este tema. El siguiente script es un ejemplo:
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	A continuación, use el siguiente script para configurar y habilitar la integración de AKV.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

La extensión del agente de Iaas de SQL actualizará la máquina virtual de SQL con esta nueva configuración.

## Pasos siguientes
Después de habilitar la integración de Almacén de claves de Azure, puede habilitar el cifrado de SQL Server en la máquina virtual de SQL. En primer lugar, tendrá que crear una clave asimétrica en el Almacén de claves y una clave simétrica dentro de SQL Server en la máquina virtual. A continuación, podrá ejecutar las instrucciones de T-SQL para habilitar el cifrado de las bases de datos y las copias de seguridad.

Hay varias formas de cifrado que puede aprovechar:

- [Cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [Copias de seguridad cifradas](https://msdn.microsoft.com/library/dn449489.aspx)
- [Cifrado de nivel de columna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Los siguientes scripts de Transact-SQL proporcionan ejemplos para cada una de estas áreas.

>[AZURE.NOTE]Cada ejemplo se basa en los dos requisitos previos: una clave asimétrica del Almacén de claves denominada **CONTOSO\_KEY** y una credencial creada por la característica de integración de AKV llamada **Azure\_EKM\_TDE\_cred**.

### Cifrado de datos transparente (TDE)
1. Cree un inicio de sesión de SQL Server que el motor de base de datos usará para TDE, después agréguele la credencial.
	
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
	
2. Cree la clave de cifrado de base de datos que se usará para TDE.
	
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

### Copias de seguridad cifradas
1. Cree un inicio de sesión de SQL Server que el motor de base de datos usará para cifrar las copias de seguridad, después agréguele la credencial.
	
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
	
2. Realice una copia de seguridad de la base de datos especificando el cifrado con la clave asimétrica guardada en el Almacén de claves.
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### Cifrado de nivel de columna (CLE)
Este script crea una clave simétrica protegida por la clave asimétrica en el Almacén de claves y, a continuación, usa la clave simétrica para cifrar los datos de la base de datos.

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

## Recursos adicionales
Para obtener más información sobre cómo usar estas características de cifrado, consulte [Uso de la administración extensible de claves con las características de cifrado de SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Tenga en cuenta que en los pasos descritos en este artículo se supone que ya tiene SQL Server en ejecución en una máquina virtual de Azure. De lo contrario, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md) Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [Información general sobre SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO1-->