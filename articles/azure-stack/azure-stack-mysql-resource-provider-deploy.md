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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 28ceb7345c0d74e2a7d7911d5b4bf24a0ceb214a
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bases de datos MySQL en Microsoft Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Puede implementar un proveedor de recursos MySQL en Azure Stack. Después de implementar el proveedor de recursos, puede crear servidores y bases de datos MySQL mediante plantillas de implementación de Azure Resource Manager y proporcionar las bases de datos MySQL como servicio. Las bases de datos MySQL, que son comunes en los sitios web, admiten muchas plataformas de sitio web. Por ejemplo, después de implementar el proveedor de recursos, puede crear sitios web de WordPress desde el complemento de plataforma como servicio (PaaS) Azure Web Apps para Azure Stack.

Para implementar el proveedor de MySQL en un sistema que no tiene acceso a Internet, puede copiar el archivo [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) a un recurso compartido local. A continuación, proporcione ese nombre de recurso compartido cuando se le solicite. También debe instalar los módulos PowerShell de Azure y Azure Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Arquitectura de adaptador del proveedor de recursos MySQL Server

El proveedor de recursos se compone de tres componentes:

- **La VM de adaptador del proveedor de recursos MySQL**, que es una máquina virtual con Windows que ejecuta los servicios del proveedor.
- **El proveedor de recursos en sí**, que procesa las solicitudes de aprovisionamiento y expone los recursos de las bases de datos.
- **Los servidores que hospedan MySQL Server**, que proporcionan capacidad para las bases de datos, conocidos como servidores host.

Esta versión ya no crea una instancia de MySQL. Debe crearlas o proporcionar acceso a instancias externas de SQL. Visite la [Galería de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) para obtener una plantilla de ejemplo que pueda:
- Crear un servidor MySQL en su lugar
- Descargar e implementar una instancia de MySQL Server desde Marketplace.

![NOTA] Los servidores de hospedaje instalados en una instancia de Azure Stack de varios nodos debe crearse desde una suscripción de inquilino. No se pueden crear desde la suscripción de proveedor predeterminado. Es decir, se deben crear desde el portal de inquilino o desde una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias adecuadas. El administrador de servicios puede ser el propietario de dicha suscripción.

### <a name="required-privileges"></a>Privilegios necesarios
Esta cuenta del sistema debe tener los siguientes privilegios:

1.  Base de datos: crear, eliminar
2.  Inicio de sesión: crear, establecer, eliminar, conceder, revocar

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 Datacenter Core, que puede descargarse desde Administración de Marketplace. Debe utilizar una imagen de Windows Server 2016 Core. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). El entorno de tiempo de ejecución de .NET 3.5 ya no es necesario.


2. Inicie sesión en un host que pueda tener acceso a la máquina virtual de punto de conexión con privilegios.

    a. En las instalaciones de Azure Stack Development Kit (ASDK), inicie sesión en el host físico.

    b. En los sistemas de varios nodos, el host debe ser un sistema que pueda tener acceso al punto de conexión con privilegios.

3. [Descargue el archivo de binarios del proveedor de recursos MySQL](https://aka.ms/azurestackmysqlrp) y ejecute el extractor automático para extraer el contenido en un directorio temporal.

4.  El certificado raíz de Azure Stack se recupera desde el punto de conexión con privilegios. Para ASDK, como parte de este proceso se crea un certificado autofirmado. Para varios nodos, debe proporcionar un certificado adecuado.

    Si tiene que proporcionar su propio certificado, es necesario el siguiente certificado:

    Un certificado comodín para \*.dbadapter.\<región\>.\<fqdn externo\>. Este certificado debe ser de confianza, como podría ser emitido por una entidad de certificación. Es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios. Se puede usar un solo certificado de sitio con el nombre explícito de la máquina virtual [mysqladapter] utilizado durante la instalación.


5. Abra una consola de PowerShell con privilegios elevados **nueva** (administrativos) y cambie al directorio al que extrajo los archivos. Use una nueva ventana para evitar problemas que puedan surgir de los módulos de PowerShell incorrectos que ya están cargados en el sistema.

6. [Instale Azure PowerShell versión 1.2.11](azure-stack-powershell-install.md).

7. Ejecute el script DeploySqlProvider.ps1.

El script sigue estos pasos:

* Descargue el archivo binario del conector de MySQL (puede proporcionarse sin conexión).
* Cargue los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
* Publique los paquetes de la galería para poder implementar las bases de datos SQL a través de ella.
* Publique un paquete de galería para implementar los servidores de hospedaje.
* Implemente una máquina virtual con la imagen de Windows Server 2016 que creó en el paso 1 e instale el proveedor de recursos.
* Registre un registro DNS local que se asigne a la máquina virtual del proveedor de recursos.
* Registre el proveedor de recursos en Azure Resource Manager local (Inquilino y Administrador).


Puede:
- Especificar al menos los parámetros necesarios en la línea de comandos
- O bien, si la ejecución se realiza sin ningún parámetro, escribirlos cuando se le solicite.

Este es un ejemplo que puede ejecutar desde el símbolo del sistema de PowerShell (pero cambie la información de la cuenta y la contraseña según sea necesario):


```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = 'AzureStack'
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
.$tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint '10.10.10.10' `
  -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```

### <a name="deploymysqlproviderps1-parameters"></a>Parámetros de DeployMySqlProvider.ps1

Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pide que proporcione los requeridos.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | La credencial del administrador de nube, necesaria para el acceso al punto de conexión con privilegios. | _obligatorio_ |
| **AzCredential** | Proporcione las credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _obligatorio_ |
| **VMLocalCredential** | Defina las credenciales para la cuenta de administrador local de la máquina virtual del proveedor de recursos MySQL. | _obligatorio_ |
| **PrivilegedEndpoint** | Proporcione la dirección IP o el nombre DNS del punto de conexión con privilegios. |  _obligatorio_ |
| **DependencyFilesLocalPath** | Ruta de acceso a un recurso compartido local que contiene [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Si se proporciona uno, el archivo de certificados debe colocarse también en este directorio. | _opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | La contraseña para el certificado .pfx | _requerido_ |
| **MaxRetryCount** | Defina el número de veces que desea volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Defina el tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes) | No |
| **DebugMode** | Impide la limpieza automática en caso de error. | No |
| **AcceptLicense** | Omite el símbolo del sistema para aceptar la licencia de GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


Dependiendo del rendimiento del sistema y de las velocidades de descarga, la instalación puede tardar tan solo 20 minutos o hasta varias horas. Si la hoja MySQLAdapter no está disponible, actualice el portal de administración.

> [!NOTE]
> Si la instalación tarda más de 90 minutos, se puede producir un error y verá un mensaje de error en la pantalla y en el archivo de registro. Se vuelve a intentar la implementación desde el paso que da error. Es posible que los sistemas que no cumplan las especificaciones recomendadas de memoria y vCPU no puedan implementar el RP MySQL.



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

> [!NOTE]
>  Una vez completado el script de instalación, deberá actualizar el portal para ver la hoja de administración.


1. Inicie sesión en el portal de administración como administrador de servicios.

2. Compruebe que la implementación se realizó correctamente. Busque **Grupos de recursos** &gt;, haga clic en el grupo de recursos **system.\<location\>.mysqladapter** y compruebe que las cuatro implementaciones se realizaron correctamente.

      ![Comprobación de la implementación de RP de MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Proporcionar capacidad conectando con un servidor host MySQL

1. Inicie sesión en el portal de Azure Stack como administrador de servicios.

2. Vaya a **RECURSOS ADMINISTRATIVOS** &gt; **MySQL Hosting Servers** (Servidores de hospedaje de MySQL) &gt; **+ Agregar**.

    En la hoja **MySQL Hosting Servers** (Servidores host de MySQL) puede conectar el proveedor de recursos del servidor MySQL a instancias reales de MySQL Server que actúan como back-end del proveedor de recursos.

    ![Servidores host](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Rellene el formulario con los detalles de conexión de la instancia de MySQL Server. Proporcione el nombre de dominio completo (FQDN) o una dirección IPv4 válida, y no el nombre corto de la VM. Esta instalación ya no proporciona una instancia de MySQL predeterminada. El tamaño especificado ayuda al proveedor de recursos a administrar la capacidad de la base de datos. Debe ser similar a la capacidad física del servidor de bases de datos.

    > [!NOTE]
    > Siempre que Azure Resource Manager del inquilino y del administrador tenga acceso a la instancia de MySQL, puede ponerse bajo el control del proveedor de recursos. La instancia de MySQL __debe__ asignase exclusivamente al RP.

4. A medida que agregue servidores, debe asignarlos a una SKU nueva o existente para permitir la diferenciación de las ofertas de servicio.
  Por ejemplo, puede tener una instancia empresarial que proporciona:
    - funcionalidad de base de datos
    - copia de seguridad automática
    - reserva de servidores de alto rendimiento para departamentos individuales
    - etcétera.
    El nombre de SKU debe reflejar las propiedades para que los inquilinos puedan colocar sus bases de datos de forma adecuada. Todos los servidores de hospedaje en una SKU deben tener las mismas funcionalidades.

    ![Crear una SKU de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
Las SKU pueden tardar hasta una hora en estar visibles en el portal. No puede crear una base de datos hasta que se cree la SKU.


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>Para probar su implementación, cree la primera base de datos MySQL


1. Inicie sesión en el portal de Azure Stack como administrador de servicios.

2. Haga clic en el botón **+ Nuevo** &gt; **Datos y almacenamiento** &gt; **Base de datos MySQL**.

3. Rellene el formulario con los detalles de la base de datos.

    ![Crear una base de datos MySQL de prueba](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Seleccione una SKU.

    ![Seleccionar una SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Cree una configuración de inicio de sesión. Se puede reutilizar la configuración de inicio de sesión o puede crearse una nueva. Esta opción contiene el nombre de usuario y la contraseña para la base de datos.

    ![Creación de un nuevo inicio de sesión en la base de datos](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    La cadena de conexión incluye el nombre real del servidor de bases de datos. Cópielo del portal.

    ![Obtener la cadena de conexión para la base de datos MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> La longitud de los nombres de usuario no puede superar los 32 caracteres con MySQL 5.7 ni los 16 caracteres en versiones anteriores.


## <a name="add-capacity"></a>Ampliación de capacidad

Para agregar capacidad, agregue servidores MySQL adicionales en el portal de Azure Stack. Pueden agregarse servidores adicionales a una SKU nueva o existente. Asegúrese de que las características de servidor son las mismas.


## <a name="making-mysql-databases-available-to-tenants"></a>Poner las bases de datos MySQL a disposición de los inquilinos
Cree planes y ofertas para poner las bases de datos MySQL a disposición de los inquilinos. Agregue el servicio Microsoft.MySqlAdapter, agregue una cuota, etc.

![Crear planes y ofertas para incluir las bases de datos](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="updating-the-administrative-password"></a>Actualización de la contraseña administrativa
Puede modificar la contraseña cambiándola la primera vez en la instancia del servidor MySQL. Vaya a **RECURSOS ADMINISTRATIVOS** &gt; **MySQL Hosting Servers** (Servidores de hospedaje de MySQL) &gt; y haga clic en el servidor de hospedaje. En el panel Configuración, haga clic en Contraseña.

![Actualización de la contraseña de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="removing-the-mysql-adapter-resource-provider"></a>Quitar el proveedor de recursos de adaptador de MySQL

Para quitar el proveedor de recursos, es esencial quitar antes todas las dependencias.

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del proveedor de recursos.

2. Todas las bases de datos de inquilino deben eliminarse del proveedor de recursos (no se eliminan los datos). Los propios inquilinos deben hacerlo.

3. Los inquilinos deben anular el registro del espacio de nombres.

4. El administrador debe eliminar los servidores host del adaptador de MySQL.

5. El administrador debe eliminar los planes que hacen referencia el adaptador de MySQL.

6. El administrador debe eliminar las cuotas asociadas al adaptador de MySQL.

7. Vuelva a ejecutar el script de implementación con el parámetro -Uninstall, los puntos de conexión de Azure Resource Manager, DirectoryTenantID y las credenciales para la cuenta de administrador del servicio.




## <a name="next-steps"></a>Pasos siguientes


Pruebe otros [servicios PaaS](azure-stack-tools-paas-services.md), como el [proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md) y el [proveedor de recursos de App Services](azure-stack-app-service-overview.md).
