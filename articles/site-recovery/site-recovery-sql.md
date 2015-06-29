<properties 
	pageTitle="Recuperación ante desastres con SQL Server y Azure Site Recovery" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de SQL Server en un sitio secundario local o en Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# Recuperación ante desastres con SQL Server y Azure Site Recovery 

Site Recovery es un servicio de Azure que contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores virtuales. Site Recovery admite una serie de mecanismos de replicación para una protección, replicación y conmutación por error coherente de máquinas en Azure o un centro de datos secundario. Obtenga información general acerca de todos los escenarios de implementación en [Información general sobre Azure Site Recovery](site-recovery-overview.md).

 Este artículo describe cómo proteger el back-end de SQL Server de una aplicación con una combinación de tecnologías de SQL Server BCDR y Site Recovery. Debe tener una buena comprensión de las características de SQL Server BCDR (clústeres de conmutación por error, grupos de disponibilidad de AlwaysOn, creación de reflejos de bases de datos, trasvase de registros) y Site Recovery antes de implementar los escenarios descritos en este artículo.



## Información general

Muchas cargas de trabajo utilizan SQL Server como base. Las aplicaciones como SharePoint, Dynamics y SAP utilizan SQL Server para implementar servicios de datos. Características de alta disponibilidad y recuperación ante desastres de SQL Server como grupos de disponibilidad de SQL Server para proteger y recuperar las bases de datos de SQL Server.

Site Recovery puede proteger SQL Server que se ejecuta como una máquina virtual de Hyper-V, una máquina virtual de VMware o un servidor físico.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>Servidor físico</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>De local a local</td>
		<td>De local a Azure</td>
		<td>De local a local</td>
		<td>De local a Azure</td>
		<td>De local a local</td>
		<td>De local a Azure</td>
    </tr>
	<tr align="left" valign="top">
		<td>Sí</td>
		<td>Sí</td>
		<td>Sí</td>
		<td>Próximamente</td>
		<td>Sí</td>
		<td>Próximamente</td>
    </tr>
    </tbody>
    </table>

## Compatibilidad e integración

Site Recovery se puede integrar con las tecnologías nativas de SQL Server BCDR resumidas en la tabla para proporcionar una solución de recuperación ante desastres.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Característica</b></td>
		<td><b>Detalles</b></td>
		<td><b>Versión de SQL Server</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>Grupos de disponibilidad AlwaysOn</b></td>
		<td><p>Múltiples instancias independientes de SQL Server se ejecutan en un clúster de conmutación por error que tienen varios nodos.</p> <p>Las bases de datos se pueden agrupar en grupos de conmutación por error que se puede copiar (reflejar) en instancias de SQL Server para que no se necesite ningún almacenamiento compartido.</p> <p>Proporciona recuperación ante desastres entre un sitio principal y uno o más sitios secundarios. Dos nodos pueden configurarse en un clúster no compartido con Bases de datos de SQL Server configurado en un grupo de disponibilidad con replicación sincrónica y conmutación por error automática.</p></td>
		<td>SQL Server 2014/2012 Enterprise Edition</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>Clústeres de conmutación por error (FCI AlwaysOn)</b></td>
		<td><p>SQL Server aprovecha los clústeres de conmutación por error de Windows para una alta disponibilidad de las cargas de trabajo de SQL Server local.</p><p>Los nodos que ejecutan instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error. Si una instancia está inactiva, el clúster conmuta por error a otro.</p> <p>El clúster no protege ante errores o interrupciones en el almacenamiento compartido. El disco compartido se puede implementar con iSCSI, canal de fibra o VHDX compartido.</p></td>
		<td><p>Ediciones de SQL Server Enterprise</p> <p>SQL Server Standard Edition (limitado solo a dos nodos)</p></td>
	<tr align="left" valign="top">
		<td><b>Creación de un reflejo de la base de datos en modo de alta seguridad</b></td>
		<td>Protege una sola base de datos en una única copia secundaria. Disponible en modos de replicación de seguridad alta (sincrónica) y de alto rendimiento (asincrónica). No requiere un clúster de conmutación por error.</td>
		<td><p>SQL Server 2008 R2</p><p>Todas las ediciones de SQL Server Enterprise</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>SQL Server independiente</b></td>
		<td>SQL Server y la base de datos se hospedan en un único servidor (físico o virtual). Los clústeres de host se utilizan para lograr alta disponibilidad, si el servidor virtual. Sin alta disponibilidad de nivel de invitado.</td>
		<td>Edición Enterprise o Standard</td>
 
    </tbody>
    </table>

La siguiente tabla resume nuestras recomendaciones para integrar las tecnologías de SQL Server BCDR en la implementación de Site Recovery.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Versión</b></td>
		<td><b>Edición</b></td>
		<td><b>Implementación</b></td>
		<td><b>De local a local</b></td>
		<td><b>De local a Azure</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 o 2012</td>
		<td rowspan = "2">Enterprise</td>
		<td>Instancia de clúster de conmutación por error</td>
		<td>Grupos de disponibilidad AlwaysOn</td>
		<td>Grupos de disponibilidad AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Grupos de disponibilidad AlwaysOn para alta disponibilidad</td>
		<td>Grupo de disponibilidad AlwaysOn</td>
		<td>Grupo de disponibilidad AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Standard</td>
		<td>Instancia de clúster de conmutación por error</td>
		<td>Replicación de Site Recovery con un reflejo local</td>
		<td>Replicación de Site Recovery con un reflejo local</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise o Standard</td>
		<td>Independiente</td>
		<td>Replicación de Site Recovery con un reflejo local</td>
		<td>Replicación de Site Recovery con un reflejo local</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise o Standard</td>
		<td>Independiente</td>
		<td>Replicación de Site Recovery con un reflejo local</td>
		<td>Replicación de Site Recovery con un reflejo local</td>
    </tr>
    </tbody>
    </table>


## Requisitos previos de implementación

Esto es lo necesita antes de empezar:


- La implementación local de SQL Server ejecuta una versión compatible de SQL Server. Normalmente también necesitará un Active Directory para SQL server.
- Los requisitos previos para el escenario que desea implementar. Los requisitos previos pueden encontrarse en cada artículo de la implementación. En [Información general sobre Azure Site Recovery](site-recovery-overview.md) puede ver una lista y vínculos a ellos.
- Si desea configurar la recuperación de Azure, necesitará ejecutar la herramienta [Evaluación de preparación de máquinas virtuales de Azure](http://www.microsoft.com/download/details.aspx?id=40898) en sus máquinas virtuales de SQL Server para asegurarse de que son compatibles con Azure y Site Recovery.

## Configuración de la protección

Necesitará realizar un par de pasos:

- Configuración de Active Directory
- Configuración de la protección de un servidor de clúster o independiente de SQL Server

### Configuración de Active Directory

Necesitará Active Directory en el sitio de recuperación secundario para que SQL Server se ejecute correctamente. Hay dos opciones:

- **Empresas pequeñas**: si tiene un pequeño número de aplicaciones y un solo controlador de dominio para el sitio local y desea conmutar por error todo el sitio, se recomienda utilizar la replicación de Site Recovery para replicar el controlador de dominio en el centro de datos secundario o en Azure.

- **Empresas medianas y grandes**: si tiene un gran número de aplicaciones, ejecuta un bosque de Active Directory y desea que la conmutación por error se realice por aplicaciones o cargas de trabajo, se recomienda configurar un controlador de dominio adicional en el centro de datos secundario o en Azure. Tenga en cuenta que si utiliza grupos de disponibilidad AlwaysOn para recuperar en un sitio remoto se recomienda configurar otro controlador de dominio adicional en el sitio secundario o en Azure, que se utilizará para la instancia de SQL Server recuperada.

Las instrucciones de este documento suponen que un controlador de dominio está disponible en la ubicación secundaria.

### Configuración de la protección de un servidor SQL Server independiente


En esta configuración, se recomienda que utilizar la replicación de Site Recovery para proteger el equipo de SQL Server. Los pasos exactos dependerán de si SQL Server está configurado como una máquina virtual o un servidor físico y si desea replicar en Azure o un sitio local secundario. Obtenga instrucciones para todos los escenarios de implementación en [Información general sobre Site Recovery](site-recovery-overview.md).


### Configuración de la protección para el clúster de SQL Server (2012 o 2014 Enterprise)

Si el servidor SQL Server utiliza grupos de disponibilidad para alta disponibilidad o una instancia de clúster de conmutación por error, se recomienda utilizar también grupos de disponibilidad en el sitio de recuperación. Tenga en cuenta de que esta guía es para aplicaciones que no utilizan transacciones distribuidas.

##### De local a local

1. [Configure bases de datos](https://msdn.microsoft.com/library/hh213078.aspx) en grupos de disponibilidad.
2. Cree una nueva red virtual en el sitio secundario.
3. Configure una VPN de sitio a sitio entre la nueva red virtual y el sitio principal.
4. Cree una máquina virtual en el sitio de recuperación e instale SQL Server en él.
5. Amplíe los grupos de disponibilidad AlwaysOn existentes a la nueva máquina virtual de SQL Server. Configure esta instancia de SQL Server como una copia de réplica asincrónica.
6. Cree un agente de escucha del grupo de disponibilidad o actualice el agente de escucha existente para incluir la máquina virtual de réplica asincrónica.
7. Asegúrese de que la granja de aplicaciones está configurada con el agente de escucha. Si realiza la configuración mediante el nombre del servidor de la base de datos, actualícelo para utilizar el agente de escucha para que no tenga que volver a configurar después de la conmutación por error.

#### De local a Azure

Cuando se replica a Azure, la configuración de varios grupos de disponibilidad es un desafío porque cada grupo de disponibilidad necesita un agente de escucha dedicado y la configuración de cada agente de escucha requiere un servicio de nube independiente. Se recomienda configurar un grupo de disponibilidad con todas las bases de datos incluidas.

1. Cree una red virtual de Azure.
2. Configure una VPN de sitio a sitio entre el sitio local y esta red.
3. Cree una nueva máquina virtual de Azure de SQL Server en la red y configúrela como una réplica del grupo de disponibilidad asincrónica. Si necesita alta disponibilidad para la capa de SQL Server después de la conmutación por error en Azure, a continuación, configure dos copias de réplica asincrónica en Azure.
4. Configure una réplica del controlador de dominio en la red virtual.
5. Asegúrese de que están habilitadas las extensiones de la máquina virtual en la máquina virtual. Esto es necesario para insertar en un plan de recuperación scripts específicos para SQL Server.
6. Configure un agente de escucha de SQL Server para el grupo de disponibilidad con el equilibrador de carga interno de Azure.
7. Configure la capa de aplicación para utilizar el agente de escucha para obtener acceso al nivel de base de datos. Para las aplicaciones que utilizan transacciones distribuidas, le recomendamos usar Site Recovery con la replicación de SAN o la replicación de sitio a sitio de VMWare.

### Configuración de la protección para el clúster de SQL Server (Standard o 2008 R2)

Para un clúster que ejecuta SQL Server Standard Edition o SQL Server 2008 R2, se recomienda utilizar la replicación de Site Recovery para proteger SQL Server.

#### De local a local

- Para un entorno de Hyper-V, si la aplicación utiliza transacciones distribuidas, se recomienda implementar [Site Recovery con la replicación de SAN](site-recovery-vmm-san.md).

- Para un entorno de VMware, se recomienda implementar la protección [VMware a VMware](site-recovery-vmware-to-vmware.md).

#### De local a Azure

Site Recovery no admite la compatibilidad con clústeres de invitado al replicar en Azure. SQL Server tampoco proporciona una solución de recuperación ante desastres de bajo costo para la edición Standard. Se recomienda proteger el clúster de SQL Server local en un servidor SQL Server independiente y recuperarla en Azure.


1. Configure una instancia de SQL Server independiente adicional en el sitio local.
2. Configure esta instancia para actuar como un reflejo para las bases de datos que deben protegerse. Configure el reflejo en modo de alta seguridad.
3.	Configure Site Recovery en el sitio local en función del entorno ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware](site-recovery-vmware-to-azure.md).
4.	Utilice la replicación de Site Recovery para replicar la nueva instancia de SQL Server en Azure. Es una copia de seguridad alta reflejada por lo que se sincronizará con el clúster principal, pero se puede replicar en Azure con la replicación de Site Recovery.

El gráfico siguiente muestra esta configuración.

![Clúster estándar](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##Creación de planes de recuperación

Los planes de recuperación agrupan las máquina que se deben conmutar por error. Obtenga más información sobre [planes de recuperación](site-recovery-create-recovery-plans.md) y [conmutación por error](site-recovery-failover.md) antes de empezar.


### Creación de un plan de recuperación para clústeres de SQL Server (SQL Server 2012/2014 Enterprise)

#### Configuración de scripts de SQL Server para la conmutación por error en Azure

En este escenario, se aprovechan scripts personalizados y la Automatización de Azure para los planes de recuperación con el fin de configurar una conmutación por error con scripts de los grupos de disponibilidad de SQL Server.

1.	Cree un archivo local para que el script realice la conmutación por error de un grupo de disponibilidad. Este script de ejemplo especifica una ruta de acceso al grupo de disponibilidad en la réplica de Azure y realiza la conmutación por error a esa instancia de réplica. Este script se ejecutará en la máquina virtual de réplica de SQL Server y se pasará con la extensión de script personalizado.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Cargue el script en un blob en una cuenta de Almacenamiento de Azure. Use este ejemplo:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Cree un runbook de automatización de Azure para invocar los scripts en la máquina virtual de réplica de SQL Server en Azure. Utilice este script de ejemplo para ello. [Obtenga más información](site-recovery-runbook-automation.md) sobre el uso de runbooks de automatización en los planes de recuperación.

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	Al crear un plan de recuperación para la aplicación, agregue un paso de script "pre-Group 1 boot" que invoca el runbook de automatización para realizar la conmutación por error de grupos de disponibilidad.

#### Configuración de scripts de SQL Server para la conmutación por error a un sitio secundario

1. Agregue este script de ejemplo a la biblioteca de VMM en los sitios principales y secundarios.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Al crear un plan de recuperación para la aplicación, agregue un paso de script "pre-Group 1 boot" que invoca el script para realizar la conmutación por error de grupos de disponibilidad.

### Creación de un plan de recuperación para clústeres de SQL Server (Standard)

#### Configuración de scripts de SQL Server para la conmutación por error en Azure

1.	Cree un archivo local para que el script realice la conmutación por error del reflejo de Base de datos de SQL Server. Utilice este script de ejemplo.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Cargue el script en un blob en una cuenta de Almacenamiento de Azure. Utilice este script de ejemplo.

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Cree un runbook de automatización de Azure para invocar el script en la máquina virtual de réplica de SQL Server en Azure. Utilice este script de ejemplo para ello. [Obtenga más información](site-recovery-runbook-automation.md) acerca del uso de runbooks de automatización en los planes de recuperación. Asegúrese de que el agente de máquina virtual se ejecuta en la máquina virtual de SQL Server conmutada por error antes de hacerlo.

    	workflow SQLAvailabilityGroupFailover
		{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    	$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    	#Connect to Azure
    	$AzureAccount = Add-AzureAccount -Credential $Cred
    	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    	InLineScript
    	{
     	#Update the script with name of your storage account, key and blob name
     	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. Agregue estos pasos en el plan de recuperación para conmutar por error la capa de SQL Server:

	- Para la conmutación por error planeada, agregue un script principal para apagar el clúster principal después de "desconectar el grupo".
	- Agregue la máquina virtual de reflejo de la base de datos SQL Server en el plan de recuperación, preferiblemente en el primer grupo de arranque.
3.	Agregue un script posterior a la conmutación por error para conmutar la copia reflejada en esta máquina virtual mediante el script de automatización anterior. Tenga en cuenta que si bien habrá cambiado el nombre de la instancia de base de datos, la capa de aplicación tendrá que reconfigurarse para usar la nueva base de datos.


#### Configuración de scripts de SQL Server para la conmutación por error a un sitio secundario

1.	Agregue este script de ejemplo a la biblioteca de VMM en los sitios principales y secundarios.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Agregue la máquina virtual de reflejo de la base de datos SQL Server en el plan de recuperación, preferiblemente en el primer grupo de arranque.
3.	Agregue un script posterior a la conmutación por error para conmutar la copia reflejada en esta máquina virtual mediante el script de VMM anterior. Tenga en cuenta que si bien habrá cambiado el nombre de la instancia de base de datos, la capa de aplicación tendrá que reconfigurarse para usar la nueva base de datos.





## Consideraciones sobre la conmutación por error de prueba

Si utiliza grupos de disponibilidad AlwaysOn, no puede realizar una conmutación por error de prueba de la capa de SQL Server. Como alternativa, considere las siguientes opciones:

- Opción 1

	1. Realice una conmutación por error de prueba de la aplicación y las capas de front-end.
	2. Actualice la capa de aplicación para obtener acceso a la copia de réplica en modo de solo lectura y realizar una prueba de solo lectura de la aplicación.

- Opción 2
1.	Cree una copia de la instancia de máquina virtual de SQL Server de réplica (con clon de VMM para la copia de seguridad de Azure o de sitio a sitio) y muéstrela en una red de prueba
2.	Realice la conmutación por error de prueba con el plan de recuperación.

## Consideraciones de la conmutación por recuperación

Para los clústeres SQL estándar, la conmutación por recuperación después de una conmutación por error no planeada requiere realizar una copia de seguridad de SQL Server y una restauración de la instancia reflejada en el clúster original y, a continuación, restablecer el reflejo.





 

<!---HONumber=58_postMigration-->