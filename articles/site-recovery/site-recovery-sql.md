<properties 
	pageTitle="Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery | Microsoft Azure" 
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
	ms.date="12/14/2015" 
	ms.author="raynew"/>


# Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery 

Site Recovery es un servicio de Azure que contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores virtuales. Site Recovery admite una serie de mecanismos de replicación para una protección, replicación y conmutación por error coherente de máquinas en Azure o un centro de datos secundario. Obtenga información general acerca de todos los escenarios de implementación en [Información general sobre Azure Site Recovery](site-recovery-overview.md).

 Este artículo describe cómo proteger el back-end de SQL Server de una aplicación con una combinación de tecnologías de SQL Server BCDR y Site Recovery. Debe tener una buena comprensión de las características de SQL Server BCDR (clústeres de conmutación por error, grupos de disponibilidad de AlwaysOn, creación de reflejos de bases de datos, trasvase de registros) y Site Recovery antes de implementar los escenarios descritos en este artículo.



## Información general

Muchas cargas de trabajo utilizan SQL Server como base. Las aplicaciones como SharePoint, Dynamics y SAP utilizan SQL Server para implementar servicios de datos. Las características de alta disponibilidad y recuperación ante desastres de SQL Server, como los grupos de disponibilidad de SQL Server, se usan para proteger y recuperar las bases de datos de SQL Server. Las aplicaciones implementan SQL Server en las configuraciones siguientes.


1. Un servidor SQL Server independiente: el servidor SQL Server y todas las bases de datos se hospedan en un solo equipo (una máquina física o virtual). Cuando se virtualiza, la agrupación en clústeres de host se usa para la alta disponibilidad local; no se implementa la alta disponibilidad de nivel invitado. 
2.	Instancias de agrupación en clústeres de conmutación por error de SQL Server (también conocidas como FCI AlwaysOn): en esta configuración, se establecen dos o más nodos de instancias de SQL Server con discos compartidos en un clúster de conmutación por error de Windows. Si alguna de las instancias de clúster de conmutación por error de SQL está inactiva, el clúster puede conmutar por error el SQL a otra instancia. Esta configuración se usa normalmente para alta disponibilidad en el sitio principal. Esto no protege frente a errores o una interrupción en el nivel de almacenamiento compartido. El disco compartido se puede implementar con ISCSI, canal de fibra o VHDX compartido.
3.	Grupos de disponibilidad AlwaysOn de SQL: en esta configuración, se establecen dos nodos en un clúster no compartido con bases de datos de SQL configuradas en un grupo de disponibilidad con replicación sincrónica y conmutación por error automática.

SQL Server también proporciona tecnologías nativas de recuperación ante desastres en ediciones empresariales para recuperar bases de datos en un sitio remoto. Cuando sea posible, se aprovecharán las siguientes tecnologías nativas de recuperación ante desastres de SQL y se usarán en integración para la creación de un plan de recuperación ante desastres basado en Azure Site Recovery.


1. Grupo de disponibilidad AlwayOn de SQL de recuperación ante desastres para ediciones SQL 2012 o 2014 Enterprise 
2.	Reflejo de Base de datos SQL en modo de alta seguridad para servidor de SQL Server Standard (cualquier versión) o SQL Server 2008 R2



Site Recovery puede proteger SQL Server cuando se ejecuta en una máquina virtual de Hyper-V, una máquina virtual de VMware o un servidor físico.

 |**De local a local** | **De local a Azure** 
---|---|---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí 
**Servidor físico** | Sí | Sí


## Compatibilidad e integración

A continuación se enumeran las versiones y ediciones de SQL Server a las que se aplica este artículo:


- SQL Server 2014 Enterprise y Standard
- SQL Server 2012 Enterprise y Standard
- SQL Server 2008 R2 Enterprise y Standard


Site Recovery se puede integrar con las tecnologías nativas de SQL Server BCDR resumidas en la tabla siguiente para proporcionar una solución de recuperación ante desastres.

**Característica** |**Detalles** | **Versión de SQL Server** 
---|---|---
**Grupo de disponibilidad AlwaysOn** | <p>Se ejecutan varias instancias independientes de SQL Server en un clúster de conmutación por error que tiene varios nodos.</p> <p>Las bases de datos se pueden repartir en grupos de conmutación por error que pueden copiarse (reflejarse) en instancias de SQL Server para que no se necesite ningún almacenamiento compartido.</p> <p>Proporciona recuperación ante desastres entre un sitio principal y uno o varios sitios secundarios. Se pueden configurar dos nodos en un clúster no compartido con bases de datos de SQL Server configuradas en un grupo de disponibilidad con replicación sincrónica y conmutación por error automática.</p> | SQL Server 2014/2012 Enterprise Edition
**Clústeres de conmutación por error (FCI AlwaysOn)** | <p>SQL Server aprovecha los clústeres de la conmutación por error de Windows para la alta disponibilidad de las cargas de trabajo locales de SQL Server.</p><p>Los nodos que ejecutan instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error. Si una instancia está inactiva, el clúster conmuta por error en otro.</p> <p>El clúster no protege ante errores o interrupciones en el almacenamiento compartido. El disco compartido se puede implementar con iSCSI, canal de fibra o VHDX compartidos.</p> | Ediciones de SQL Server Enterprise</p> <p>SQL Server Standard Edition (limitado solo a dos nodos)
**Creación de un reflejo de la base de datos (modo de alta seguridad)** | Protege una sola base de datos en una única copia secundaria. Disponible en modos de replicación de seguridad alta (sincrónica) y de alto rendimiento (asincrónica). No requiere un clúster de conmutación por error. | <p>SQL Server 2008 R2</p><p>Todas las ediciones de SQL Server Enterprise</p>
**SQL Server independiente** | SQL Server y la base de datos se hospedan en un único servidor (físico o virtual). Los clústeres de host se utilizan para lograr alta disponibilidad, si el servidor virtual. Sin alta disponibilidad de nivel de invitado. | Edición Enterprise o Standard




La siguiente tabla resume nuestras recomendaciones para integrar las tecnologías de SQL Server BCDR en la implementación de Site Recovery.

**Versión** |**Edición** | **Implementación** | **De local a local** | **De local a Azure** 
---|---|---|---|---
SQL Server 2014 o 2012 | Enterprise | Instancia de clúster de conmutación por error | Grupos de disponibilidad AlwaysOn | Grupos de disponibilidad AlwaysOn
 | Enterprise | Grupos de disponibilidad AlwaysOn para alta disponibilidad | Grupos de disponibilidad AlwaysOn | Grupos de disponibilidad AlwaysOn
 | Standard | Instancia de clúster de conmutación por error (FCI) | Replicación de Site Recovery con un reflejo local | Replicación de Site Recovery con un reflejo local
 | Enterprise o Standard | Independiente | Replicación de Site Recovery | Replicación de Site Recovery 
SQL Server 2008 R2 | Enterprise o Standard | Instancia de clúster de conmutación por error (FCI) | Replicación de Site Recovery con un reflejo local | Replicación de Site Recovery con un reflejo local
 | Enterprise o Standard | Independiente | Replicación de Site Recovery | Replicación de Site Recovery
SQL Server (cualquier versión) | Enterprise o Standard | Instancia de clúster de conmutación por error: aplicación de DTC | Replicación de Site Recovery | No compatible

## Requisitos previos de implementación

Esto es lo necesita antes de empezar:


- La implementación local de SQL Server ejecuta una versión compatible de SQL Server. Normalmente también necesitará un Active Directory para SQL server.
- Los requisitos previos para el escenario que desea implementar. Los requisitos previos pueden encontrarse en cada artículo de la implementación. En [Información general sobre Azure Site Recovery](site-recovery-overview.md) puede ver una lista y vínculos a ellos.
- Si desea configurar la recuperación de Azure, necesitará ejecutar la herramienta [Evaluación de preparación de máquinas virtuales de Azure](http://www.microsoft.com/download/details.aspx?id=40898) en sus máquinas virtuales de SQL Server para asegurarse de que son compatibles con Azure y Site Recovery.


## Configuración de la protección de AD

Necesitará Active Directory en el sitio de recuperación secundario para que SQL Server se ejecute correctamente. Hay dos opciones:

- **Empresas pequeñas**: si tiene un pequeño número de aplicaciones y un solo controlador de dominio para el sitio local y desea conmutar por error todo el sitio, se recomienda utilizar la replicación de Site Recovery para replicar el controlador de dominio en el centro de datos secundario o en Azure.

- **Empresas medianas y grandes**: si tiene un gran número de aplicaciones, ejecuta un bosque de Active Directory y desea que la conmutación por error se realice por aplicaciones o cargas de trabajo, se recomienda configurar un controlador de dominio adicional en el centro de datos secundario o en Azure. Tenga en cuenta que si utiliza grupos de disponibilidad AlwaysOn para recuperar en un sitio remoto se recomienda configurar otro controlador de dominio adicional en el sitio secundario o en Azure, que se utilizará para la instancia de SQL Server recuperada.

Las instrucciones de este documento suponen que un controlador de dominio está disponible en la ubicación secundaria. Puede consultar la guía de soluciones de AD DR [aquí](http://aka.ms/asr-ad).

## Integración con AlwaysOn de SQL a Azure

### De local a Azure

Azure Site Recovery (ASR) admite de forma nativa AlwaysOn de SQL. Si creó un grupo de disponibilidad de SQL con una máquina virtual de Azure que se esté configurada como "secundaria" puede usar ASR para administrar la conmutación por error de los grupos de disponibilidad.

Esta capacidad está actualmente en versión preliminar y está disponible cuando el centro de datos principal está administrado por System Center Virtual Machine Manager (VMM).

#### Entornos administrados por el servidor VMM
Si entra en un almacén de ASR verá una pestaña para los servidores de SQL Server en los elementos de la pestaña Protegido.

![Elementos protegidos](./media/site-recovery-sql/protected-items.png)

A continuación se muestran los pasos necesarios para integrar AlwaysOn de SQL con ASR.

##### Requisitos previos
- Un servidor de SQL Server local en un servidor independiente o en un clúster de conmutación por error 
- Una o más máquinas virtuales de Azure con SQL Server instalado
- Grupo de disponibilidad de SQL configurado entre el servidor local de SQL Server y el servidor de SQL Server que se ejecuta en Azure
- El acceso remoto a PowerShell debe habilitarse en el servidor local de SQL Server. El servidor VMM debe poder realizar llamadas remotas de PowerShell a SQL Server
- En el servidor local de SQL Server debe agregarse una cuenta de usuario en los grupos de usuario SQL con los permisos siguientes como mínimo
	- ALTER AVAILABILITY GROUP: [ referencia 1](https://msdn.microsoft.com/library/hh231018.aspx), [referencia 2](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: [referencia 1](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Debe crearse una cuenta de ejecución en el servidor VMM para la cuenta en el paso anterior
- Se debe instalar el módulo PS de SQL en los servidores SQL Server locales y en las máquinas virtuales de Azure
- Se debe instalar el Agente de máquina virtual en las máquinas virtuales que se ejecutan en Azure
- NTAUTHORITY\\System debe tener los siguientes permisos en el servidor de SQL Server que se ejecuta en las máquinas virtuales en Azure
	- ALTER AVAILABILITY GROUP: [ referencia 1](https://msdn.microsoft.com/library/hh231018.aspx), [referencia 2](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: [referencia 1](https://msdn.microsoft.com/library/ff877956.aspx#Security)

##### 1\. Incorporación de un servidor de SQL Server

Haga clic en Agregar SQL para agregar un nuevo servidor de SQL Server.

![Agregar SQL](./media/site-recovery-sql/add-sql.png)

Proporcione los detalles de SQL Server, VMM y las credenciales que se usarán para administrar el servidor de SQL Server.

![Agregar diálogo SQL](./media/site-recovery-sql/add-sql-dialog.png)

###### Parámetros
 - Nombre: nombre descriptivo que quiera proporcionar para hacer referencia a este servidor SQL Server
 - El FQDN de SQL Server: nombre de dominio completo (FQDN) del SQL Server de origen que se va a agregar. En caso de que el servidor SQL Server se instale en un clúster de conmutación por error, proporcione el FQDN del clúster y no el de cualquiera de los nodos del clúster. 
 - Instancia de SQL Server: elija la instancia SQL predeterminada o proporcione el nombre de la instancia SQL personalizada.
 - Servidor VMM: seleccione uno de los servidores VMM que ya se hayan registrado con Azure Site Recovery (ASR). ASR usará este servidor VMM para comunicarse con el servidor SQL Server
 - CUENTA DE EJECUCIÓN: proporcione el nombre de una de las cuentas de ejecución que se crearon en el servidor VMM seleccionado anteriormente. Esta cuenta de ejecución se usará para tener acceso a SQL Server y debe tener permisos de lectura y de conmutación por error en los grupos de disponibilidad en el servidor SQL Server. 

Una vez que agregue SQL Server aparecerá en la pestaña de servidores de SQL.

![Lista SQL Server](./media/site-recovery-sql/sql-server-list.png)

##### 2\. Incorporación de un grupo de disponibilidad de SQL

Una vez que se agrega el servidor SQL Server, el paso siguiente es agregar los grupos de disponibilidad a ASR. Para ello, profundice dentro del servidor SQL Server que agregó en el paso anterior y haga clic en Agregar un grupo de disponibilidad de SQL.

![Agregar SQL AG](./media/site-recovery-sql/add-sqlag.png)

El grupo de disponibilidad de SQL se puede replicar en una o varias máquinas virtuales en Azure. Al agregar el grupo de disponibilidad de SQL se le pide que proporcione el nombre y la suscripción de la máquina virtual de Azure donde desea que ASR realice la conmutación por error del grupo de disponibilidad.

![Agregar diálogo SQL AG](./media/site-recovery-sql/add-sqlag-dialog.png)

En el ejemplo anterior el grupo de disponibilidad DB1-AG se convertirá en principal en la máquina virtual SQLAGVM2 que se ejecuta dentro de la suscripción DevTesting2 en caso de una conmutación por error.

>[AZURE.NOTE]Solamente los grupos de disponibilidad que son principales en el servidor SQL Server que agregó en el paso anterior están disponibles para agregarse a ASR. Si convirtió a un grupo de disponibilidad en principal en el servidor SQL Server, o si agrega más grupos de disponibilidad en el servidor SQL Server después una vez agregado, realice una actualización con la opción que está disponible para ello en SQL Server.

#### 3\. Creación de un plan de recuperación

El siguiente paso es crear un plan de recuperación con las máquinas virtuales y los grupos de disponibilidad. Seleccione el mismo servidor VMM que usó en el paso 1 como origen y Microsoft Azure como destino.

![Creación del plan de recuperación](./media/site-recovery-sql/create-rp1.png)

![Creación del plan de recuperación](./media/site-recovery-sql/create-rp2.png)

En el ejemplo la aplicación de Sharepoint consta de 3 máquinas virtuales que usan un grupo de disponibilidad de SQL como su back-end. En este plan de recuperación podríamos seleccionar tanto el grupo de disponibilidad como la máquina virtual que constituyen la aplicación.

Puede personalizar aún más el plan de recuperación moviendo las máquinas virtuales a diferentes grupos de conmutación por error para secuenciar el orden de conmutación por error. Grupo de disponibilidad siempre se conmuta primero ya que se usará como un back-end de cualquier aplicación.

![Personalización de planes de recuperación](./media/site-recovery-sql/customize-rp.png)

#### 4\. Conmutación por error

Una vez que se agrega un grupo de disponibilidad a un plan de recuperación hay diferentes opciones de conmutación por error disponibles.

##### Conmutación por error planeada

Una conmutación por error planeada supone una conmutación sin pérdida de datos. Para lograrlo, el modo de disponibilidad del grupo de disponibilidad de SQL se establece primero en sincrónico y, a continuación, se desencadena una conmutación por error para convertir el grupo de disponibilidad en principal en la máquina virtual proporcionada, al tiempo que se agrega el grupo de disponibilidad a ASR. Una vez completada la conmutación por error, el modo de disponibilidad se establece con el mismo valor que tenía antes de que se desencadenara la conmutación planeada.

##### Conmutación por error no planeada

Una conmutación por error imprevista puede dar lugar a una pérdida de datos. Al desencadenar una conmutación por error que no está planeada el modo de disponibilidad del grupo de disponibilidad no se cambia y se convierte en principal en la máquina virtual proporcionada al agregar el grupo de disponibilidad al ASR. Una vez que la conmutación por error imprevista se completa y el servidor local que ejecuta SQL Server vuelve a estar disponible, es necesario desencadenar una replicación inversa en el grupo de disponibilidad. Tenga en cuenta que esta acción no está disponible en el plan de recuperación y se puede realizar en el grupo de disponibilidad de SQL en la pestaña de servidores SQL Server

##### Test Failover
La conmutación por error de prueba no se admite para los grupo de disponibilidad de SQL. Si se desencadena una conmutación por error de prueba de un plan de recuperación que contenga un grupo de disponibilidad de SQL, se omitirá la conmutación por error para el grupo de disponibilidad.

Como alternativa, considere las siguientes opciones:

###### Opción 1



1. Realice una conmutación por error de prueba de la aplicación y las capas de front-end.

2. Actualice la capa de aplicación para obtener acceso a la copia de réplica en modo de solo lectura y realizar una prueba de solo lectura de la aplicación.

###### Opción 2

1.	Cree una copia de la instancia de máquina virtual de SQL Server de réplica (con clon de VMM para la copia de seguridad de Azure o de sitio a sitio) y muéstrela en una red de prueba
2.	Realice la conmutación por error de prueba con el plan de recuperación.

#### Conmutación por recuperación

Si desea que el grupo de disponibilidad vuelva a ser principal en el servidor local de SQL Server, puede hacerlo desencadenando una conmutación por error planeada en el plan de recuperación y eligiendo la dirección de Microsoft Azure al servidor VMM local

#### Replicación inversa

Después de una conmutación por error no planeada tiene que desencadenarse una replicación inversa en el grupo de disponibilidad para continuar con la replicación. Hasta que esto se realiza la replicación permanece suspendida.


### Entornos no administrados por el servidor VMM

Para los entornos que no están administrados por un servidor VMM, se pueden usar los runbooks de Automatización de Azure para configurar una conmutación por error con script de los grupos de disponibilidad de SQL. A continuación se muestran los pasos a seguir para esta configuración:


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

###De local a local
Si el servidor SQL Server utiliza grupos de disponibilidad para alta disponibilidad o una instancia de clúster de conmutación por error, se recomienda utilizar también grupos de disponibilidad en el sitio de recuperación. Tenga en cuenta de que esta guía es para aplicaciones que no utilizan transacciones distribuidas.


1. [Configure bases de datos](https://msdn.microsoft.com/library/hh213078.aspx) en grupos de disponibilidad.
2. Cree una nueva red virtual en el sitio secundario.
3. Configure una VPN de sitio a sitio entre la nueva red virtual y el sitio principal.
4. Cree una máquina virtual en el sitio de recuperación e instale SQL Server en él.
5. Amplíe los grupos de disponibilidad AlwaysOn existentes a la nueva máquina virtual de SQL Server. Configure esta instancia de SQL Server como una copia de réplica asincrónica.
6. Cree un agente de escucha del grupo de disponibilidad o actualice el agente de escucha existente para incluir la máquina virtual de réplica asincrónica.
7. Asegúrese de que la granja de aplicaciones está configurada con el agente de escucha. Si realiza la configuración mediante el nombre del servidor de la base de datos, actualícelo para utilizar el agente de escucha para que no tenga que volver a configurar después de la conmutación por error.

Para las aplicaciones que usan transacciones distribuidas, le recomendamos usar [Site Recovery con la replicación de SAN](site-recovery-vmm-san.md) o la [replicación de sitio a sitio de VMWare](site-recovery-vmware-to-vmware.md).

#### Consideraciones del Plan de recuperación

1. Agregue este script de ejemplo a la biblioteca de VMM en los sitios principales y secundarios.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Al crear un plan de recuperación para la aplicación, agregue un paso de script "pre-Group 1 boot" que invoca el script para realizar la conmutación por error de grupos de disponibilidad.



## Configuración de la protección de un servidor SQL Server independiente


En esta configuración, se recomienda que utilizar la replicación de Site Recovery para proteger el equipo de SQL Server. Los pasos exactos dependerán de si SQL Server está configurado como una máquina virtual o un servidor físico y si desea replicar en Azure o un sitio local secundario. Obtenga instrucciones para todos los escenarios de implementación en [Información general sobre Site Recovery](site-recovery-overview.md).


## Configuración de la protección del clúster de SQL Server (Standard o 2008 R2)

Para un clúster que ejecuta SQL Server Standard Edition o SQL Server 2008 R2, se recomienda utilizar la replicación de Site Recovery para proteger SQL Server.

#### De local a local

- Si la aplicación usa transacciones distribuidas, se recomienda implementar [Site Recovery con la replicación de SAN](site-recovery-vmm-san.md) para un entorno de Hyper-V y [VMware a VMware](site-recovery-vmware-to-vmware.md) para un entorno VMware.

- Para las aplicaciones que no sean DTC, aproveche el enfoque anterior para recuperar el clúster como un servidor independiente mediante el uso de un reflejo de la base de datos local de seguridad alta.

#### De local a Azure

Site Recovery no admite la compatibilidad con clústeres de invitado al replicar en Azure. SQL Server tampoco proporciona una solución de recuperación ante desastres de bajo costo para la edición Standard. Se recomienda proteger el clúster de SQL Server local en un servidor SQL Server independiente y recuperarla en Azure.


1. Configure una instancia de SQL Server independiente adicional en el sitio local.
2. Configure esta instancia para actuar como un reflejo para las bases de datos que deben protegerse. Configure el reflejo en modo de alta seguridad.
3.	Configure Site Recovery en el sitio local en función del entorno ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware](site-recovery-vmware-to-azure.md).
4.	Utilice la replicación de Site Recovery para replicar la nueva instancia de SQL Server en Azure. Es una copia de seguridad alta de reflejo, por lo que se sincronizará con el clúster principal, pero se puede replicar en Azure con la replicación de Site Recovery.

El gráfico siguiente muestra esta configuración.

![Clúster estándar](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Consideraciones de la conmutación por recuperación

Para los clústeres SQL estándar, la conmutación por recuperación después de una conmutación por error no planeada requiere realizar una copia de seguridad de SQL y una restauración a partir de la instancia de reflejo en el clúster original y, a continuación, restablecer el reflejo.










 

<!---HONumber=AcomDC_1217_2015-->