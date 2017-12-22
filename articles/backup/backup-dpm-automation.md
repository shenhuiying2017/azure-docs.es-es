---
title: Azure Backup - Uso de PowerShell para hacer copias de seguridad de cargas de trabajo DPM | Microsoft Docs
description: "Obtenga información acerca de cómo implementar y administrar Azure Backup para Data Protection Manager (DPM) mediante PowerShell"
services: backup
documentationcenter: 
author: NKolli1
manager: shreeshd
editor: 
ms.assetid: e9bd223c-2398-4eb1-9bf3-50e08970fea7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: adigan;anuragm;trinadhk;markgal
ms.openlocfilehash: 9322037427c84f0b8a91cc76f5c0fed52167bc3c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Implementación y administración de copias de seguridad en Azure para servidores de Data Protection Manager (DPM) con PowerShell
En este artículo se muestra cómo usar PowerShell para configurar Azure Backup en un servidor DPM y para administrar copias de seguridad y recuperaciones.

## <a name="setting-up-the-powershell-environment"></a>Configuración del entorno de PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Para poder usar PowerShell para administrar las copias de seguridad de Data Protection Manager en Azure, debe tener el entorno adecuado en PowerShell. Al principio de la sesión de PowerShell, asegúrese de ejecutar el siguiente comando para importar los módulos correctos y poder hacer referencia correctamente a los cmdlet de DPM:

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Instalación y registro
Para empezar:

1. [Descargue la última versión de PowerShell](https://github.com/Azure/azure-powershell/releases) (la versión mínima necesaria es: 1.0.0).
2. Para empezar, habilite los commandlets de Azure Backup, para lo que debe cambiar al modo *AzureResourceManager* usando el commandlet **Switch-AzureMode**:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Las siguientes tareas de instalación y registro se pueden automatizar con PowerShell:

* Creación de un almacén de Recovery Services
* Instalación del agente de Azure Backup
* Registro con el servicio Azure Backup
* Configuración de redes
* Configuración de cifrado

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación
Los siguientes pasos le guiarán por el proceso de creación de un almacén de Recovery Services. Un almacén de Recovery Services no es lo mismo que un almacén de copia de seguridad.

1. Si utiliza Azure Backup por primera vez, debe utilizar el cmdlet **Register-AzureRMResourceProvider** para registrar el proveedor de Azure Recovery Services con su suscripción.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. El almacén de Recovery Services es un recurso de ARM, por lo que deberá colocarlo dentro de un grupo de recursos. Puede usar un grupo de recursos existente o crear uno nuevo. Al crear un nuevo grupo de recursos, especifique su nombre y su ubicación.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Utilice el cmdlet **New-AzureRmRecoveryServicesVault** para crear un nuevo almacén. Asegúrese de especificar para el almacén la misma ubicación del grupo de recursos.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Especifique el tipo de redundancia de almacenamiento que se usará: [almacenamiento con redundancia local (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) o [almacenamiento con redundancia geográfica (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). En el ejemplo siguiente se muestra que la opción -BackupStorageRedundancy para testVault está establecida en GeoRedundant.

   > [!TIP]
   > Muchos de los cmdlets de Azure Backup requieren el objeto de almacén de Recovery Services como entrada. Por este motivo, es conveniente almacenar el objeto de almacén de Recovery Services de Backup en una variable.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visualización de los almacenes de una suscripción
Utilice **Get-AzureRmRecoveryServicesVault** para ver la lista de todos los almacenes de la suscripción actual. Este comando se puede usar para comprobar que se ha creado un almacén o para ver qué almacenes están disponibles en la suscripción.

Ejecute el comando Get-AzureRmRecoveryServicesVault, y se mostrarán todos los almacenes de la suscripción.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalación del agente de Azure Backup en un servidor DPM
Antes de instalar el agente de Azure Backup, necesitará tener el instalador descargado y disponible en el servidor de Windows. Puede obtener la versión más reciente del instalador en el [Centro de descarga de Microsoft](http://aka.ms/azurebackup_agent) o en la página Panel del almacén de Recovery Services. Guarde el instalador en una ubicación que tenga fácil acceso, como *C:\Downloads\*.

Para instalar el agente, ejecute el comando siguiente en una consola de PowerShell con privilegios elevados **en el servidor DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Esto instala el agente con todas las opciones predeterminadas. La instalación está unos minutos en segundo plano. Si no se especifica la opción */nu* , se abre la ventana de **Windows Update** al final de la instalación para comprobar si hay actualizaciones.

El agente se muestra en la lista de programas instalados. Para ver la lista de programas instalados, vaya a **Panel de Control** > **Programas** > **Programas y características**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opciones de instalación
Para ver todas las opciones disponibles a través de la línea de comandos, use el siguiente comando:

```
PS C:\> MARSAgentInstaller.exe /?
```

Las opciones disponibles incluyen:

| Opción | Detalles | Valor predeterminado |
| --- | --- | --- |
| /q |Instalación silenciosa |- |
| /p:"ubicación" |Ruta de acceso a la carpeta de instalación para el agente de Azure Backup. |C:\Archivos de programa\Microsoft Azure Recovery Services Agent |
| /s:"ubicación" |Ruta de acceso a la carpeta de caché para el agente de Azure Backup. |C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Participación en Microsoft Update |- |
| /nu |No busca actualizaciones una vez completada la instalación |- |
| /d |Desinstala el agente de Microsoft Azure Recovery Services. |- |
| /ph |Dirección host del proxy |- |
| /po |Número de puerto del host de proxy |- |
| /pu |Nombre de usuario del host de proxy |- |
| /pw |Contraseña de proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registro de DPM en un almacén de Recovery Services
Después de crear el almacén de Recovery Services, descargue el agente más reciente y las credenciales de almacén y guárdelas en una ubicación adecuada como C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

En el servidor de DPM, ejecute el cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) para registrar la máquina en el almacén.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Opciones de configuración inicial
Una vez que el servidor DPM se registra con el almacén de Recovery Services, se inicia con la configuración de suscripción predeterminada. Estas opciones de suscripción incluyen funciones de red, cifrado y el área de ensayo. Para cambiar la configuración de la suscripción, primero se debe obtener un identificador en la configuración (valor predeterminado) existente con el cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) :

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas las modificaciones se realizan en este objeto de PowerShell local ```$setting``` y, a continuación, el objeto completo se confirma en DPM y Azure Backup para guardarlos con el cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). Deberá usar la marca ```–Commit``` para asegurarse de que los cambios se conservan. Azure Backup no aplicará ni usará la configuración a menos que se confirme.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Redes
Si la conectividad del equipo DPM para el servicio de Azure Backup en Internet es a través de un servidor proxy, se debe proporcionar la configuración del servidor proxy para que las copias de seguridad se efectúen correctamente. Esto se realiza mediante los parámetros ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` y ```ProxyPassword``` con el cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). En este ejemplo, no hay ningún servidor proxy y por tanto se borra explícitamente cualquier información relacionada con el proxy.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

También puede controlar el uso de ancho de banda con las opciones de ```-WorkHourBandwidth``` y ```-NonWorkHourBandwidth``` para un conjunto determinado de días de la semana. En este ejemplo no se define ninguna limitación.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configuración del área de ensayo
El agente de Azure Backup que se ejecuta en el servidor DPM necesita almacenamiento temporal para los datos restaurados desde la nube (área de almacenamiento provisional local). Configure el área de ensayo mediante el cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) y el parámetro ```-StagingAreaPath```.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

En el ejemplo anterior, el área de ensayo se establecerá en *C:\StagingArea* en el objeto de PowerShell ```$setting```. Asegúrese de que la carpeta especificada ya existe, o bien se producirá un error en la confirmación final de la configuración de la suscripción.

### <a name="encryption-settings"></a>Configuración de cifrado
Los datos de copia de seguridad enviados a Azure Backup se cifran para proteger la confidencialidad de los datos. La frase de contraseña de cifrado es la "contraseña" que permite descifrar los datos en el momento de la restauración. Es importante que mantenga esta información segura cuando la establezca.

En el ejemplo siguiente, el primer comando convierte la cadena ```passphrase123456789``` en una cadena segura y la asigna a la variable denominada ```$Passphrase```. El segundo comando establece la cadena segura en ```$Passphrase``` como la contraseña para cifrar las copias de seguridad.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Mantenga la información de la frase de contraseña segura una vez establecida. No podrá restaurar los datos de Azure sin esta frase de contraseña.
>
>

En este punto, debería haber realizado todos los cambios necesarios al objeto ```$setting``` . Recuerde que debe confirmar los cambios.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteger los datos en Azure Backup
En esta sección, agregará un servidor de producción a DPM y, a continuación, protegerá los datos en el almacenamiento de DPM local y, a continuación, en Azure Backup. En los ejemplos, se mostrará cómo realizar copias de seguridad de archivos y carpetas. La lógica se puede ampliar fácilmente para efectuar una copia de seguridad de cualquier origen de datos compatible con DPM. Las copias de seguridad de DPM se rigen por un grupo de protección (PG) con cuatro partes:

1. **Miembros del grupo** es una lista de todos los objetos que se pueden proteger (también conocidos como *Orígenes de datos* en DPM) que desea proteger en el mismo grupo de protección. Por ejemplo, puede proteger las máquinas virtuales de producción en un grupo de protección y las bases de datos de SQL Server en otro grupo de protección, ya que pueden tener distintos requisitos de copia de seguridad. Antes de que puedan realizar una copia de seguridad de cualquier origen de datos en un servidor de producción, deberá asegurarse de que el agente de DPM esté instalado en el servidor y administrado por DPM. Siga los pasos para [instalar el agente de DPM](https://technet.microsoft.com/library/bb870935.aspx) y vincularlo al servidor DPM apropiado.
2. **Método de protección de datos** especifica las ubicaciones de copia de seguridad de destino: cinta, disco y nube. En nuestro ejemplo se protegerán los datos en el disco local y en la nube.
3. Una **programación de copia de seguridad** que especifica cuándo deben realizarse copias de seguridad y la frecuencia con la que se deben sincronizar los datos entre el servidor DPM y el servidor de producción.
4. Una **programación de retención** que especifica cuánto tiempo se conservarán los puntos de recuperación en Azure.

### <a name="creating-a-protection-group"></a>Creación de un grupo de protección
Empiece por crear un nuevo grupo de protección mediante el cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) .

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

El cmdlet anterior creará un grupo de protección denominado *ProtectGroup01*. Un grupo de protección existente también se puede modificar más adelante para agregar la copia de seguridad a la nube de Azure. Sin embargo, para realizar cambios en el grupo de protección (nuevo o existente), necesitamos obtener un identificador en un objeto *modificable* utilizando el cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) .

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Agregar miembros de grupo al grupo de protección
Cada agente de DPM conoce la lista de orígenes de datos del servidor en el que está instalado. Para agregar un origen de datos al grupo de protección, el agente de DPM debe enviar primero una lista de los orígenes de datos al servidor DPM. A continuación, se agregan y seleccionan uno o más orígenes de datos al grupo de protección. Los pasos de PowerShell necesarios para lograrlo son:

1. Recuperar una lista de todos los servidores administrados por DPM a través del agente de DPM.
2. Elija un servidor específico.
3. Recupere una lista de todos los orígenes de datos del servidor.
4. Elija uno o más orígenes de datos y agréguelos al grupo de protección

La lista de servidores en los que está instalado el agente de DPM y está siendo administrando por el servidor DPM se adquiere con el cmdlet [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) . En este ejemplo se van a filtrar y configurar solo PS con el nombre *productionserver01* para efectuar una copia de seguridad.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Ahora, capture la lista de orígenes de datos en ```$server``` con el cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605). En este ejemplo se filtra para el volumen *D:\* que se quiere configurar para la copia de seguridad. A continuación, este origen de datos se agrega al grupo de protección mediante el cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732). Recuerde usar el objeto de grupo de protección *modifiable* ```$MPG``` para realizar las incorporaciones.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita este paso tantas veces como sea necesario hasta que haya agregado todos los orígenes de datos elegidos al grupo de protección. También puede comenzar con un solo origen de datos y completar el flujo de trabajo para crear el grupo de protección y, posteriormente, agregar más orígenes de datos al grupo de protección.

### <a name="selecting-the-data-protection-method"></a>Selección del método de protección de datos
Una vez que los orígenes de datos se han agregado al grupo de protección, el siguiente paso es especificar el método de protección mediante el cmdlet [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) . En este ejemplo, el grupo de protección es el programa de instalación de disco local y la copia de seguridad en la nube. También tendrá que especificar el origen de datos que quiere proteger en la nube mediante el cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) con la marca -Online.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Establecimiento del período de retención
Establecer el período de retención de los puntos de copia de seguridad mediante el cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) . Aunque puede parecer extraño establecer el período de retención antes de definir la programación de copia de seguridad, usando el cmdlet ```Set-DPMPolicyObjective``` se establece automáticamente una programación de copia de seguridad predeterminada que, a continuación, se puede modificar. Siempre es posible establecer el programa de copia de seguridad en primer lugar y, a continuación, la directiva de retención.

En el ejemplo siguiente, el cmdlet establece los parámetros de retención de copias de seguridad de disco. Esto conservará las copias de seguridad durante 10 días y sincronizará los datos cada 6 horas entre el servidor de producción y el servidor DPM. ```SynchronizationFrequencyMinutes``` no define la frecuencia con la que se crea un punto de copia de seguridad, sino la frecuencia con la que se copian los datos en el servidor DPM.  Esto evita que las copias de seguridad se vuelvan demasiado grandes.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Para las copias de seguridad que tienen como destino Azure (DPM hace referencia a estas como copias de seguridad en línea) se pueden configurar intervalos de retención para [retenciones a largo plazo usando un esquema abuelo-padre-hijo (GFS)](backup-azure-backup-cloud-as-tape.md). Es decir, puede definir una directiva de retención combinada que implique directivas de retención diaria, semanal, mensual y anual. En este ejemplo, se crea una matriz que representa el esquema de retención complejo que se desea y, a continuación, se configura el intervalo de retención usando el cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) .

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Establecer la programación de copia de seguridad
DPM establece automáticamente una programación de copia de seguridad predeterminada si especifica el objetivo de protección mediante el cmdlet ```Set-DPMPolicyObjective``` . Para cambiar las programaciones predeterminadas, use el cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) seguido por el cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723).

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

En el ejemplo anterior, ```$onlineSch``` es una matriz con cuatro elementos que contiene la programación de protección en línea existente para el grupo de protección en el esquema de GFS:

1. ```$onlineSch[0]``` contiene la programación diaria
2. ```$onlineSch[1]``` contiene la programación semanal
3. ```$onlineSch[2]``` contiene la programación mensual
4. ```$onlineSch[3]``` contiene la programación anual

Por ello, si necesita modificar la programación semanal, deberá hacer referencia a ```$onlineSch[1]```.

### <a name="initial-backup"></a>Copia de seguridad inicial
Cuando efectúa una copia de seguridad de un origen de datos por primera vez, DPM debe crear una réplica inicial que genera una copia completa del origen de datos que se debe proteger en el volumen de réplica DPM. Esta actividad se puede programar para una hora específica o puede desencadenarse manualmente mediante el cmdlet [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) con el parámetro ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Cambiar el tamaño de la réplica de DPM y el volumen de puntos de recuperación
También puede cambiar el tamaño del volumen de réplica de DPM, así como el volumen de instantánea mediante el cmdlet [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) , como en el ejemplo siguiente: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb).

### <a name="committing-the-changes-to-the-protection-group"></a>Confirmar los cambios en el grupo de protección
Por último, los cambios deben confirmarse antes de que DPM pueda realizar la copia de seguridad según la configuración del nuevo grupo de protección. Esto se realiza mediante el cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) .

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Ver los puntos de copia de seguridad
Puede usar el cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) para obtener una lista de todos los puntos de recuperación para un origen de datos. En este ejemplo, se realiza lo siguiente:

* se capturan todos los grupos de protección del servidor DPM, que se almacenan en una matriz ```$PG```
* se obtienen los orígenes de datos correspondientes a ```$PG[0]```
* se obtienen todos los puntos de recuperación de un origen de datos.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restauración de datos protegidos en Azure
Restauración de datos es una combinación de un objeto ```RecoverableItem``` y un objeto ```RecoveryOption```. En la sección anterior se facilita una lista de los puntos de la copia de seguridad de un origen de datos.

En el ejemplo siguiente, demostraremos cómo restaurar una máquina virtual de Hyper-V de Azure Backup mediante la combinación de puntos de copia de seguridad con el destino para la recuperación. Este ejemplo incluye:

* Creación de una opción de recuperación mediante el cmdlet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592).
* Recuperación de la matriz de puntos de copia de seguridad mediante el cmdlet ```Get-DPMRecoveryPoint``` .
* Elegir un punto de copia de seguridad desde el que efectuar la restauración.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Los comandos se pueden ampliar fácilmente para cualquier tipo de origen de datos.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre DPM para Azure Backup, vea [Introducción a Copia de seguridad de DPM](backup-azure-dpm-introduction.md)
