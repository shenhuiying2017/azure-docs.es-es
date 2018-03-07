---
title: " Administración del servidor de configuración para realizar la recuperación ante desastres del servidor físico con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo administrar un servidor de configuración existente para realizar la recuperación ante desastres del servidor físico en Azure con el servicio Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2018
ms.author: anoopkv
ms.openlocfilehash: 7fe68f072ef438e21f3e6d3d52aee9e86e537687
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Administración del servidor de configuración para la recuperación ante desastres del servidor físico

Un servidor de configuración local se configura cuando se usa el servicio [Azure Site Recovery](site-recovery-overview.md) para realizar la recuperación ante desastres de servidores físicos en Azure. El servidor de configuración coordina la comunicación entre las máquinas locales y Azure, además de administrar la replicación de datos. En este artículo se resumen las tareas comunes para administrar el servidor de configuración después de que se haya implementado.

## <a name="prerequisites"></a>requisitos previos

En esta tabla se resumen los requisitos previos para implementar la máquina del servidor de configuración local.

| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 12 GB|
| Número de discos | 3, incluidos el disco del sistema operativo, el disco de memoria caché del servidor de procesos, la unidad de retención para la conmutación por recuperación |
| Espacio libre en el disco (caché del servidor de procesos) | 600 GB
| Espacio libre en el disco (disco de retención) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Configuración regional del sistema operativo | Inglés (EE. UU.)|
| Versión de VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Roles de Windows Server | No habilite estos roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Directivas de grupo| No habilite estas directivas de grupo: <br> - Impedir el acceso al símbolo del sistema <br> - Impedir el acceso a herramientas de edición del Registro <br> - Confiar en la lógica de datos adjuntos de archivos <br> - Activar la ejecución de scripts <br> [Más información](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Ningún sitio web predeterminado debe existir previamente <br> - Habilitar la [Autenticación anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar la configuración de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Ningún sitio web o aplicación que escuche en el puerto 443 deben existir previamente<br>|
| Tipo de NIC | VMXNET3 (cuando se implementa como una máquina virtual VMware) |
| Tipo de dirección IP | estática |
| Acceso a Internet | El servidor necesita acceder a estas direcciones URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (no es necesario para servidores de procesos de escalado horizontal) <br> - time.nist.gov <br> - time.windows.com |
| Puertos | 443 (orquestación del canal de control)<br>9443 (Transporte de datos)|

## <a name="download-the-latest-installation-file"></a>Descargue el archivo de instalación más reciente.

La versión más reciente del archivo de instalación del servidor de configuración está disponible en el portal de Site Recovery. Además, se puede descargar directamente en el [Centro de descarga de Microsoft](http://aka.ms/unifiedsetup).

1. Inicie sesión en Azure Portal y busque el almacén de Recovery Services.
2. Vaya a **Site Recovery Infrastructure** (Infraestructura de Site Recovery)  > **Servidores de configuración** (en For VMware & Physical Machines [Para máquinas físicas y VMware]).
3. Haga clic en el botón **+Servidores**.
4. En la página **Agregar servidor**, haga clic en el botón Descargar para descargar la clave de Registro. Necesitará esta clave durante la instalación del servidor de configuración para registrarla en el servicio Azure Site Recovery.
5. Haga clic en el vínculo **Download the Microsoft Azure Site Recovery Unified Setup** (Descargar la instalación unificada de Microsoft Azure Site Recovery) para descargar la versión más reciente del servidor de configuración.

  ![Página de descarga](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalación y registro del servidor

1. Ejecute el archivo de instalación unificada.
2. En **Antes de comenzar**, seleccione **Install the configuration server and process server** (Instalar el servidor de configuración y el servidor de procesos).

    ![Antes de comenzar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. En **Third-Party Software License** (Licencia de software de terceros), haga clic en **Acepto** para descargar e instalar MySQL.
4. En **Configuración de Internet**, especifique cómo se conecta el proveedor que se ejecuta en el servidor de configuración a Azure Site Recovery a través de Internet. Asegúrese de que ha permitido las direcciones URL necesarias.

    - Si quiere conectarse con el proxy configurado actualmente en la máquina, seleccione **Conectar con Azure Site Recovery con un servidor proxy**.
    - Si quiere que el proveedor se conecte directamente, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
    - Si el proxy existente requiere autenticación, o si quiere usar un proxy personalizado para la conexión del proveedor, seleccione **Connect with custom proxy settings** (Conectarse con una configuración de proxy personalizada) y especifique la dirección, el puerto y las credenciales.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. En **Comprobación de requisitos previos**, el programa de instalación ejecuta una comprobación para asegurarse de que se pueda ejecutar la instalación. Si aparece una advertencia sobre la **comprobación de la sincronización de hora global**, compruebe que la hora del reloj del sistema (configuración de **fecha y hora**) es la misma que la de la zona horaria.

    ![requisitos previos](./media/physical-manage-configuration-server/combined-wiz5.png)
7. En **MySQL Configuration** (Configuración de MySQL), cree credenciales para iniciar sesión en la instancia de servidor MySQL que se va a instalar.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. En **Detalles del entorno**, seleccione si replicará máquinas virtuales de VMware. Si va a hacerlo, compruebe si PowerCLI 6.0 está instalado.
9. En **Ubicación de instalación**, seleccione dónde quiere instalar los archivos binarios y almacenar la memoria caché. La unidad que seleccione debe tener al menos 5 GB de espacio disponible en disco, pero se recomienda usar una unidad de memoria caché con 600 GB o más de espacio libre.

    ![Ubicación de instalación](./media/physical-manage-configuration-server/combined-wiz8.png)
10. En **Selección de red**, especifique el agente de escucha (adaptador de red y puerto SSL) en el que el servidor de configuración enviará y recibirá los datos de replicación. El puerto 9443 es el que se usa de forma predeterminada para enviar y recibir el tráfico de replicación, pero puede modificar este número de puerto para adecuarlo a los requisitos de su entorno. Además del puerto 9443, también se abre el puerto 443 que un servidor web utiliza para coordinar las operaciones de replicación. No use el puerto 443 para enviar o recibir tráfico de replicación.

    ![Selección de red](./media/physical-manage-configuration-server/combined-wiz9.png)


11. En **Resumen**, revise la información y haga clic en **Instalar**. Se genera una frase de contraseña cuando finaliza la instalación. La necesitará al habilitar la replicación, así que cópiela y manténgala en una ubicación segura.


Después de finalizar el registro, el servidor aparecerá en la hoja **Configuración** > **Servidores** del almacén.


## <a name="install-from-the-command-line"></a>Instalación desde la línea de comandos

Ejecute el archivo de instalación del modo siguiente:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Ejemplo de uso
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parámetros

|Nombre de parámetro| type | DESCRIPCIÓN| Valores|
|-|-|-|-|
| /ServerMode|Obligatorio|Especifica si se deben instalar los servidores de configuración y de procesos, o solo el servidor de procesos|CS<br>PS|
|/InstallLocation|Obligatorio|La carpeta donde se instalan los componentes| Cualquier carpeta del equipo|
|/MySQLCredsFilePath|Obligatorio|La ruta de acceso al archivo en que se almacenan las credenciales del servidor MySQL|El archivo debe tener el formato especificado a continuación|
|/VaultCredsFilePath|Obligatorio|La ruta de acceso del archivo de credenciales del almacén|Ruta de acceso de archivo válido|
|/EnvType|Obligatorio|El tipo de entorno que desea proteger |VMware<br>NonVMware|
|/PSIP|Obligatorio|Dirección IP de la NIC que se usará para la transferencia de datos de replicación| Cualquier dirección IP válida|
|/CSIP|Obligatorio|Dirección IP de la NIC en la que el servidor de configuración realiza la escucha| Cualquier dirección IP válida|
|/PassphraseFilePath|Obligatorio|Ruta de acceso completa a la ubicación del archivo de frase de contraseña|Ruta de acceso de archivo válido|
|/BypassProxy|Opcional|Especifica si el servidor de configuración se conecta a Azure sin proxy|Para hacerlo, obtenga este valor desde Venu|
|/ProxySettingsFilePath|Opcional|Configuración del proxy (el proxy predeterminado requiere autenticación, o un proxy personalizado)|El archivo debe tener el formato especificado a continuación|
|DataTransferSecurePort|Opcional|Número de puerto en el PSIP que se usará para los datos de replicación| Número de puerto válido (el valor predeterminado es 9433)|
|/SkipSpaceCheck|Opcional|Omitir comprobación de espacio para disco de caché| |
|/AcceptThirdpartyEULA|Obligatorio|La marca implica la aceptación de los términos de licencia de terceros| |
|/ShowThirdpartyEULA|Opcional|Muestra los términos de licencia de terceros. Si se proporciona como entrada, se omiten todos los demás parámetros| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Creación de entrada de archivo para MYSQLCredsFilePath

El parámetro MySQLCredsFilePath toma como entrada un archivo. Cree el archivo con el siguiente formato y páselo como parámetro de entrada MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Creación de entrada de archivo para ProxySettingsFilePath
El parámetro ProxySettingsFilePath toma un archivo como entrada. Cree el archivo con el siguiente formato y páselo como parámetro de entrada ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modificación de la configuración de proxy

Se puede modificar la configuración de proxy para el equipo del servidor de configuración como se indica a continuación:

1. Inicie sesión en el servidor de configuración.
2. Inicie el archivo cspsconfigtool.exe mediante el acceso directo del escritorio.
3. Haga clic en la pestaña **Registro de almacén**.
4. Descargue un nuevo archivo de registro de almacén desde el portal y proporciónelo como entrada a la herramienta.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Proporcione los detalles del nuevo proxy y haga clic en el botón **Registrar**.
6. Abra una ventana de comandos de administrador de PowerShell.
7. Ejecute el siguiente comando:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Si tiene conectados más servidores de procesos a este servidor de configuración, deberá [corregir la configuración del proxy en todos los servidores de proceso de escalado horizontal](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) de su implementación.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Proceso para volver a registrar el servidor de configuración en el mismo almacén
  1. Inicie sesión en el servidor de configuración.
  2. Inicie el archivo cspsconfigtool.exe mediante el acceso directo del escritorio.
  3. Haga clic en la pestaña **Registro de almacén**.
  4. Descargue un nuevo archivo de registro del portal y proporciónelo como entrada a la herramienta.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Proporcione los detalles del servidor proxy y haga clic en el botón **Registrar**.  
  6. Abra una ventana de comandos de administrador de PowerShell.
  7. Ejecute el siguiente comando.

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Si tiene varios servidores de procesos, deberá [volver a registrarlos](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registro de un servidor de configuración con otro almacén

> [!WARNING]
> El siguiente paso desasocia el servidor de configuración del almacén actual y se detiene la replicación de todas las máquinas virtuales protegidas en el servidor de configuración.

1. Inicie sesión en el servidor de configuración
2. desde el símbolo del sistema de administrador y ejecute el comando:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Inicie el archivo cspsconfigtool.exe mediante el acceso directo del escritorio.
4. Haga clic en la pestaña **Registro de almacén**.
5. Descargue un nuevo archivo de registro del portal y proporciónelo como entrada a la herramienta.
6. Proporcione los detalles del servidor proxy y haga clic en el botón **Registrar**.  
7. Abra una ventana de comandos de administrador de PowerShell.
8. Ejecute el siguiente comando.
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Actualización de un servidor de configuración

Los paquetes acumulativos se ejecutan para actualizar el servidor de configuración. Las actualizaciones se pueden aplicar para hasta las versiones N-4. Por ejemplo: 

- Si ejecuta 9.7, 9.8, 9,9 o 9.10, puede actualizar directamente a 9.11.
- Si ejecuta la versión 9.6 o anterior, y desea actualizar a 9.11, primero debe actualizar a la versión 9.7 antes de a la versión 9.11.

En la [página wiki de actualizaciones](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx), hay disponibles vínculos a paquetes acumulativos para actualizar a todas las versiones del servidor de configuración.

Actualice el servidor como se indica a continuación:

1. Descargue el archivo del instalador de actualizaciones en el servidor de configuración.
2. Haga doble clic para ejecutar el instalador.
3. El instalador detecta la versión actual que se ejecuta en la máquina.
4. Haga clic en **Aceptar** para confirmar y ejecutar la actualización. 


## <a name="delete-or-unregister-a-configuration-server"></a>Eliminación o anulación del registro de un servidor de configuración

> [!WARNING]
> Antes de comenzar la retirada de su servidor de configuración, lleve a cabo estos pasos:
> 1. [Deshabilite la protección](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) en todas las máquinas virtuales de este servidor de configuración.
> 2. [Desasocie](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) y [elimine](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) todas las directivas de replicación del servidor de configuración.
> 3. [Elimine](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) todos los servidores vCenter y hosts de vSphere que estén asociados con el servidor de configuración.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Eliminación del servidor de configuración de Azure Portal
1. En Azure Portal, vaya a **Site Recovery Infrastructure** (Infraestructura de Site Recovery)  > **Servidores de configuración** en el menú Almacén.
2. Haga clic en el servidor de configuración que quiere retirar.
3. En la página de detalles del servidor de configuración, haga clic en el botón **Eliminar**.
4. Haga clic en **Sí** para confirmar la eliminación del servidor.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Desinstalación del servidor de configuración y sus dependencias
  > [!TIP]
  Si tiene pensado volver a usar el servidor de configuración con Azure Site Recovery, puede ir al paso 4 directamente.

1. Inicie sesión como administrador en el servidor de configuración.
2. Abra Panel de Control > Programas > Desinstalar programas.
3. Desinstale los programas en el orden siguiente:
  * Agente de Microsoft Azure Recovery Services
  * Servicio de movilidad de Microsoft Azure Site Recovery/servidor de destino maestro
  * Proveedor de Microsoft Azure Site Recovery
  * Servidor de configuración/servidor de procesos de Microsoft Azure Site Recovery
  * Dependencias del servidor de configuración de Microsoft Azure Site Recovery
  * MySQL Server 5.5
4. Ejecute el siguiente comando desde un símbolo del sistema de administrador:
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Eliminación o anulación del registro de un servidor de configuración (PowerShell)

1. [Instale](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) el módulo de Azure PowerShell.
2. Inicie sesión en su cuenta de Azure mediante el comando
    
    `Login-AzureRmAccount`
3. Seleccione la suscripción en la que existe el almacén:

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Ahora, configure el contexto de almacén.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Obtenga el valor seleccionado de su servidor de configuración.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminación del servidor de configuración

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> La opción **-Force** de Remove-AzureRmSiteRecoveryFabric se puede usar para forzar la eliminación del servidor de configuración.

## <a name="renew-ssl-certificates"></a>Renovación de certificados SSL
El servidor de configuración lleva integrado un servidor web, que coordina las actividades de Mobility Service, los servidores de procesos y los servidores de destino maestros conectados a él. El servidor web usa un certificado SSL para autenticar clientes. El certificado expira después de tres años y se puede renovar en cualquier momento.

### <a name="check-expiry"></a>Comprobación de expiración

En lo que respecta a las implementaciones de servidores de configuración antes de mayo de 2016, la expiración del certificado se estableció en 1 año. Si tiene un certificado que va a expirar, ocurre lo siguiente:

- Cuando la fecha de expiración es 2 meses o menos, el servicio comienza a enviar las notificaciones en el portal y por correo electrónico (si está suscrito a las notificaciones de Azure Site Recovery).
- Aparece un banner de notificación en la página de recursos de almacén. Haga clic en el banner para obtener más información.
- Si ve el botón **Actualizar ahora**, quiere decir que hay algunos componentes en el entorno que aún no se han actualizado a la versión 9.4.xxxx.x o superior. Actualice los componentes antes de renovar el certificado. La renovación no es posible en versiones anteriores.

### <a name="renew-the-certificate"></a>Renovación del certificado

1. En el almacén, abra **Infraestructura de Site Recovery** > **Servidor de configuración**y haga clic en el servidor de configuración requerido.
2. La fecha de expiración aparece en **Estado del servidor de configuración**.
3. Haga clic en **Renovar certificados**. 




## <a name="common-issues"></a>Problemas comunes
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>pasos siguientes

Revise los tutoriales para configurar la recuperación ante desastres de [servidores físicos](tutorial-physical-to-azure.md) en Azure.

