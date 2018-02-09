---
title: Usar bases de datos MySQL como PaaS en Azure Stack | Microsoft Docs
description: "Aprenda cómo puede implementar el proveedor de recursos de MySQL y proporcionar bases de datos MySQL como servicio en Azure Stack."
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: mabrigg
ms.openlocfilehash: db7daf61fa80854c17b58252d7d6cb30c329dfb1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bases de datos MySQL en Microsoft Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede implementar un proveedor de recursos MySQL en Azure Stack. Después de implementar el proveedor de recursos, puede crear servidores y bases de datos MySQL mediante plantillas de implementación de Azure Resource Manager. También puede proporcionar bases de datos MySQL como servicio. 

Las bases de datos MySQL, que son comunes en los sitios web, admiten muchas plataformas de sitio web. Por ejemplo, después de implementar el proveedor de recursos, puede crear sitios web de WordPress desde el complemento de plataforma como servicio (PaaS) Web Apps para Azure Stack.

Para implementar el proveedor de MySQL en un sistema sin acceso a Internet, copie el archivo [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) en un recurso compartido local. A continuación, proporcione ese nombre de recurso compartido cuando se le solicite. Debe instalar los módulos PowerShell de Azure y Azure Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Arquitectura de adaptador del proveedor de recursos del servidor MySQL

El proveedor de recursos tiene tres componentes:

- **La VM del adaptador del proveedor de recursos de MySQL**, que es una máquina virtual Windows que ejecuta los servicios del proveedor.

- **El proveedor de recursos en sí**, que procesa las solicitudes de aprovisionamiento y expone los recursos de las bases de datos.

- **Los servidores que hospedan el servidor MySQL**, que proporcionan capacidad para las bases de datos, denominados servidores host.

Esta versión ya no crea instancias de MySQL. Esto significa que debe crearlas usted mismo o proporcionar acceso a instancias de SQL externas. Visite la [Galería de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) para obtener una plantilla de ejemplo que pueda:
- Crear un servidor MySQL en su lugar.
- Descargar e implementar un servidor MySQL desde Azure Marketplace.

> [!NOTE]
> Los servidores de hospedaje que se instalan en una implementación de Azure Stack de varios nodos deben crearse a partir de una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear desde el portal de inquilino o desde una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias adecuadas. El administrador del servicio puede ser el propietario de la suscripción de inquilino.

### <a name="required-privileges"></a>Privilegios necesarios
Esta cuenta del sistema debe tener los siguientes privilegios:

1.  Base de datos: crear, eliminar
2.  Inicio de sesión: crear, establecer, eliminar, conceder, revocar

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 Datacenter Core desde Marketplace Management (Administración de Marketplace). Debe utilizar una imagen de Windows Server 2016 Core. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Asegúrese de seleccionar la opción Core). El entorno de tiempo de ejecución de .NET 3.5 ya no es necesario.


2. Inicie sesión en un host que pueda tener acceso a la VM de punto de conexión con privilegios.

    - En las instalaciones del SDK de Azure Stack, inicie sesión en el host físico. 
    - En los sistemas de varios nodos, el host debe ser un sistema que pueda tener acceso al punto de conexión con privilegios.
    
    >[!NOTE]
    > El script se *debe* ejecutar en un sistema con Windows 10 o Windows Server 2016 con la versión más reciente del runtime de .NET instalada. De lo contrario, se produce un error de instalación. El host del SDK de Azure cumple este criterio.
    

3. Descargue el binario del proveedor de recursos de MySQL. A continuación, ejecute el autoextractor para extraer el contenido en un directorio temporal.

    >[!NOTE] 
    > La compilación del proveedor de recursos corresponde a las compilaciones de Azure Stack. Asegúrese de descargar el binario correcto para la versión de Azure Stack que se ejecuta.

    | Compilación de Azure Stack | Instalador de MySQL RP |
    | --- | --- |
    | 1.0.180102.3 o 1.0.180106.1 (varios nodos) | [MySQL RP, versión 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1.0.171122.1 | [MySQL RP, versión 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1.0.171028.1 | [MySQL RP, versión 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  El certificado raíz de Azure Stack se recupera del punto de conexión con privilegios. Para el SDK de Azure, como parte de este proceso, se crea un certificado autofirmado. Para varios nodos, debe proporcionar un certificado adecuado.

    Si tiene que proporcionar su propio certificado, coloque un archivo .pfx en **DependencyFilesLocalPath** que cumpla los criterios siguientes:

    - Un certificado comodín para \*.dbadapter.\<región\>.\<FQDN externo\> o un certificado de entidad de certificación único con un nombre común de mysqladapter.dbadapter.\<región\>.\<FQDN externo\>.

    - Este certificado debe ser de confianza. Es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios.

    - En DependencyFilesLocalPath solo existe un archivo de certificado individual.
    
    - El nombre de archivo no debe contener caracteres especiales.


5. Abra una **nueva** consola de PowerShell con privilegios elevados (administrativos). A continuación, cambie al directorio desde el que extrajo los archivos. Use una nueva ventana para evitar problemas que puedan surgir de los módulos de PowerShell incorrectos que ya están cargados en el sistema.

6. [Instale Azure PowerShell versión 1.2.11](azure-stack-powershell-install.md).

7. Ejecute el script `DeployMySqlProvider.ps1`.

El script sigue estos pasos:

* Descarga el binario del conector de MySQL (puede proporcionarse sin conexión).
* Carga los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
* Publica los paquetes de la galería para poder implementar las bases de datos SQL a través de esta.
* Publica un paquete de galería para implementar los servidores de hospedaje.
* Implementa una VM con la imagen de Windows Server 2016 que creó en el paso 1. También instala el proveedor de recursos.
* Registra un registro de DNS local que se asigna a la VM del proveedor de recursos.
* Registra el proveedor de recursos en la instancia local de Azure Resource Manager (inquilino y administrador).


Puede:
- Especificar los parámetros necesarios en la línea de comandos.
- Ejecutar sin parámetros y escribirlos cuando se le solicite.

Este es un ejemplo que se puede ejecutar desde el símbolo del sistema de PowerShell. Asegúrese de cambiar la información de la cuenta y las contraseñas según sea necesario:


```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure SDK, the default is AzureStack, and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros requeridos.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** | Las credenciales para la cuenta de administrador local de la VM del proveedor de recursos de MySQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **DependencyFilesLocalPath** | Ruta de acceso a un recurso compartido local que contiene [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Si se proporciona una de estas rutas de acceso, el archivo de certificados debe colocarse también en este directorio. | _Opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | Número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |
| **AcceptLicense** | Omite el aviso para aceptar la licencia GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |



Dependiendo del rendimiento del sistema y de las velocidades de descarga, la instalación puede tardar tan solo 20 minutos o hasta varias horas. Si la hoja **MySQLAdapter** no está disponible, actualice el portal de administración.

> [!NOTE]
> Si la instalación tarda más de 90 minutos, tal vez genere errores. Si lo hace, verá un mensaje de error en la pantalla y en el archivo de registro. Se vuelve a intentar la implementación desde el paso que da error. Es posible que los sistemas que no cumplan las especificaciones recomendadas de memoria y núcleos no puedan implementar el proveedor de recursos de MySQL.



## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

> [!NOTE]
>  Cuando finalice la ejecución del script de instalación, deberá actualizar el portal para ver la hoja de administración.


1. Inicie sesión en el portal de administración como administrador de servicios.

2. Compruebe que la implementación se realizó correctamente. Vaya a **Grupos de recursos** y seleccione el grupo de recursos **system.\<ubicación\>.mysqladapter**. Compruebe que las cuatro implementaciones se realizaron correctamente.

      ![Comprobación de la implementación del proveedor de recursos de MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Proporcionar capacidad conectando con un servidor host MySQL

1. Inicie sesión en el portal de Azure Stack como administrador de servicios.

2. Vaya a **RECURSOS ADMINISTRATIVOS** > **MySQL Hosting Servers** (Servidores de hospedaje MySQL) > **+Agregar**.

    En la hoja **MySQL Hosting Servers** (Servidores de hospedaje MySQL), puede conectar el proveedor de recursos del servidor MySQL a instancias reales del servidor MySQL que actúan como back-end del proveedor de recursos.

    ![Servidores de hospedaje](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Proporcione los detalles de conexión de la instancia del servidor MySQL. Asegúrese de proporcionar el nombre de dominio completo (FQDN) o una dirección IPv4 válida, y no el nombre corto de la VM. Esta instalación ya no proporciona una instancia de MySQL predeterminada. El tamaño especificado ayuda al proveedor de recursos a administrar la capacidad de la base de datos. Debe ser similar a la capacidad física del servidor de bases de datos.

    > [!NOTE]
    >Si Azure Resource Manager del inquilino y del administrador tiene acceso a la instancia de MySQL, puede ponerse bajo el control del proveedor de recursos. La instancia de MySQL *debe* asignarse exclusivamente al proveedor de recursos.

4. A medida que agregue servidores, debe asignarlos a una SKU nueva o existente para permitir la diferenciación de las ofertas de servicio.
  Por ejemplo, puede tener una instancia empresarial que proporciona:
    - Funcionalidad de base de datos
    - Copia de seguridad automática
    - Reserva de servidores de alto rendimiento para departamentos individuales
 

El nombre de SKU debe reflejar las propiedades para que los inquilinos puedan colocar sus bases de datos de forma adecuada. Todos los servidores de hospedaje en una SKU deben tener las mismas funcionalidades.

![Crear una SKU de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> Las SKU pueden tardar hasta una hora en estar visibles en el portal. No puede crear una base de datos hasta que se cree la SKU.


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Probar la implementación mediante la creación de su primera base de datos MySQL


1. Inicie sesión en el portal de Azure Stack como administrador de servicios.

2. Seleccione **+Nuevo** > **Datos y almacenamiento** > **Base de datos MySQL**.

3. Proporcione los detalles de la base de datos.

    ![Crear una base de datos MySQL de prueba](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Seleccione una SKU.

    ![Seleccionar una SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Cree una configuración de inicio de sesión. Puede reutilizar una configuración de inicio de sesión existente o crear una nueva. Esta opción contiene el nombre de usuario y la contraseña para la base de datos.

    ![Creación de un nuevo inicio de sesión en la base de datos](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    La cadena de conexión incluye el nombre real del servidor de bases de datos. Cópielo del portal.

    ![Obtener la cadena de conexión para la base de datos MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> La longitud de los nombres de usuario no puede superar los 32 caracteres en MySQL 5.7. En versiones anteriores, no puede superar los 16 caracteres.


## <a name="add-capacity"></a>Ampliación de capacidad

Para agregar capacidad, agregue servidores MySQL adicionales en el portal de Azure Stack. Pueden agregarse servidores adicionales a una SKU nueva o existente. Asegúrese de que las características de servidor son las mismas.


## <a name="make-mysql-databases-available-to-tenants"></a>Poner las bases de datos MySQL a disposición de los inquilinos
Cree planes y ofertas para poner las bases de datos MySQL a disposición de los inquilinos. Por ejemplo, agregue el servicio Microsoft.MySqlAdapter, agregue una cuota, etc.

![Crear planes y ofertas para incluir las bases de datos](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Actualizar la contraseña administrativa
Puede modificar la contraseña cambiándola la primera vez en la instancia del servidor MySQL. Seleccione **RECURSOS ADMINISTRATIVOS** > **MySQL Hosting Servers** (Servidores de hospedaje MySQL). A continuación, seleccione el servidor de hospedaje. En el panel **Configuración**, seleccione **Contraseña**.

![Actualización de la contraseña de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Actualización del adaptador del proveedor de recursos de MySQL (solo varios nodos; compilaciones 1710 y posteriores)
Cada vez que se actualiza la compilación de Azure Stack, se publica un nuevo adaptador del proveedor de recursos de MySQL. Puede que el adaptador existente siga funcionando. Sin embargo, se recomienda actualizar a la compilación más reciente lo antes posible tras la actualización de Azure Stack. 

El proceso de actualización es similar al proceso de instalación que se ha descrito anteriormente. Se crea una nueva VM con el código del proveedor de recursos más reciente. A continuación, se migra la configuración a esta nueva instancia, incluida la información de la base de datos y del servidor de hospedaje. También se migra el registro DNS necesario.

Use el script UpdateMySQLProvider.ps1 con los mismos argumentos que hemos descrito antes. Proporcione aquí también el certificado.

> [!NOTE]
> La actualización solo se admite en sistemas de varios nodos.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>Parámetros de UpdateMySQLProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros requeridos.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** |Credenciales para la cuenta de administrador local de la VM del proveedor de recursos de SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar también en este directorio. | _Opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | La contraseña para el certificado .pfx | _Obligatorio_ |
| **MaxRetryCount** | Número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |
| **AcceptLicense** | Omite el aviso para aceptar la licencia GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |

## <a name="remove-the-mysql-resource-provider-adapter"></a>Quitar el adaptador del proveedor de recursos de MySQL

Para quitar el proveedor de recursos, es esencial quitar antes todas las dependencias.

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del proveedor de recursos.

2. Todas las bases de datos de inquilino deben eliminarse del proveedor de recursos. (Eliminar las bases de datos de inquilino no elimina los datos). Los propios inquilinos deben hacer esta tarea.

3. Los inquilinos deben anular el registro del espacio de nombres.

4. El administrador debe eliminar los servidores de hospedaje del adaptador de MySQL.

5. El administrador debe eliminar los planes que hacen referencia el adaptador de MySQL.

6. El administrador debe eliminar cualquier cuota asociada al adaptador de MySQL.

7. Vuelva a ejecutar el script de implementación con los siguientes elementos:
    - Parámetro -Uninstall
    - Puntos de conexión de Azure Resource Manager
    - DirectoryTenantID
    - Credenciales para la cuenta de administrador del servicio
