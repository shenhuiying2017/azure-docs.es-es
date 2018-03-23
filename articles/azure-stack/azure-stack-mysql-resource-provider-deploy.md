---
title: Usar bases de datos MySQL como PaaS en Azure Stack | Microsoft Docs
description: Aprenda cómo puede implementar el proveedor de recursos de MySQL y proporcionar bases de datos MySQL como servicio en Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 15a1648193555ecc5847170ab65f48dfa4f6417b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
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
> Los servidores de hospedaje que se instalan en sistemas integrados de Azure Stack se deben crear mediante una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear desde el portal de inquilino o desde una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias adecuadas. El administrador del servicio puede ser el propietario de la suscripción de inquilino.

### <a name="required-privileges"></a>Privilegios necesarios
Esta cuenta del sistema debe tener los siguientes privilegios:

1.  Base de datos: crear, eliminar
2.  Inicio de sesión: crear, establecer, eliminar, conceder, revocar

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 Datacenter Core desde Marketplace Management (Administración de Marketplace). Debe utilizar una imagen de Windows Server 2016 Core. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Asegúrese de seleccionar la opción Core).


2. Inicie sesión en un host que pueda tener acceso a la VM de punto de conexión con privilegios.

    - En las instalaciones del SDK de Azure Stack, inicie sesión en el host físico. 
    - En los sistemas de varios nodos, el host debe ser un sistema que pueda tener acceso al punto de conexión con privilegios.
    
    >[!NOTE]
    > El script se *debe* ejecutar en un sistema con Windows 10 o Windows Server 2016 con la versión más reciente del runtime de .NET instalada. De lo contrario, se produce un error de instalación. El host del SDK de Azure Stack cumple este criterio.
    

3. Descargue el binario del proveedor de recursos de MySQL. A continuación, ejecute el autoextractor para extraer el contenido en un directorio temporal.

    >[!NOTE] 
    > El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack. Asegúrese de descargar el binario correcto para la versión de Azure Stack que se ejecuta.

    | Compilación de Azure Stack | Instalador de MySQL RP |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP, versión 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 o 1.0.180106.1 (varios nodos) | [MySQL RP, versión 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP, versión 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP, versión 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  El certificado raíz de Azure Stack se recupera del punto de conexión con privilegios. Para el SDK de Azure, como parte de este proceso, se crea un certificado autofirmado. Para varios nodos, debe proporcionar un certificado adecuado.

    Si tiene que proporcionar su propio certificado, coloque un archivo .pfx en **DependencyFilesLocalPath** que cumpla los criterios siguientes:

    - Un certificado comodín para \*.dbadapter.\<región\>.\<FQDN externo\> o un certificado de entidad de certificación único con un nombre común de mysqladapter.dbadapter.\<región\>.\<FQDN externo\>.

    - Este certificado debe ser de confianza. Es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios.

    - En DependencyFilesLocalPath solo existe un archivo de certificado individual.
    
    - El nombre de archivo no debe contener caracteres especiales o espacios.


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

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
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


### <a name="deploymysqlproviderps1-parameters"></a>Parámetros de DeployMySqlProvider.ps1
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
Cuando las compilaciones de Azure Stack se actualicen, podría lanzarse un nuevo adaptador del proveedor de recursos de Azure SQL. Aunque el adaptador existente continúa funcionando, se recomienda actualizar a la compilación más reciente lo antes posible. 

Para actualizar el proveedor de recursos, use el script *UpdateMySQLProvider.ps1*. El proceso es similar al usado para instalar un proveedor de recursos, como se describe en la sección [Implementar el proveedor de recursos](#deploy-the-resource-provider) de este artículo. El script se incluye con la descarga del proveedor de recursos.

El script *UpdateMySQLProvider.ps1* crea una nueva máquina virtual con el código del proveedor de recursos más reciente y migra la configuración de la máquina virtual antigua a la nueva. La configuración que se migra incluye información de base de datos y del servidor de hospedaje, así como el registro DNS necesario.

El script requiere el uso de los mismos argumentos que se describen para el script DeployMySqlProvider.ps1. Proporcione aquí también el certificado. 

Este es un ejemplo del script *UpdateMySQLProvider.ps1* que puede ejecutar desde el símbolo del sistema de PowerShell. Asegúrese de cambiar la información de la cuenta y las contraseñas según sea necesario: 

> [!NOTE]
> El proceso de actualización solo se aplica a sistemas integrados.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

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


## <a name="collect-diagnostic-logs"></a>Recopilación de registros de diagnóstico
El proveedor de recursos de MySQL es una máquina virtual bloqueada. Si es necesario recopilar registros de la máquina virtual, se proporciona para tal fin un punto de conexión _DBAdapterDiagnostics_ de JEA (Just Enough Administration) de PowerShell. Hay dos comandos disponibles a través de este punto de conexión:

* Get-AzsDBAdapterLog: prepara un paquete ZIP que contiene registros de diagnóstico del proveedor de recursos y lo coloca en la unidad de usuario de la sesión. El comando se puede invocar sin ningún parámetro y recopila las últimas cuatro horas de registros.
* Remove-AzsDBAdapterLog: limpia los paquetes de registros existentes en la máquina virtual del proveedor de recursos.

Se crea una cuenta de usuario llamada _dbadapterdiag_ durante la implementación o la actualización del proveedor de recursos para conectar con el punto de conexión de diagnóstico y extraer los registros del proveedor de recursos. La contraseña de esta cuenta es la misma que la proporcionada para la cuenta de administrador local durante la implementación o actualización.

Para usar estos comandos, debe crear una sesión remota de PowerShell a la máquina virtual del proveedor de recursos e invocar el comando. También puede proporcionar los parámetros FromDate y ToDate. Si no especifica uno o ninguno de estos parámetros, el valor de FromDate será cuatro horas antes de la hora actual y el valor de ToDate será la hora actual.

Este script de ejemplo muestra el uso de estos comandos:

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>Operaciones de mantenimiento (sistemas integrados)
El proveedor de recursos MySQL es una máquina virtual bloqueada. La actualización de la seguridad de la máquina virtual del proveedor de recursos se puede realizar mediante el punto de conexión _DBAdapterMaintenance_ de JEA (Just Enough Administration) de PowerShell.

Para facilitar estas operaciones, se proporciona un script con el paquete de instalación del proveedor de recursos.


### <a name="update-the-virtual-machine-operating-system"></a>Actualización del sistema operativo de la máquina virtual
Hay varias maneras de actualizar la máquina virtual Windows Server:
* Instalar el paquete más reciente del proveedor de recursos mediante una imagen de Windows Server 2016 Core actualmente revisada
* Instalar un paquete de Windows Update durante la instalación o actualización del proveedor de recursos


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Actualización de las definiciones de Windows Defender de máquina virtual

Siga estos pasos para actualizar las definiciones de Defender:

1. Descargue la actualización de las definiciones de Windows Defender desde [esta página](https://www.microsoft.com/en-us/wdsi/definitions).

    En esa página, en "Manually download and install the definitions" (Descargar e instalar manualmente las definiciones), descargue el archivo de 64 bits "Windows Defender Antivirus for Windows 10 and Windows 8.1" (Windows Defender Antivirus para Windows 10 y Windows 8.1). 
    
    Vínculo directo: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Cree una sesión de PowerShell al punto de conexión de mantenimiento de la máquina virtual del adaptador del proveedor de recursos MySQL.
3. Copie el archivo de actualización de definiciones en la máquina del adaptador de base de datos mediante la sesión del punto de conexión de mantenimiento.
4. En la sesión de PowerShell de mantenimiento, invoque el comando _Update-DBAdapterWindowsDefenderDefinitions_.
5. Después de la instalación, se recomienda quitar el archivo de actualización de definiciones usado. Se puede quitar en la sesión de mantenimiento mediante el comando _Remove-ItemOnUserDrive)_.


Este es un script de ejemplo para actualizar las definiciones de Defender (sustituya la dirección o el nombre de la máquina virtual por el valor real):

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


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
