---
title: "Replicación de aplicaciones con SQL Server y Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo replicar SQL Server mediante Azure Site Recovery, una de las funcionalidades de recuperación ante desastres de SQL Server."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f822756c0ce45c98b95a0cec2efd1353aff71561
ms.openlocfilehash: f2d8a9221a989f9e79b6f4e17d9448303544ffd6
ms.lasthandoff: 02/22/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery

En este artículo se describe cómo proteger el back-end de SQL Server de una aplicación con una combinación de tecnologías de continuidad empresarial y recuperación ante desastres (BCDR) de SQL Server y [Azure Site Recovery](site-recovery-overview.md).

Antes de empezar, asegúrese de que comprende las funcionalidades de recuperación ante desastres de SQL Server, incluidos los clústeres de conmutación por error, los grupos de disponibilidad AlwaysOn, la creación de reflejo de la base de datos y el trasvase de registros.


## <a name="sql-server-deployments"></a>Implementaciones de SQL Server

Muchas cargas de trabajo usan SQL Server como base que, además, se puede integrar con aplicaciones como SharePoint, Dynamics y SAP para implementar servicios de datos.  SQL Server se puede implementar de varias maneras:

* **Servidor SQL independiente**: SQL Server y todas las bases de datos se hospedan en una sola máquina (física o virtual). Cuando se virtualiza, la agrupación en clústeres de host se utiliza para conseguir una elevada disponibilidad local. No se implementa alta disponibilidad de nivel de invitado.
* **Instancias de agrupación en clústeres de conmutación por error de SQL Server**: dos o más nodos que ejecutan instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error de Windows. Si un nodo está inactivo, el clúster puede conmutar por error SQL Server en otra instancia. Esta configuración normalmente se usa para implementar la alta disponibilidad en un sitio principal. Esta implementación no protege frente a errores o una interrupción en la capa de almacenamiento compartido. Un disco compartido se puede implementar con iSCSI, canal de fibra o VHDx compartido.
* **Grupos de disponibilidad AlwaysOn de SQL**: se configuran dos o más nodos en un clúster no compartido, con bases de datos SQL Server configuradas en un grupo de disponibilidad, con replicación sincrónica y conmutación por error automática.

 En este artículo se aprovechan las siguientes tecnologías nativas de recuperación ante desastres de SQL para recuperar bases datos en un sitio de remoto:

* Grupos de disponibilidad AlwayOn de SQL para proponer recuperación ante desastres para ediciones empresariales de SQL 2012 o 2014.
* Reflejo de base de datos SQL en modo de alta seguridad para SQL Server Standard Edition (cualquier versión) o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Compatibilidad de Site Recovery

### <a name="supported-scenarios"></a>Escenarios admitidos
Site Recovery puede proteger SQL Server como se resume en la tabla.

**Escenario** | **En un sitio secundario** | **En Azure**
--- | --- | ---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí
**Servidor físico** | Sí | Sí

### <a name="supported-sql-server-versions"></a>Versiones admitidas de SQL Server
Se admiten estas versiones de SQL Server en los escenarios admitidos:

* SQL Server 2014 Enterprise y Standard
* SQL Server 2012 Enterprise y Standard
* SQL Server 2008 R2 Enterprise y Standard

### <a name="supported-sql-server-integration"></a>Integración de SQL Server admitida

Site Recovery se puede integrar con las tecnologías nativas de SQL Server BCDR resumidas en la tabla para proporcionar una solución de recuperación ante desastres.

**Característica** | **Detalles** | **SQL Server** |
--- | --- | ---
**Grupos de disponibilidad AlwaysOn** | Se ejecutan varias instancias independientes de SQL Server, cada una en un clúster de conmutación por error que tiene varios nodos.<br/><br/>Las bases de datos se pueden agrupar en grupos de conmutación por error que se puede copiar (reflejar) en instancias de SQL Server para que no se necesite ningún almacenamiento compartido.<br/><br/>Proporciona recuperación ante desastres entre un sitio principal y uno o más sitios secundarios. Dos nodos pueden configurarse en un clúster no compartido con Bases de datos de SQL Server configurado en un grupo de disponibilidad con replicación sincrónica y conmutación por error automática. | SQL Server 2014 y 2012 Enterprise Edition
**Clústeres de conmutación por error (FCI AlwaysOn)** | SQL Server aprovecha la agrupación en clústeres de conmutación por error de Windows para conseguir alta disponibilidad de las cargas de trabajo locales de SQL Server.<br/><br/>Los nodos que ejecutan instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error. Si una instancia está inactiva, el clúster conmuta por error a una diferente.<br/><br/>El clúster no protege frente a errores o interrupciones en el almacenamiento compartido. El disco compartido se puede implementar con iSCSI, canal de fibra o VHDX compartido. | SQL Server Enterprise Edition<br/><br/>SQL Server Standard Edition (limitada a solo dos nodos)
**Creación de un reflejo de la base de datos (modo de alta seguridad)** | Protege una sola base de datos en una única copia secundaria. Disponible en modos de replicación de seguridad alta (sincrónica) y de alto rendimiento (asincrónica). No requiere un clúster de conmutación por error. | SQL Server 2008 R2<br/><br/>Todas las ediciones de SQL Server Enterprise
**SQL Server independiente** | SQL Server y la base de datos se hospedan en un único servidor (físico o virtual). Los clústeres de host se utilizan para lograr alta disponibilidad, si el servidor virtual. Sin alta disponibilidad de nivel de invitado. | Edición Enterprise o Standard

## <a name="deployment-recommendations"></a>Recomendaciones de implementación

**Versión** | **Implementación** | **De local a secundario** | **De local a Azure** |
--- | --- | --- | --- | --- |
**SQL Server 2014/2012 Enterprise FCI** | Clúster de conmutación por error | Grupos de disponibilidad AlwaysOn | Grupos de disponibilidad AlwaysOn
**SQL Server 2014/2012 AlwaysOn** | Grupos de disponibilidad AlwaysOn | AlwaysOn | AlwaysOn
**SQL Server 2014/2012 Standard FCI** | Clúster de conmutación por error | Replicación de Site Recovery con un reflejo local | Replicación de Site Recovery con un reflejo local
**SQL Server 2014/2012 Enterprise/Standard** | Independiente | Replicación de Site Recovery | Replicación de Site Recovery
**SQL Server 2008 R2 Enterprise/Standard** | FCI |Replicación de Site Recovery con un reflejo local |Replicación de Site Recovery con un reflejo local |
**SQL Server 2008 R2 Enterprise/Standard** | Independiente |Replicación de Site Recovery | Replicación de Site Recovery
**SQL Server (todas las versiones) Enterprise/Standard** |FCI: aplicación de DTC | Replicación de Site Recovery |No compatible

## <a name="deployment-prerequisites"></a>Requisitos previos de implementación

* La implementación local de SQL Server ejecuta una versión compatible de SQL Server. Normalmente, también necesitará Active Directory para SQL Server.
* Requisitos para el escenario que desea implementar. Obtenga más información sobre los requisitos de compatibilidad para la [replicación en Azure](site-recovery-support-matrix-to-azure.md) y la replicación [local](site-recovery-support-matrix.md), y los [requisitos previos de implementación](site-recovery-prereq.md).
* Para configurar la recuperación de Azure, ejecute la herramienta [Evaluación de preparación de máquinas virtuales de Azure](http://www.microsoft.com/download/details.aspx?id=40898) en sus máquinas virtuales de SQL Server para asegurarse de que son compatibles con Azure y Site Recovery.

## <a name="set-up-active-directory"></a>Configuración de Active Directory

Configure Active Directory en el sitio de recuperación secundario para que SQL Server se ejecute correctamente.

* **Empresas pequeñas**: con un pequeño número de aplicaciones y un solo controlador de dominio para el sitio local, si desea conmutar por error todo el sitio, se recomienda utilizar la replicación de Site Recovery para replicar el controlador de dominio en el centro de datos secundario o en Azure.
* **Empresas medianas y grandes**: si tiene un gran número de aplicaciones, un bosque de Active Directory y desea que la conmutación por error se realice por aplicaciones o cargas de trabajo, se recomienda configurar un controlador de dominio adicional en el centro de datos secundario o en Azure. Si utiliza grupos de disponibilidad AlwaysOn para recuperar en un sitio remoto se recomienda configurar otro controlador de dominio adicional en el sitio secundario o en Azure, que se utilizará para la instancia de SQL Server recuperada.

Las instrucciones de este artículo suponen que un controlador de dominio está disponible en la ubicación secundaria. [Más información](site-recovery-active-directory.md) sobre la protección de Active Directory con Site Recovery.

## <a name="integrate-with-sql-server-always-on-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>Integración con SQL Server Always On para la replicación en Azure (portal clásico con un servidor VMM o de configuración)


Site Recovery admite SQL AlwaysOn de forma nativa. Si creó un grupo de disponibilidad de SQL con una máquina virtual de Azure configurada como ubicación secundaria, puede usar Site Recovery para administrar la conmutación por error de los grupos de disponibilidad.

> [!NOTE]
> Esta funcionalidad actualmente se encuentra disponible en modo de vista previa. Está disponible cuando el sitio primario tiene servidores host de Hyper-V administrados en nubes de System Center VMM, o cuando ha configurado la [replicación de VMware](site-recovery-vmware-to-azure.md). La funcionalidad no está actualmente disponible en el nuevo Azure Portal. En este momento, no está disponible en el nuevo Portal de Azure.
>
>

#### <a name="before-you-start"></a>Antes de comenzar

Para integrar SQL AlwaysOn con Site Recovery necesita:

* Una instancia de SQL Server local (en un servidor independiente o un clúster de conmutación por error).
* Una o más máquinas virtuales de Azure con SQL Server instalado.
* Un grupo de disponibilidad de SQL Server configurado entre la instancia de SQL Server local y la instancia de SQL Server que se ejecuta en Azure.
* Un acceso remoto a PowerShell habilitado en el servidor local de SQL Server. El servidor VMM o el servidor de configuración deben poder realizar llamadas remotas de PowerShell a la máquina de SQL Server.
* Se debe agregar una cuenta de usuario a la máquina de SQL Server local. Agréguela en un grupo de SQL Server con al menos estos permisos:
  * ALTER AVAILABILITY GROUP: los permisos se describen [aquí](https://msdn.microsoft.com/library/hh231018.aspx) y [aquí](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3).
  * ALTER DATABASE: los permisos se describen  [aquí](https://msdn.microsoft.com/library/ff877956.aspx#Security).
* Si ejecuta VMM, se debe crear la cuenta de RunAs en el servidor VMM
- Si está ejecutando VMware, se debe crear una cuenta en el servidor de configuración mediante el archivo ejecutable CSPSConfigtool.exe
* Se debe instalar el módulo PS de SQL en los servidores SQL que se ejecutan en el entorno local y en máquinas virtuales de Azure.
* El agente de máquinas virtuales se debe instalar en Azure Virtual Machines.
* NTAUTHORITY\System debe tener los siguientes permisos en la instancia de SQL Server que se ejecuta en Azure Virtual Machines.
  * ALTER AVAILABILITY GROUP: los permisos se describen [aquí](https://msdn.microsoft.com/library/hh231018.aspx) y [aquí](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3).
  * ALTER DATABASE: los permisos se describen  [aquí](https://msdn.microsoft.com/library/ff877956.aspx#Security).

### <a name="add-a-sql-server"></a>Incorporación de un servidor de SQL Server
1. Haga clic en **Agregar SQL** para agregar un nuevo servidor SQL Server.

    ![Agregar SQL](./media/site-recovery-sql/add-sql.png)
2. En **Configurar opciones de SQL** > **Nombre**, escriba un nombre descriptivo para referirse al servidor de SQL Server.
3. **SQL Server (FQDN)**, especifique el FQDN del servidor SQL Server de origen que quiere agregar. En caso de que el servidor SQL Server se instale en un clúster de conmutación por error, proporcione el FQDN del clúster y no el de los nodos del clúster.  
4. En **Instancia de SQL Server**, elija la instancia predeterminada o proporcione el nombre personalizado.
5. En **Servidor de administración**, seleccione un servidor VMM o un servidor de configuración registrado en el almacén. Site Recovery utiliza este servidor para comunicarse con el servidor de SQL Server.
6. En **Cuenta de ejecución**, proporcione el nombre de la cuenta de ejecución de VMM o la cuenta del servidor de configuración. Esta cuenta se usa para tener acceso a SQL Server y debe tener permisos de lectura y de conmutación por error para los grupos de disponibilidad en la máquina de SQL Server.

    ![Agregar diálogo SQL](./media/site-recovery-sql/add-sql-dialog.png)

Después de agregar el servidor SQL Server, este aparecerá en la pestaña **Servidores SQL**.

![Lista SQL Server](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>Incorporación de un grupo de disponibilidad de SQL

1. En el servidor de SQL Server que agregó, haga clic en **Add SQL Availability Group** (Agregar grupo de disponibilidad de SQL).

    ![Agregar SQL AG](./media/site-recovery-sql/add-sqlag.png)
2. El grupo de disponibilidad se puede replicar en una o varias máquinas virtuales de Azure. Cuando agrega el grupo, debe proporcionar el nombre y la suscripción de la máquina virtual de Azure en la que desea que Site Recovery conmute por error el grupo.

    ![Agregar diálogo SQL AG](./media/site-recovery-sql/add-sqlag-dialog.png)
3. En este ejemplo, el grupo de disponibilidad DB1-AG se convertirá en principal en la máquina virtual SQLAGVM2 que se ejecuta dentro de la suscripción DevTesting2 en caso de una conmutación por error.

> [!NOTE]
> Solamente los grupos de disponibilidad que son principales en el servidor SQL Server que agregó se pueden agregar a Site Recovery. Si convirtió un grupo de disponibilidad en principal en el servidor de SQL Server, o si agregó más grupos de disponibilidad en el servidor SQL Server una vez agregado, actualice el grupo en SQL Server.
>
>

### <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación

Cree un plan de recuperación con las máquinas virtuales y los grupos de disponibilidad. Seleccione el servidor VMM, o el servidor de configuración, como origen y Azure como destino.

![Creación del plan de recuperación](./media/site-recovery-sql/create-rp1.png)

![Creación del plan de recuperación](./media/site-recovery-sql/create-rp2.png)

En el ejemplo, la aplicación de Sharepoint consta de tres máquinas virtuales que usan un grupo de disponibilidad de SQL como su back-end. En este plan de recuperación, podemos seleccionar el grupo de disponibilidad y las máquinas virtuales de la aplicación. Puede personalizar aún más el plan de recuperación moviendo las máquinas virtuales a diferentes grupos de conmutación por error para secuenciar el orden de conmutación por error. El grupo de disponibilidad siempre se conmuta por error en primer lugar ya que se utiliza como back-end de la aplicación.

![Personalización de planes de recuperación](./media/site-recovery-sql/customize-rp.png)

### <a name="fail-over"></a>Conmutación por error

Una vez que se agrega un grupo de disponibilidad a un plan de recuperación hay diferentes opciones disponibles.

**Conmutación por error** | **Detalles**
--- | ---
**Conmutación por error planeada** | Una conmutación por error planeada supone una conmutación por error sin pérdida de datos. Para lograrlo, el modo del grupo de disponibilidad de SQL se establece primero en sincrónico y, a continuación, se desencadena una conmutación por error para convertir el grupo de disponibilidad en principal en la máquina virtual proporcionada, al tiempo que se agrega el grupo a Site Recovery. Una vez completada la conmutación por error, el modo de disponibilidad se establece con el mismo valor que tenía antes de que se desencadenara la conmutación planeada.
**Conmutación por error no planeada** | Una conmutación por error no planeada puede dar lugar a una pérdida de datos. Durante el desencadenamiento de la conmutación por error no planeada no se cambia el modo de disponibilidad del grupo. Este se convertirá en principal en la máquina virtual proporcionada al agregar el grupo de disponibilidad a Site Recovery. Una vez que la conmutación por error imprevista se completa y el servidor local que ejecuta SQL Server vuelve a estar disponible, es necesario desencadenar una replicación inversa en el grupo de disponibilidad. Esta acción está disponible en **Servidores SQL Server** > **Grupo de disponibilidad de SQL**y no en el plan de recuperación.
**Conmutación por error de prueba** |La conmutación por error de prueba no se admite para el grupo de disponibilidad de SQL. Si desencadena una conmutación por error de prueba, esta se omitirá para el grupo de disponibilidad.

Pruebe las siguientes opciones de conmutación por error.

**Opción** | **Detalles**
--- | ---
**Opción 1** | 1. Realice una conmutación por error de prueba de la aplicación y las capas de front-end.<br/><br/>2. Actualice la capa de aplicación para obtener acceso a la copia de réplica en modo de solo lectura y realizar una prueba de solo lectura de la aplicación.
**Opción 2** | 1. Cree una copia de la instancia de máquina virtual de SQL Server de réplica (con clon de VMM para la copia de seguridad de Azure o de sitio a sitio) y muéstrela en una red de prueba<br/><br/> 2. Realice la conmutación por error de prueba con el plan de recuperación.

### <a name="fail-back"></a>Conmutación por recuperación

Si desea que el grupo de disponibilidad vuelva a ser principal en el servidor local de SQL Server, puede desencadenar una conmutación por error planeada en el plan de recuperación y elegir la dirección de Microsoft Azure al servidor VMM local.

> [!NOTE]
> Después de una conmutación por error no planeada tiene que desencadenarse una replicación inversa en el grupo de disponibilidad para continuar con la replicación.  Hasta que esto se realiza la replicación permanece suspendida.
>
>

## <a name="integrate-with-sql-server-always-on-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>Integración con SQL Server Always On para la replicación en Azure (Azure Portal, portal clásico con un servidor VMM o de configuración)

Estas instrucciones son relevantes si va a realizar la integración con grupos de disponibilidad de SQL Server en el nuevo Azure Portal, o en el portal clásico si no usa un servidor VMM o un servidor de configuración. En este escenario, se pueden usar los runbooks de Azure Automation para configurar una conmutación por error con script de los grupos de disponibilidad de SQL.

Esto es lo que debe hacer:

1. Cree un archivo local para un script que realiza la conmutación por error de un grupo de disponibilidad. Este script de ejemplo especifica una ruta de acceso al grupo de disponibilidad en la réplica de Azure y realiza la conmutación por error a esa instancia de réplica. Este script se ejecutará en la máquina virtual de réplica de SQL Server y se pasará con la extensión del script personalizado.

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. Cargue el script en un blob en una cuenta de Almacenamiento de Azure. Use este ejemplo:

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. Cree un runbook de Azure Automation para invocar los scripts en la máquina virtual de réplica de SQL Server en Azure. Utilice este script de ejemplo para ello. [Obtenga más información](site-recovery-runbook-automation.md) sobre el uso de runbooks de automatización en los planes de recuperación.

4. Al crear un plan de recuperación para la aplicación, agregue un paso de script "pre-Group 1 boot" que invoca el runbook de automatización para realizar la conmutación por error de grupos de disponibilidad.


5. SQL AlwaysOn no admite de forma nativa la conmutación por error de prueba. Por consiguiente, es recomendable que:
    1. Configure [Azure Backup](../backup/backup-azure-vms.md) en la máquina virtual que hospeda la réplica del grupo de disponibilidad en Azure.
    1. Antes de desencadenar la conmutación por error del plan de recuperación, recupere la máquina virtual a partir de la copia de seguridad realizada en el paso anterior.
    1. Realice una conmutación por error del plan de recuperación.


> [!NOTE]
> En el siguiente script se supone que el grupo de disponibilidad de SQL está hospedado en una máquina virtual clásica de Azure y que el nombre de la máquina virtual restaurada en el paso&2; es SQLAzureVM-Test. Modifique el script de acuerdo con el nombre que use para la máquina virtual recuperada.
>
>


     ``workflow SQLAvailabilityGroupFailover
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
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integración con SQL Server Always On para la replicación en un sitio local secundario

Si el servidor SQL Server utiliza grupos de disponibilidad para alta disponibilidad (o un FCI), se recomienda utilizar también grupos de disponibilidad en el sitio de recuperación. Tenga en cuenta que esto es aplicable a aplicaciones que no utilizan transacciones distribuidas.

1. [Configure bases de datos](https://msdn.microsoft.com/library/hh213078.aspx) en grupos de disponibilidad.
2. Cree una red virtual en el sitio secundario.
3. Configure una conexión VPN de sitio a sitio entre la red virtual y el sitio principal.
4. Cree una máquina virtual en el sitio de recuperación e instale SQL Server en ella.
5. Amplíe los grupos de disponibilidad AlwaysOn existentes a la nueva máquina virtual de SQL Server. Configure esta instancia de SQL Server como una copia de réplica asincrónica.
6. Cree un agente de escucha del grupo de disponibilidad o actualice el agente de escucha existente para incluir la máquina virtual de réplica asincrónica.
7. Asegúrese de que la granja de aplicaciones está configurada con el agente de escucha. Si realiza la configuración mediante el nombre del servidor de la base de datos, actualícelo para utilizar el agente de escucha para que no tenga que volver a configurar después de la conmutación por error.

Para las aplicaciones que usan transacciones distribuidas, le recomendamos implementar Site Recovery con la [replicación de SAN](site-recovery-vmm-san.md) o la [replicación de sitio a sitio de servidor físico/VMware](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Consideraciones del plan de recuperación
1. Agregue este script de ejemplo a la biblioteca de VMM en los sitios principales y secundarios.

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. Al crear un plan de recuperación para la aplicación, agregue un paso de script "pre-Group 1 boot" que invoca el script para realizar la conmutación por error de grupos de disponibilidad.

## <a name="protect-a-standalone-sql-server"></a>Protección de un servidor SQL Server independiente

En este escenario, se recomienda que utilice la replicación de Site Recovery para proteger la máquina de SQL Server. Los pasos exactos dependerán de si SQL Server está en una máquina virtual o en un servidor físico y si desea replicar en Azure o en un sitio local secundario. Más información acerca de [escenarios de Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Protección de un clúster de SQL Server (Standard Edition o Windows Server 2008 R2)

Para un clúster que ejecuta SQL Server Standard Edition o SQL Server 2008 R2, se recomienda utilizar la replicación de Site Recovery para proteger SQL Server.

### <a name="on-premises-to-on-premises"></a>De local a local

* Si la aplicación usa transacciones distribuidas, se recomienda implementar [Site Recovery con la replicación de SAN](site-recovery-vmm-san.md) en caso de un entorno de Hyper-V y [VMware/servidor físico a VMware](site-recovery-vmware-to-vmware.md) si se trata de un entorno de VMware.
* Para las aplicaciones que no sean DTC, use el enfoque anterior para recuperar el clúster como un servidor independiente mediante el uso de un reflejo de la base de datos local de seguridad alta.

### <a name="on-premises-to-azure"></a>De local a Azure

Site Recovery no proporciona la compatibilidad con clústeres de invitado al replicar en Azure. SQL Server tampoco proporciona una solución de recuperación ante desastres de bajo costo para la edición Standard. En este escenario, se recomienda proteger el clúster de SQL Server local en un servidor SQL Server independiente y recuperarlo en Azure.

1. Configure una instancia de SQL Server independiente adicional en el sitio local.
2. Configure esta instancia para actuar como un reflejo para las bases de datos que desea proteger. Configure el reflejo en modo de alta seguridad.
3. Configure Site Recovery en el sitio local para [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o para [máquinas virtuales de VMware o servidores físicos](site-recovery-vmware-to-azure-classic.md).
4. Utilice la replicación de Site Recovery para replicar la nueva instancia de SQL Server en Azure. Como es una copia de alta seguridad de reflejo, se sincronizará con el clúster principal, pero se puede replicar en Azure con la replicación de Site Recovery.


![Clúster estándar](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>Consideraciones de la conmutación por recuperación

Para los clústeres de SQL Server Standard, la conmutación por recuperación después de una conmutación por error no planeada requiere realizar una copia de seguridad de SQL Server y una restauración de la instancia reflejada en el clúster original y, a continuación, restablecer el reflejo.

## <a name="next-steps"></a>Pasos siguientes
[Obtenga información](site-recovery-components.md) acerca de la arquitectura de Site Recovery.

