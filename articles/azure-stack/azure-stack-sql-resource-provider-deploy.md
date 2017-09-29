---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: "Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: JeffGo
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 018d556d52aa1a1f436460d9811c43f9b45bd440
ms.contentlocale: es-es
ms.lasthandoff: 09/15/2017

---

# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Uso de bases de datos SQL en Microsoft Azure Stack


Use el adaptador del proveedor de recursos de SQL Server para exponer las bases de datos SQL como un servicio de Azure Stack. Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear bases de datos para aplicaciones nativas en la nube y sitios web y cargas de trabajo que se basan en SQL, sin tener que proporcionar cada vez una máquina virtual ( VM) que hospede SQL Server.

El proveedor de recursos no admite todas las funcionalidades de administración de bases de datos de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Por ejemplo, no se admiten grupos de bases de datos elásticas ni se puede escalar el rendimiento de la base de datos. La API no es compatible con SQL DB.


## <a name="sql-server-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de SQL Server
El proveedor de recursos no se basa ni ofrece todas las funcionalidades de administración de bases de datos de Azure SQL Database. Por ejemplo, los grupos de bases de datos elásticas y la posibilidad de aumentar o disminuir el rendimiento de las bases de datos automáticamente no están disponibles. Sin embargo, el proveedor de recursos admite operaciones similares de creación, lectura, actualización y eliminación (CRUD).

El proveedor de recursos tiene tres componentes:

- **La máquina virtual del adaptador del proveedor de recursos SQL**, que es una máquina virtual Windows que ejecuta los servicios del proveedor.
- **El proveedor de recursos en sí**, que procesa las solicitudes de aprovisionamiento y expone los recursos de las bases de datos.
- **Los servidores que hospedan SQL Server**, que proporcionan capacidad para las bases de datos, conocidos como servidores de hospedaje. 

Esta versión ya no crea una instancia de SQL. Debe crear una o varias, o proporcionar acceso a instancias externas de SQL. Dispone de una serie de opciones, incluidas las plantillas, en los elementos de Marketplace y la [Galería de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows). 

>[!NOTE]
Si ha descargado alguno de los elementos de SQL Marketplace, asegúrese de descargar también la extensión IaaS de SQL o estos no se implementarán.


## <a name="deploy-the-resource-provider"></a>Implementación del proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 EVAL, que puede descargarse a través de Marketplace Management. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). El entorno de tiempo de ejecución de .NET 3.5 ya no es necesario.

2. [Descargue el archivo de binarios del proveedor de recursos SQL](https://aka.ms/azurestacksqlrp) y extráigalo en el host del kit de desarrollo.

3. Inicie sesión en el host del kit de desarrollo y extraiga el archivo de instalador del RP SQL en un directorio temporal.

4. Como parte de este proceso, se recupera el certificado raíz de Azure Stack y se crea un certificado autofirmado. 

    __Opcional:__ si tiene que proporcionar sus propios certificados, prepárelos y cópielos en un directorio local, si desea personalizar los certificados (que se pasan al script de instalación). Necesita los siguientes certificados:

    a. Un certificado comodín para *.dbadapter.\<región\>.\<fqdn externo\>. Este certificado debe ser de confianza, como sería emitido por una entidad de certificación; es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios. (Puede usarse un único certificado de sitio con el nombre explícito de la máquina virtual que proporcione durante la instalación).

    b. El certificado raíz utilizado por Azure Resource Manager para la instancia de Azure Stack. Si no se encuentra, se recuperará el certificado raíz.


5. Abra una **nueva** consola de PowerShell con privilegios elevados y cambie al directorio adonde extrajo los archivos. Use una nueva ventana para evitar problemas que puedan surgir de los módulos de PowerShell incorrectos que ya están cargados en el sistema.

6. Si ha instalado alguna de las versiones de los módulos AzureRm o AzureStack PowerShell que no sean 1.2.9 o 1.2.10, deberá quitarlas o, de lo contrario, la instalación no continuará. Se incluyen las versiones 1.3 o posterior.

7. Ejecute el script DeploySqlProvider.ps1 con los parámetros que se enumeran a continuación.

El script sigue estos pasos:

* Si es necesario, descargue una versión compatible de Azure PowerShell.
* Cargue los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
* Publique los paquetes de la galería para poder implementar las bases de datos SQL a través de ella.
* Implemente una máquina virtual con la imagen de Windows Server 2016 que creó en el paso 1 e instale el proveedor de recursos.
* Registre un registro DNS local que se asigne a la máquina virtual del proveedor de recursos.
* Registre el proveedor de recursos en Azure Resource Manager local (Inquilino y Administrador).

> [!NOTE]
> Si la instalación tarda más de 90 minutos, se puede producir un error y verá un mensaje de error en la pantalla y en el archivo de registro, pero la implementación vuelve a intentarse desde el paso que no pudo completarse. Es posible que los sistemas que no cumplan las especificaciones recomendadas de memoria y núcleos no puedan implementar el RP SQL.
>

Este es un ejemplo que puede ejecutar desde el símbolo del sistema de PowerShell (pero cambie la información de la cuenta y los puntos de conexión del portal según sea necesario):

```
# Install the AzureRM.Bootstrapper module
Install-Module -Name AzureRm.BootStrapper -Force

# Installs and imports the API Version Profile required by Azure Stack into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.10 -Force

# Download the Azure Stack Tools from GitHub and set the environment
cd c:\
Invoke-Webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip
Expand-Archive master.zip -DestinationPath . -Force

# This endpoint may be different for your installation
Import-Module C:\AzureStack-Tools-master\Connect\AzureStack.Connect.psm1
Add-AzureRmEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external" 

# For AAD, use the following
$tenantID = Get-AzsDirectoryTenantID -AADTenantName "<your directory name>" -EnvironmentName AzureStackAdmin

# For ADFS, replace the previous line with
# $tenantID = Get-AzsDirectoryTenantID -ADFS -EnvironmentName AzureStackAdmin

$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeploySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "SqlRPRG" -VmName "SqlVM" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass
 ```

### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pide que proporcione los requeridos.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado |
| --- | --- | --- |
| **DirectoryTenantID** | El identificador de directorio (guid) de AD FS o Azure. | _requerido_ |
| **AzCredential** | Proporcione las credenciales de la cuenta de administrador de servicio de Azure Stack. Debe usar las mismas credenciales que para la implementación de Azure Stack. | _requerido_ |
| **VMLocalCredential** | Defina las credenciales para la cuenta de administrador local de la máquina virtual del proveedor de recursos SQL. Esta contraseña también se utiliza para la cuenta **sa** de SQL. | _requerido_ |
| **ResourceGroupName** | Defina un nombre para un grupo de recursos en el que se almacenarán los elementos creados por este script. Por ejemplo, *SqlRPRG*. |  _requerido_ |
| **VmName** | Defina el nombre de la máquina virtual en la que se va a instalar el proveedor de recursos. Por ejemplo, *SqlVM*. |  _requerido_ |
| **DependencyFilesLocalPath** | Los archivos de certificados se deben colocar también en este directorio. | _opcional_ |
| **DefaultSSLCertificatePassword** | La contraseña para el certificado .pfx | _requerido_ |
| **MaxRetryCount** | Defina el número de veces que desea volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Defina el tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes) | No |
| **DebugMode** | Impide la limpieza automática en caso de error | No |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

> [!NOTE]
>  Una vez completado el script de instalación, deberá actualizar el portal para ver la hoja de administración.


1. En el escritorio de la máquina virtual de consola, haga clic en **Microsoft Azure Stack Portal** e inicie sesión en el portal como administrador de servicios.

2. Compruebe que la implementación se realizó correctamente. Haga clic en **Grupos de recursos** &gt;, haga clic en el grupo de recursos que usó y, a continuación, asegúrese de que en la parte de fundamentos de la hoja (la mitad superior) se indica ** _fecha_ (Correcto)**.

      ![Comprobación de la implementación de RP de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="provide-capacity-by-connecting-to-a-hosting-sql-server"></a>Aprovisionamiento de capacidad conectando con un servidor SQL de hospedaje

1. Inicie sesión en el portal de administración de Azure Stack como administrador de servicios.

2. Cree una máquina virtual SQL, a menos que tenga una ya disponible. Marketplace Management ofrece algunas opciones para implementar máquinas virtuales SQL.

3. Haga clic en **Proveedores de recursos** &gt; **SQLAdapter** &gt; **Hosting Servers** (Servidores de hospedaje) &gt; **+Agregar**.

    La hoja **SQL Hosting Servers** (Servidores de hospedaje SQL) es donde puede conectar el proveedor de recursos del servidor SQL a instancias reales de SQL Server que actúan como back-end del proveedor de recursos.

    ![Servidores de hospedaje](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.PNG)

4. Rellene el formulario con los detalles de conexión de la instancia de SQL Server.

    ![Nuevo servidor de hospedaje](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.PNG)

    > [!NOTE]
    > Siempre y cuando el inquilino y el administrador en Azure Resource Manager puedan acceder a la instancia de SQL, esta puede ponerse bajo el control del proveedor de recursos. La instancia de SQL __debe__ asignarse exclusivamente al RP.

5. A medida que agregue servidores, debe asignarlos a una SKU nueva o existente para diferenciar las ofertas de servicio. Por ejemplo, podría tener una instancia de SQL Enterprise que proporcione la capacidad de base de datos y la copia de seguridad automática, la reserva de servidores de alto rendimiento para los departamentos individuales, etc. El nombre de la SKU debe reflejar las propiedades para que los inquilinos puedan colocar sus bases de datos correctamente y todos los servidores de hospedaje de una SKU deben tener las mismas funcionalidades.

    Por ejemplo:

    ![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
Las SKU pueden tardar hasta una hora en estar visibles en el portal. No puede crear una base de datos hasta que se cree la SKU.


## <a name="create-your-first-sql-database-to-test-your-deployment"></a>Creación de la primera base de datos SQL para probar la implementación

1. Inicie sesión en el portal de administración de Azure Stack como administrador de servicios.

2. Haga clic en **+ Nuevo** &gt; **Datos y almacenamiento "** &gt; **SQL Server Datbase (versión preliminar)** &gt; **Agregar**.

3. Rellene el formulario con los detalles de la base de datos, incluidos el **Nombre de base de datos** y el **Tamaño máximo**, y cambie los otros parámetros según sea necesario. Se le pide que elija una SKU para la base de datos. Cuando se agregan servidores de hospedaje, se les asigna una SKU y las bases de datos se crean en ese grupo de servidores de hospedaje que componen la SKU.

    ![Nueva base de datos](./media/azure-stack-sql-rp-deploy/newsqldb.png)


4. Rellene la configuración de inicio de sesión: **Inicio de sesión de base de datos** y **Contraseña**. Se trata de una credencial de Autenticación de SQL que se crea solo para el acceso a esta base de datos. El nombre de usuario de inicio de sesión debe ser único globalmente. Cree una nueva configuración de inicio de sesión o seleccione una existente. Puede volver a usar la configuración de inicio de sesión para otras bases de datos con la misma SKU.

    ![Creación de un nuevo inicio de sesión en la base de datos](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Envíe el formulario y espere a que la implementación se complete.

    En la hoja resultante, observe el campo "Cadena de conexión". Puede usar esa cadena en cualquier aplicación que requiera acceso a SQL Server (por ejemplo, una aplicación web) en Azure Stack.

    ![Recuperación de la cadena de conexión](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="add-capacity"></a>Ampliación de capacidad

Amplíe la capacidad incorporando hosts SQL adicionales en Azure Stack Portal y asócielos a una SKU apropiada. Si desea utilizar otra instancia de SQL en lugar de la instalada en la máquina virtual del proveedor, haga clic en **Proveedores de recursos** &gt; **SQLAdapter** &gt; **SQL Hosting Servers** (Servidores de hospedaje SQL) &gt; **+ Agregar**.

## <a name="making-sql-databases-available-to-tenants"></a>Bases de datos SQL a disposición de los inquilinos

Cree planes y ofertas para poner las bases de datos SQL a disposición de los inquilinos. Debe crear un plan, agregarle el servicio Microsoft.SqlAdapter y agregar una cuota existente o crear una nueva. Si crea una cuota, puede especificar la capacidad permitida para el inquilino.
    ![Creación de planes y ofertas para incluir bases de datos](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="tenant-usage-of-the-resource-provider"></a>Uso de inquilinos del proveedor de recursos

Las bases de datos de autoservicio se proporcionan a través del portal de inquilinos.

## <a name="removing-the-sql-adapter-resource-provider"></a>Eliminación del proveedor de recursos del adaptador de SQL

Para quitar el proveedor de recursos, es esencial quitar primero todas las dependencias.

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del proveedor de recursos.

2. Todas las bases de datos de inquilino deben eliminarse del proveedor de recursos (así no se eliminarán los datos). Los propios inquilinos deben hacerlo.

3. El administrador debe eliminar los servidores de hospedaje del adaptador de SQL.

4. El administrador debe eliminar los planes que hagan referencia al adaptador de SQL.

5. El administrador debe eliminar las SKU y las cuotas asociadas al adaptador de SQL.

6. Vuelva a ejecutar el script de implementación con el parámetro -Uninstall, los puntos de conexión de Azure Resource Manager, DirectoryTenantID y las credenciales para la cuenta de administrador del servicio.



## <a name="next-steps"></a>Pasos siguientes


Pruebe otros [servicios PaaS](azure-stack-tools-paas-services.md), como el [proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md) y el [proveedor de recursos de App Services](azure-stack-app-service-overview.md).

