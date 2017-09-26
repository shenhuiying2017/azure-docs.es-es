---
title: Usar bases de datos MySQL como PaaS en Azure Stack | Microsoft Docs
description: "Aprenda cómo puede implementar el proveedor de recursos de MySQL y proporcionar bases de datos MySQL como servicio en Azure Stack"
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
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 45c7edcc645e82107805b3e62d87655a830fb22a
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bases de datos MySQL en Microsoft Azure Stack


Puede implementar un proveedor de recursos MySQL en Azure Stack. Después de implementar el proveedor de recursos, puede crear servidores y bases de datos MySQL mediante plantillas de implementación de Azure Resource Manager y proporcionar las bases de datos MySQL como servicio. Las bases de datos MySQL, que son comunes en los sitios web, admiten muchas plataformas de sitio web. Por ejemplo, después de implementar el proveedor de recursos, puede crear sitios web de WordPress desde el complemento de plataforma como servicio (PaaS) Azure Web Apps para Azure Stack.

Para implementar el proveedor de MySQL en un sistema que no tiene acceso a Internet, puede copiar el archivo [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) a un recurso compartido local y proporcionar ese nombre de recurso compartido cuando se le solicite (consulte la información que encontrará a continuación). También debe instalar los módulos PowerShell de Azure y Azure Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Arquitectura de adaptador del proveedor de recursos MySQL Server

El proveedor de recursos se compone de tres componentes:

- **La VM de adaptador del proveedor de recursos MySQL**, que es una máquina virtual con Windows que ejecuta los servicios del proveedor.
- **El proveedor de recursos en sí**, que procesa las solicitudes de aprovisionamiento y expone los recursos de las bases de datos.
- **Los servidores que hospedan MySQL Server**, que proporcionan capacidad para las bases de datos, conocidos como servidores host. 

Esta versión ya no crea una instancia de MySQL. Debe crearlas o proporcionar acceso a instancias externas de SQL. Puede visitar la [galería de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) para ver una plantilla de ejemplo que puede crear un servidor MySQL por usted, o puede descargar e implementar un servidor MySQL Server de Marketplace.

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 Datacenter - Eval, que puede descargarse desde la administración de Marketplace. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image).

2. [Descargue el archivo de binarios del proveedor de recursos MySQL](https://aka.ms/azurestackmysqlrp) y extráigalo en el host del kit de desarrollo.

3. Inicie sesión en el host del kit de desarrollo y extraiga el archivo de instalador del RP MySQL en un directorio temporal.

4. Se recupera el certificado raíz de Azure Stack, y se crea un certificado autofirmado como parte de este proceso. 

    __Opcional:__ Si tiene que proporcionar los propios, prepare los certificados y cópielos a un directorio local si quiere personalizar los certificados (que se pasan al script de instalación). Necesita lo siguiente:

    a. Un certificado comodín para *.dbadapter.\<región\>.\<fqdn externo\>. Este certificado debe ser de confianza, como podría ser uno emitido por una entidad de certificación (es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios). (Puede usarse un único certificado de sitio con el nombre explícito de VM que proporcione durante la instalación.)

    b. El certificado raíz utilizado por Azure Resource Manager para la instancia de Azure Stack. Si no se encuentra, se recuperará el certificado raíz.

5. Abra una **nueva** consola de PowerShell con privilegios elevados y cambie al directorio al que extrajo los archivos. Use una nueva ventana para evitar problemas que puedan surgir de los módulos de PowerShell incorrectos que ya están cargados en el sistema.

6. Si ha instalado alguna de las versiones de los módulos de AzureRm o AzureStack PowerShell que no sean 1.2.9 o 1.2.10, deberá quitarlas. De lo contrario, la instalación no continuará. Esto incluye las versiones 1.3 o posterior.

7. Ejecute DeployMySqlProvider.ps1.

Este script sigue estos pasos:

* Si es necesario, descargue una versión compatible de Azure PowerShell.
* Descargue el archivo binario del conector de MySQL (puede proporcionarse sin conexión).
* Cargue el certificado y todos los demás artefactos en una cuenta de almacenamiento de Azure Stack.
* Publique los paquetes de la galería para poder implementar las bases de datos MySQL a través de la galería.
* Implemente una máquina virtual (VM) que hospede al proveedor de recursos.
* Registre un registro de DNS local que se asigne a la VM del proveedor de recursos.
* Registre el proveedor de recursos en Azure Resource Manager local.

Especifique al menos los parámetros obligatorios en la línea de comandos o, si ejecuta sin ningún parámetro, se le pedirá que los escriba. 

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
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeployMySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "MySqlRG" -VmName "MySQLRP" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath
 ```

### <a name="deploymysqlproviderps1-parameters"></a>Parámetros de DeployMySqlProvider.ps1

Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pide que proporcione los requeridos.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado |
| --- | --- | --- |
| **DirectoryTenantID** | El identificador (GUID) de directorio de AD FS o Azure | _obligatorio_ |
| **ArmEndpoint** | El punto de conexión administrativo de Azure Resource Manager de Azure Stack | _obligatorio_ |
| **TenantArmEndpoint** | El punto de conexión de inquilino de Azure Resource Manager de Azure Stack | _obligatorio_ |
| **AzCredential** | Credencial de la cuenta de administrador de servicios de Azure Stack (use la misma cuenta que usó para implementar Azure Stack) | _obligatorio_ |
| **VMLocalCredential** | La cuenta de administrador local de la VM del proveedor de recursos MySQL | _obligatorio_ |
| **ResourceGroupName** | Grupo de recursos para los elementos creados por este script |  _obligatorio_ |
| **VmName** | Nombre de la VM que contiene el proveedor de recursos |  _obligatorio_ |
| **AcceptLicense** | Omite el símbolo del sistema para aceptar la licencia de GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |
| **DependencyFilesLocalPath** | Ruta de acceso a un recurso compartido local que contiene [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Si proporciona los archivos de certificados, se deben colocar también en este directorio. | _opcional_ |
| **DefaultSSLCertificatePassword** | La contraseña para el certificado .pfx | _requerido_ |
| **MaxRetryCount** | Se vuelve a intentar cada operación si se produce un error. | 2 |
| **RetryDuration** | Tiempo de expiración entre reintentos, en segundos | 120 |
| **Desinstalación** | Quita el proveedor de recursos. | No |
| **DebugMode** | Impide la limpieza automática en caso de error. | No |


Dependiendo del rendimiento del sistema y de las velocidades de descarga, la instalación puede tardar tan solo 20 minutos o hasta varias horas. Debe actualizar el portal de administración si la hoja MySQLAdapter no está disponible.

> [!NOTE]
> Si la instalación tarda más de 90 minutos, se puede producir un error y verá un mensaje de error en la pantalla y en el archivo de registro. Se vuelve a intentar la implementación desde el paso que da error. Es posible que los sistemas que no cumplan las especificaciones recomendadas de memoria y núcleos no puedan implementar el RP MySQL.


## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Proporcionar capacidad conectando con un servidor host MySQL

1. Inicie sesión en el portal de Azure Stack como administrador de servicios.

2. Haga clic en **Proveedores de recursos** &gt; **MySQLAdapter** &gt; **Hosting Servers** (Servidores host)&gt; **+Agregar**.

    En la hoja **MySQL Hosting Servers** (Servidores host de MySQL) puede conectar el proveedor de recursos del servidor MySQL a instancias reales de MySQL Server que actúan como back-end del proveedor de recursos.

    ![Servidores host](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Rellene el formulario con los detalles de conexión de la instancia de MySQL Server. Proporcione el nombre de dominio completo (FQDN) o una dirección IPv4 válida, y no el nombre corto de la VM. Esta instalación ya no proporciona una instancia de MySQL predeterminada. El tamaño especificado ayuda al proveedor de recursos a administrar la capacidad de la base de datos. Debe ser similar a la capacidad física del servidor de bases de datos.

    > [!NOTE]
    > Siempre que Azure Resource Manager del inquilino y del administrador tenga acceso a la instancia de MySQL, puede ponerse bajo el control del proveedor de recursos. La instancia de MySQL __debe__ asignase exclusivamente al RP.

4. A medida que agregue servidores, debe asignarlos a una SKU nueva o existente para permitir la diferenciación de las ofertas de servicio. Por ejemplo, podría tener una instancia empresarial que proporcione capacidad de base de datos y copia de seguridad automática, reservar servidores de alto rendimiento para departamentos individuales, etc. El nombre de la SKU debe reflejar las propiedades para que los inquilinos puedan colocar sus bases de datos correctamente, y todos los servidores host en una SKU deben tener la misma funcionalidad.


>[!NOTE]
Las SKU pueden tardar hasta una hora en estar visibles en el portal. No puede crear una base de datos hasta que se cree la SKU.


## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Crear la primera base de datos MySQL para probar la implementación


1. Inicie sesión en el portal de Azure Stack como administrador de servicios.

2. Haga clic en el botón **+ Nuevo** &gt; **Data + Storage** (Datos y almacenamiento)&gt; **Base de datos MySQL (versión preliminar)**.

3. Rellene el formulario con los detalles de la base de datos.

    ![Crear una base de datos MySQL de prueba](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Seleccione una SKU.

    ![Seleccionar una SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Cree una configuración de inicio de sesión. Se puede reutilizar la configuración de inicio de sesión o puede crearse una nueva. Contiene el nombre de usuario y la contraseña para la base de datos.

    La cadena de conexión incluye el nombre real del servidor de bases de datos. Cópielo del portal.

    ![Obtener la cadena de conexión para la base de datos MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> La longitud de los nombres de usuario no puede superar los 32 caracteres con MySQL 5.7 ni los 16 caracteres en versiones anteriores. Se trata de una limitación de las implementaciones de MySQL.


## <a name="add-capacity"></a>Agregar capacidad

Para agregar capacidad, agregue servidores MySQL adicionales en el portal de Azure Stack. Si quiere utilizar otra instancia de MySQL, haga clic en **Proveedores de recursos** &gt; **MySQLAdapter** &gt; **MySQL Hosting Servers** &gt; (Servidores host MySQL) **+Agregar**.


## <a name="making-mysql-databases-available-to-tenants"></a>Poner las bases de datos MySQL a disposición de los inquilinos
Cree planes y ofertas para poner las bases de datos MySQL a disposición de los inquilinos. Agregue el servicio Microsoft.MySqlAdapter, agregue una cuota, etc.

![Crear planes y ofertas para incluir las bases de datos](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="removing-the-mysql-adapter-resource-provider"></a>Quitar el proveedor de recursos de adaptador de MySQL

Para quitar el proveedor de recursos, es esencial quitar antes todas las dependencias.

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del proveedor de recursos.

2. Todas las bases de datos de inquilino deben eliminarse del proveedor de recursos (así no se eliminarán los datos). Esto deben realizarlo los inquilinos mismos.

3. Los inquilinos deben anular el registro del espacio de nombres.

4. El administrador debe eliminar los servidores host del adaptador de MySQL.

5. El administrador debe eliminar los planes que hacen referencia el adaptador de MySQL.

6. El administrador debe eliminar las cuotas asociadas al adaptador de MySQL.

7. Vuelva a ejecutar el script de implementación con el parámetro -Uninstall, los puntos de conexión de Azure Resource Manager, DirectoryTenantID y las credenciales para la cuenta de administrador del servicio.




## <a name="next-steps"></a>Pasos siguientes


Pruebe otros [servicios PaaS](azure-stack-tools-paas-services.md), como el [proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md) y el [proveedor de recursos de App Services](azure-stack-app-service-overview.md).

