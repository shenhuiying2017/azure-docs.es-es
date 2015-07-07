<properties
	pageTitle="Copia de seguridad de Azure: implementar y administrar copias de seguridad en DPM mediante Windows PowerShell | Microsoft Azure"
	description="Aprenda a implementar y administrar Copia de seguridad de Azure en Data Protection Manager (DPM) mediante Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Implementar y administrar copias de seguridad en Azure para servidores de Data Protection Manager (DPM) mediante Windows PowerShell
En este artículo se muestra cómo se usa la interfaz de línea de comandos de Windows PowerShell® para instalar Copia de seguridad de Azure en un servidor DPM y para administrar copias de seguridad y la recuperación.

## Instalación del entorno de Windows PowerShell
Antes de poder usar Windows PowerShell para administrar Copia de seguridad de Azure, deberá tener instaladas la versión y las variables de entorno adecuadas de Windows PowerShell.

Compruebe que tiene Windows PowerShell, versión 3.0 o 4.0. Para encontrar la versión de Windows PowerShell, escriba este comando en un símbolo del sistema de PowerShell de Windows o DPM.

```
$PSVersionTable
```

Recibirá el siguiente tipo de información:

| Nombre | Valor |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

Compruebe que el valor de **PSVersion** es 3.0 o 4.0. Si no es así, consulte [Windows Management Framework](http://www.microsoft.com/download/details.aspx?id=34595) 3.0 o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

## Instalación y registro
### Instalación del agente de Copia de seguridad de Azure en un servidor DPM
Para instalar al agente de Copia de seguridad de Azure, necesitará tener el instalador descargado y presente en Windows Server. Puede obtener la versión más reciente del instalador en el [Centro de descarga de Microsoft ](aka.ms/azurebackup_agent). Guarde el instalador en una ubicación que tenga fácil acceso, como *C:*.

Para instalar el agente, ejecute el comando siguiente en una consola de Windows PowerShell de DPM con privilegios elevados:

```
PS C:> MARSAgentInstaller.exe /q
```

Eso instalará el agente con todas las opciones predeterminadas. La instalación está unos minutos en segundo plano. Si no se especifica la opción */nu*, se abrirá la ventana de Windows Update al final de la instalación para comprobar si hay actualizaciones.

El agente se mostrará en la lista de programas instalados. Para ver la lista de programas instalados, vaya a **Panel de Control** > **programas** > **programas y características**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

#### Opciones de instalación
Para ver todas las opciones disponibles a través de la línea de comandos, use el siguiente comando:

```
PS C:> MARSAgentInstaller.exe /?
```

Las opciones disponibles incluyen:

| Opción | Detalles | Valor predeterminado |
| ---- | ----- | ----- |
| /q | Instalación desatendida | - | | /p:"ubicación" | Ruta de acceso a la carpeta de instalación del agente de Copia de seguridad de Azure. | C:\Archivos de programa\Microsoft Azure Recovery Services Agent | | /s:"ubicación" | Ruta de acceso a la carpeta de caché del agente de Copia de seguridad de Azure. | C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch | | /m | Participar en Microsoft Update | - | | /nu | No comprobar si hay actualizaciones cuando finalice la instalación | - | | /d | Desinstala el agente de Servicios de recuperación de Microsoft Azure | - | | /ph | Dirección de host del proxy | - | | /po | Número de puerto de host del proxy | - | | /pu | Nombre de usuario de host del proxy | - | | /pw | Contraseña del proxy | - |

### Registro con el servicio de Copia de seguridad de Azure
Para poder registrarse con el servicio de Copia de seguridad de Azure, asegúrese de que se cumplen los siguientes requisitos previos: disponer de una suscripción válida a Azure; crear un almacén de copia de seguridad; descargar las credenciales del almacén de claves y almacenarlas en una ubicación práctica (como *C:\Downloads*). También se puede cambiar el nombre de las credenciales del almacén de claves para que sea más práctico.

El registro de la máquina con el almacén de claves se realiza mediante el cmdlet [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787):

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

Esto registrará el servidor DPM denominado "TestingServer" con el almacén de claves de Microsoft Azure mediante las credenciales del almacén de claves que se encuentran en *C:*.

> [AZURE.IMPORTANT]No use rutas de acceso relativas para especificar el archivo de credenciales del almacén de claves. Debe proporcionar una ruta de acceso absoluta como entrada para el cmdlet.

### Configuración de las opciones de copia de seguridad de DPM (configuración de suscripción) para la copia de seguridad en línea
Cuando se registre el servidor DPM para la protección en línea, se establecerá con la configuración de suscripción predeterminada, que especifica varias opciones de copia de seguridad. Si quiere cambiar la configuración de suscripción, deberá obtener la configuración existente y, después, modificarla según sus requisitos.

#### Obtención de la configuración de suscripción existente del servidor DPM
Para configurar la configuración de suscripción en línea de DPM, primero deberá obtener la configuración existente mediante el cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx):

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Deberá ejecutar este comando para obtener el objeto de configuración de suscripción que se puede usar para configurar diversas opciones y, al final, guardar la configuración. Al ejecutar el comando para un servidor DPM nuevo, se devolverá la configuración predeterminada.

#### Modificación de la configuración de suscripción
Puede configurar los valores siguientes para la suscripción de copia de seguridad en línea de DPM mediante el cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791):

##### Área de almacenamiento provisional
El agente de Copia de seguridad de Azure que se ejecuta en el servidor DPM necesita almacenamiento temporal para los datos restaurados desde la nube (área de almacenamiento provisional local). Puede usar el comando siguiente para configurar el área de almacenamiento temporal. El área de almacenamiento temporal se establecerá como "C:\StagingArea" para **$setting**, pero la configuración no se aplicará al servidor DPM a menos que la confirme.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]Asegúrese de que la carpeta especificada en el comando anterior ya existe. De lo contrario, obtendrá un mensaje de error al guardar la configuración de suscripción

##### Redes
La conectividad desde el equipo DPM a Internet se realiza a través de un servidor proxy. Es posible proporcionar la configuración de proxy al agente. En este ejemplo, no hay ningún servidor proxy y por tanto se borra explícitamente cualquier información relacionada con el proxy.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

También se puede controlar el uso de ancho de banda con las opciones de *ancho de banda de horas laborables* y *ancho de banda de horas no laborables* para un conjunto determinado de días de la semana. En este ejemplo no se define ninguna limitación

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### Configuración de cifrado
Los datos de copia de seguridad enviados a Copia de seguridad de Azure están cifrados para proteger la confidencialidad de los datos. La frase de contraseña de cifrado es la "contraseña" que permite descifrar los datos en el momento de la restauración.

> [AZURE.IMPORTANT]Mantenga esta información segura cuando la establezca.

En el ejemplo siguiente, el primer comando convierte la cadena passphrase123456789 en una cadena segura y la asigna a la variable denominada $Passphrase. El segundo comando establece la cadena segura en $Passphrase como la contraseña para cifrar las copias de seguridad

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### Almacenamiento de la configuración de suscripción
Deberá confirmar los cambios en el servidor DPM mediante la opción *–Commit*

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]La configuración no se aplicará a menos que la confirme.

## Protección de datos en Copia de seguridad de Azure
En los pasos siguientes se muestra cómo puede agregar un servidor de producción a DPM y, después, proteger los datos en el disco y en línea para Azure. En los ejemplos, se mostrará cómo realizar copias de seguridad de archivos y carpetas. La lógica se puede ampliar fácilmente para realizar copias de seguridad de cualquier origen de datos que se admita. Como requisito previo, es necesario instalar y registrar el servidor DPM para la protección en línea.

Todas las copias de seguridad de DPM se rigen por un grupo de protección que consta de cuatro partes

1. **Miembros de grupo** es una lista de todos los objetos que se pueden proteger y que desea proteger en el mismo grupo de protección. Por ejemplo, quizá quiera proteger las máquinas virtuales de producción en un grupo de protección y probar las máquinas virtuales en otro, ya que pueden tener distintos requisitos de copia de seguridad.
2. **Método de protección de datos** se usa para especificar dónde quiere proteger los datos. En este ejemplo se protegerán los datos en el disco y en protección en línea para la nube de Azure.
3. Una **programación de copia de seguridad** que especifica cuándo deben realizarse las copias de seguridad y la frecuencia con que se deben sincronizar los datos entre la copia del servidor DPM y el servidor de producción.
4. Una **programación de retención** que especifica cuánto tiempo se conservarán los puntos de recuperación en Azure.

### Creación de un grupo de protección y realización de copias de seguridad de los datos en intervalos programados
#### Incorporación de un servidor de producción en un servidor DPM
Antes de hacer una copia de seguridad de los datos de un servidor de producción, debe asegurarse de que el agente de DPM está instalado en el servidor de producción y de que lo administra DPM. Siga los pasos que se muestran [aquí](https://technet.microsoft.com/library/bb870935.aspx) para instalar el agente de DPM y configurarlo para que se realice una copia de seguridad mediante el servidor DPM adecuado.

#### Creación de un grupo de protección
En este artículo, dado que se está automatizando la copia de seguridad, supondremos que no se ha configurado nada. Para comenzar, se creará un nuevo grupo de protección mediante el cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx).

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Antes de realizar ningún cambio en el grupo de protección, es necesario establecerlo en modo modificable mediante el cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx).

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

Los cmdlets anteriores crearán un grupo de protección denominado "ProtectGroup01", pero aún no realizarán copias de seguridad. Debe definir lo que quiere guardar en copia de seguridad, cuándo se realizarán y el lugar en que se almacenarán.

### Agregar miembros de grupo al grupo de protección
Existen varios pasos para agregar un origen de datos al grupo de protección

#### Obtención del servidor de producción del que se quiere realizar una copia de seguridad
Puede capturar todos los servidores de producción en los que está instalado el agente DPM y que administra el servidor DPM mediante el cmdlet [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx). En este ejemplo, se filtrará y solo se configurará el servidor de producción con el nombre "productionserver01" para la copia de seguridad.

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### Ejecución de una consulta para obtener una lista de orígenes de datos en servidores de producción
Puede ejecutar una consulta en la máquina del servidor de producción para obtener una lista de todos los orígenes de datos de los que DPM puede hacer una copia de seguridad mediante el cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx). En este ejemplo, puede filtrar el origen de datos "D:\" en el servidor de producción en el que se quiere configurar la protección.

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### Agregar el origen de datos para la protección
Puede agregar el origen de datos al servidor de producción "ProtectGroup01" que se creó en el ejemplo anterior mediante el cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx)

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### Selección del método de protección de datos
Cuando se haya agregado el origen de datos al servidor de producción, debe especificar el método de protección. En este caso, se configura el grupo de protección para la protección a corto plazo en disco y a largo plazo en cinta.

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Especificación de objetivos de puntos de recuperación
#### Establecimiento del intervalo de retención
Puede establecer el intervalo de retención del grupo de protección mediante [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx). El comando siguiente establece el *intervalo de retención* y la *frecuencia de sincronización* del punto de recuperación basado en disco.

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

En los puntos de recuperación en línea, puede configurar los intervalos de retención de distintos esquemas GFS (p, ej., diario, semanal, mensual y anual). En este ejemplo, se crea un objeto con varios intervalos de retención y, después, se configura el intervalo de retención en línea mediante el objeto, como se muestra en el ejemplo siguiente.

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### Establecimiento de programaciones de copia de seguridad
DPM establece las programaciones predeterminadas automáticamente si se especifica el objetivo de protección mediante el cmdlet **Set-PolicyObjective**. Para cambiar las programaciones predeterminadas, use el cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx) seguido por el cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx).

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### Creación de réplicas
Al realizar una copia de seguridad de origen de datos por primera vez, DPM debe crear una réplica inicial que creará una copia del origen de datos que se quiere proteger en el volumen de réplicas DPM. Puede programar la creación de una réplica automáticamente en un momento determinado o manualmente, mediante el cmdlet [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx).

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### Confirmación del grupo de protección
Por último, debe confirmar los cambios antes de que DPM pueda realizar la copia de seguridad según la configuración del grupo de protección mediante el cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx).

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Obtención de una lista de todos los puntos de recuperación en línea
Puede usar el cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx) para obtener una lista de todos los puntos de recuperación de un origen de datos. En este ejemplo, se realiza lo siguiente: se capturan todos los grupos de protección del servidor DPM, que se almacenarán en una matriz $PG; se obtienen los orígenes de datos correspondiente a $PG[0]; se obtienen todos los puntos de recuperación de un origen de datos.

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Restauración de datos protegidos en Azure
En este ejemplo se muestra cómo restaurar una máquina virtual de Hyper-V desde un punto de recuperación en línea, pero los comandos se puede ampliar fácilmente para cualquier tipo de origen de datos.

En primer lugar, deberá crear una opción de recuperación mediante el cmdlet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx). En el ejemplo siguiente, se recuperará un origen de datos de Hyper-V en una ubicación alternativa.

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

Cuando haya configurado la opción de recuperación, podrá restaurar el punto de recuperación en línea que capturó en la sección anterior [Obtención de una lista de todos los puntos de recuperación en línea](#Getting-a-list-of-all-Online-Recovery-Points).

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## Pasos siguientes
Para obtener más información sobre Copia de seguridad de Azure para DPM, consulte [Introducción a Copia de seguridad de DPM en Azure](backup-azure-dpm-introduction.md)

<!---HONumber=62-->