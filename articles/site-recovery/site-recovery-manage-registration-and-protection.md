---
title: "Eliminación de servidores y deshabilitación de la protección | Microsoft Docs"
description: "En este artículo se describe cómo anular el registro de servidores desde un almacén de Site Recovery y deshabilitar la protección para máquinas virtuales y servidores físicos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/28/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f57c88cbace41af233f542880c6199b3e278700e
ms.openlocfilehash: c8d893dbac1a4f6cb3f05f857e186bca155e5865


---

# <a name="remove-servers-and-disable-protection"></a>Quitar servidores y deshabilitar la protección

El servicio Azure Site Recovery contribuye a su estrategia de recuperación ante desastres y continuidad empresarial (BCDR). El servicio organiza la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Site Recovery?](site-recovery-overview.md)

Este artículo describe cómo anular el registro de servidores desde un almacén de Recovery Services en Azure Portal y cómo deshabilitar la protección para las máquinas protegidas por Site Recovery.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-connected-configuration-server"></a>Anulación del registro de un servidor de configuración conectado

Si replica máquinas virtuales de VMware o servidores físicos de Windows/Linux en Azure, puede anular el registro de un servidor de configuración conectado desde un almacén de la forma siguiente:

1. Deshabilite la protección de la máquina. En **Protected Items** > **Replicated items** (Elementos protegidos > Elementos replicados), haga clic con el botón derecho en la máquina > **Eliminar**.
2. Anule la asociación de todas las directivas. En **Site Recovery Infrastructure** > **For VMWare & Physical Machines** > **Replication Policies**, (Infraestructura de Site Recovery > Para VMWare y máquinas físicas > Directivas de replicación) haga doble clic en la directiva asociada. Haga clic con el botón derecho en el servidor de configuración > **Disassociate** (Desasociar).
3. Retire cualquier proceso local o servidores de destino maestros adicionales. En **Site Recovery Infrastructure** > **For VMWare & Physical Machines** > **Configuration Servers** (Infraestructura de Site Recovery > Para VMWare y máquinas físicas > Servidores de configuración), haga clic con el botón derecho > **Eliminar**.
4. Elimine el servidor de configuración.
5. Desinstale manualmente la instancia de Mobility Service que se esté ejecutando en el servidor de destino maestro (este es o bien un servidor independiente o que se esté ejecutando en el servidor de configuración).
6. Desinstale todos los servidores de procesos adicionales.
7. Desinstale el servidor de configuración.
8. En el servidor de configuración, desinstale la instancia de MySQL que instaló Site Recovery.
9. En el registro del servidor de configuración, elimine la clave ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``.

## <a name="unregister-a-unconnected-configuration-server"></a>Anulación del registro de un servidor de configuración no conectado

Si replica máquinas virtuales de VMware o servidores físicos de Windows/Linux en Azure, puede anular el registro de un servidor de configuración no conectado desde un almacén de la forma siguiente:

1. Deshabilite la protección de la máquina. En **Protected Items** > **Replicated items** (Elementos protegidos > Elementos replicados), haga clic con el botón derecho en la máquina > **Eliminar**. Seleccione **Detener administración de la máquina**.
2. Retire cualquier proceso local o servidores de destino maestros adicionales. En **Site Recovery Infrastructure** > **For VMWare & Physical Machines** > **Configuration Servers** (Infraestructura de Site Recovery > Para VMWare y máquinas físicas > Servidores de configuración), haga clic con el botón derecho > **Eliminar**.
3. Elimine el servidor de configuración.
4. Desinstale manualmente la instancia de Mobility Service que se esté ejecutando en el servidor de destino maestro (este es o bien un servidor independiente o que se esté ejecutando en el servidor de configuración).
5. Desinstale todos los servidores de procesos adicionales.
6. Desinstale el servidor de configuración.
7. En el servidor de configuración, desinstale la instancia de MySQL que instaló Site Recovery.
8. En el registro del servidor de configuración, elimine la clave ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``.

## <a name="unregister-a-connected-vmm-server"></a>Anulación del registro de un servidor VMM conectado

Como procedimiento recomendado se sugiere que anule el registro del servidor VMM cuando está conectado a Azure. Esto garantiza que la configuración en los servidores VMM (y en otros servidores VMM con nubes emparejadas) se limpia correctamente. Debe quitar un servidor no conectado solo si hay un problema permanente con la conectividad. Si el servidor VMM no está conectado, tendrá que ejecutar manualmente un script para limpiar la configuración.

1. Detenga la replicación de máquinas virtuales en nubes en el servidor VMM que desea quitar.
2. Elimine cualquier asignación de red utilizada por las nubes en el servidor VMM que desea eliminar. En **Site Recovery Infrastructure** > **For System Center VMM** > **Network Mapping** (Infraestructura de Site Recovery > Para System Center VMM > Asignación de red), haga clic con el botón derecho en la asignación de red > **Eliminar**.
3. Disocie las directivas de replicación de las nubes en el servidor VMM que desea quitar.  En **Site Recovery Infrastructure** > **For System Center VMM** >  **Replication Policies**, (Infraestructura de Site Recovery > Para System Center VMM > Directivas de replicación) haga doble clic en la directiva asociada. Haga clic con el botón derecho en la nube > **Disassociate** (Desasociar).
4. Elimine el servidor VMM o el nodo activo de VMM. En **Site Recovery Infrastructure** > **For System Center VMM** > **VMM Servers** (Infraestructura de Site Recovery > Para System Center VMM > Servidores VMM) haga clic con el botón derecho en el servidor > **Eliminar**.
5. Desinstale el proveedor manualmente en el servidor VMM. Si tiene un clúster, quítelo de todos los nodos.
6. Si está replicando en Azure, quite manualmente el agente de Microsoft Recovery Services de los hosts de Hyper-V en las nubes eliminadas.



### <a name="unregister-an-unconnected-vmm-server"></a>Anulación del registro de un servidor VMM desconectado

1. Detenga la replicación de máquinas virtuales en nubes en el servidor VMM que desea quitar.
2. Elimine cualquier asignación de red utilizada por las nubes en el servidor VMM que desea eliminar. En **Site Recovery Infrastructure** > **For System Center VMM** > **Network Mapping** (Infraestructura de Site Recovery > Para System Center VMM > Asignación de red), haga clic con el botón derecho en la asignación de red > **Eliminar**.
3. Escriba el ID del servidor VMM.
4. Disocie las directivas de replicación de las nubes en el servidor VMM que desea quitar.  En **Site Recovery Infrastructure** > **For System Center VMM** >  **Replication Policies**, (Infraestructura de Site Recovery > Para System Center VMM > Directivas de replicación) haga doble clic en la directiva asociada. Haga clic con el botón derecho en la nube > **Disassociate** (Desasociar).
5. Elimine el servidor VMM o el nodo activo. En **Site Recovery Infrastructure** > **For System Center VMM** > **VMM Servers** (Infraestructura de Site Recovery > Para System Center VMM > Servidores VMM) haga clic con el botón derecho en el servidor > **Eliminar**.
6. Descargue y ejecute el [script de limpieza](http://aka.ms/asr-cleanup-script-vmm) en el servidor VMM. Abra PowerShell con la opción **Ejecutar como administrador** para cambiar la directiva de ejecución para el ámbito predeterminado (LocalMachine). En el script, especifique el ID del servidor VMM que desea quitar. El script quita el registro y la información de emparejamiento de nube del servidor.
5. Ejecute el script de limpieza en todos los demás servidores VMM que contengan nubes que estén emparejadas con nubes en el servidor VMM que desea quitar.
6. Ejecute el script de limpieza en tos los demás nodos de clúster VMM pasivos que tengan instalado el proveedor.
7. Desinstale el proveedor manualmente en el servidor VMM. Si tiene un clúster, quítelo de todos los nodos.
8. Si está replicando en Azure, puede quitar el agente de Microsoft Recovery Services de los hosts de Hyper-V en las nubes eliminadas.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Anulación del registro de un host Hyper-V en un sitio de Hyper-V

Los hosts Hyper-V que no están administrados por VMM se reúnen en un sitio de Hyper-V. Puede eliminar un host en un sitio Hyper-V de la forma siguiente:

1. Deshabilite la replicación para máquinas virtuales de Hyper-V que se encuentran en el host.
2. Disocie las directivas para el sitio de Hyper-V. En **Site Recovery Infrastructure** > **For Hyper-V Sites** >  **Replication Policies**, (Infraestructura de Site Recovery > Para sitios Hyper-V > Directivas de replicación) haga doble clic en la directiva asociada. Haga clic con el botón derecho en el sitio > **Disassociate** (Desasociar).
3. Elimine los hosts de Hyper-V. En **Site Recovery Infrastructure** > **For System Center VMM** > **Hyper-V Hosts** (Infraestructura de Site Recovery > Para System Center VMM > Hosts Hyper-V) haga clic con el botón derecho en el servidor > **Eliminar**.
4. Elimine el sitio de Hyper-V una vez que se hayan quitado todos los hosts del mismo. En **Site Recovery Infrastructure** > **For System Center VMM** > **Hyper-V Sites** (Infraestructura de Site Recovery > Para System Center VMM > Sitios Hyper-V) haga clic con el botón derecho en el servidor > **Eliminar**.
5. Ejecute el siguiente script en cada host de Hyper-V que se haya quitado. El script limpia la configuración en el servidor y anula su registro en el almacén.


        `` pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>Deshabilitación de la protección de una máquina virtual de VMware o un servidor físico

1. En **Protected Items** > **Replicated items** (Elementos protegidos > Elementos replicados), haga clic con el botón derecho en la máquina > **Eliminar**.
2. En **Remove Machine** (Quitar máquina), seleccione una de estas opciones:
    - **Disable protection for the machine (recommended)** [Deshabilitar protección de la máquina (recomendado)]. Utilice esta opción para detener la replicación de la máquina. La configuración de Site Recovery se limpiará automáticamente. Solo verá esta opción en las siguientes circunstancias:
        - **Cambio del tamaño del volumen de la máquina virtual**: cuando cambia el tamaño de un volumen, la máquina virtual entra en un estado crítico. Seleccionar esta opción deshabilita la protección mientras conserva los puntos de recuperación en Azure. Cuando se vuelve a habilitar la protección de la máquina, se transferirán a Azure los datos para el volumen cuyo tamaño ha cambiado.
        - **Ejecución reciente de una conmutación por error**: después de ejecutar una conmutación por error para probar el entorno, seleccione esta opción para empezar a proteger los equipos locales de nuevo. Esta opción deshabilita cada máquina virtual y por ello tiene que volver a habilitar la protección para las mismas. La deshabilitación de una máquina virtual con esta configuración, no afecta a la máquina virtual de réplica en Azure. No desinstale Mobility Service en la máquina.
    - **Stop managing the machine** (Detener administración de la máquina). Si selecciona esta opción, la máquina solo se quitará del almacén. La configuración de la protección local de la máquina no se verá afectada. Para quitar la configuración de la máquina y quitar la máquina de la suscripción de Azure, tendrá que borrar la configuración desinstalando Mobility Service.

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>Deshabilitación de la protección de una máquina virtual de Hyper-V en una nube VMM

1. En **Protected Items** > **Replicated items** (Elementos protegidos > Elementos replicados), haga clic con el botón derecho en la máquina > **Eliminar**.
2. En **Remove Machine** (Quitar máquina), seleccione una de estas opciones:

    - **Disable protection for the machine (recommended)** [Deshabilitar protección de la máquina (recomendado)]. Utilice esta opción para detener la replicación de la máquina. La configuración de Site Recovery se limpiará automáticamente.
    - **Stop managing the machine** (Detener administración de la máquina). Si selecciona esta opción, la máquina solo se quitará del almacén. La configuración de la protección local de la máquina no se verá afectada. Para quitar la configuración de la máquina y quitar la máquina de la suscripción de Azure, tendrá que limpiar la configuración manualmente siguiendo las instrucciones a continuación. Tenga en cuenta que si opta por eliminar la máquina virtual y sus discos duros, se quitarán de la ubicación de destino.

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>Limpieza de la configuración de protección: replicación a un sitio secundario de VMM

Si seleccionó **Stop managing the machine** (Detener administración de la máquina) y está replicando en un sitio secundario, ejecute este script en el servidor principal para limpiar la configuración de la máquina virtual principal. En la consola VMM, haga clic en el botón PowerShell para abrir la consola VMM PowerShell. Reemplace SQLVM1 por el nombre de la máquina virtual.

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. En el servidor VMM secundario, ejecute este script para limpiar la configuración de la máquina virtual secundaria:

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. En el servidor VMM secundario, actualice las máquinas virtuales en el servidor host de Hyper-V para que se vuelva a detectar la máquina virtual secundaria en la consola VMM.
4. Los pasos anteriores borrarán la configuración de replicación en el servidor VMM. Si desea detener la replicación de la máquina virtual, ejecute el siguiente script en las máquinas virtuales principales y secundarias. Reemplace SQLVM1 por el nombre de la máquina virtual.

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>Limpieza de la configuración de protección: replicación en Azure

1. Si seleccionó **Stop managing the machine** (Detener administración de la máquina) y está replicando en Azure, ejecute este script en el servidor VMM de origen, usando PowerShell desde la consola VMM.
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. Los pasos anteriores borrarán la configuración de replicación en el servidor VMM. Para detener la replicación de la máquina virtual que se ejecuta en el servidor host de Hyper-V, ejecute este script. Reemplace SQLVM1 con el nombre de la máquina virtual y host01.contoso.com con el nombre del servidor host de Hyper-V.

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>Deshabilitación de la protección de una máquina virtual de Hyper-V en un sitio de Hyper-V

Utilice este procedimiento si está replicando máquinas virtuales de Hyper-V a Azure sin un servidor VMM.

1. En **Protected Items** > **Replicated items** (Elementos protegidos > Elementos replicados), haga clic con el botón derecho en la máquina > **Eliminar**.
2. En **Remove Machine** (Quitar máquina), puede seleccionar una de estas opciones:

   - **Disable protection for the machine (recommended)** [Deshabilitar protección de la máquina (recomendado)]. Utilice esta opción para detener la replicación de la máquina. La configuración de Site Recovery se limpiará automáticamente.
   - **Stop managing the machine** (Detener administración de la máquina). Si selecciona esta opción, la máquina solo se quitará del almacén. La configuración de la protección local de la máquina no se verá afectada. Para quitar la configuración de la máquina y quitar la máquina virtual de la suscripción de Azure, tendrá que limpiar la configuración manualmente. Si opta por eliminar la máquina virtual y sus discos duros, se quitarán de la ubicación de destino.
3. Si selecciona **Stop managing the machine** (Detener administración de la máquina) utilice este script en el servidor host de Hyper-V de origen, para quitar la replicación de la máquina virtual. Reemplace SQLVM1 por el nombre de la máquina virtual.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)



<!--HONumber=Jan17_HO1-->


