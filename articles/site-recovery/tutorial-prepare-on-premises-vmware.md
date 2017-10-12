---
title: "Preparar servidores de VMware locales para la recuperación ante desastres de máquinas virtuales de VMware en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo preparar servidores de VMware locales para la recuperación ante desastres en Azure mediante el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 90a4582c-6436-4a54-a8f8-1fee806b8af7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 2e59a78f2c348b581155484d77e272a050da1f1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Preparar servidores de VMware locales para la recuperación ante desastres en Azure

En este tutorial se muestra cómo preparar la infraestructura de VMware local para replicar máquinas virtuales de VMware en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Preparar una cuenta en el servidor vCenter o en el host ESXi de vSphere para automatizar la detección de máquinas virtuales
> * Preparar una cuenta para la instalación automática de Mobility Service en máquinas virtuales de VMware
> * Revisar los requisitos del servidor de VMware
> * Revisar los requisitos de las máquinas virtuales de VMware

En esta serie de tutoriales, se muestra cómo realizar copias de seguridad de una sola máquina virtual mediante Azure Site Recovery. Si va a proteger varias máquinas virtuales de VMware, debe descargar la [herramienta Deployment Planner](https://aka.ms/asr-deployment-planner) para la replicación de VMware. Con esta herramienta puede recopilar información sobre la compatibilidad de las máquinas virtuales, los discos por máquina virtual y la actividad de datos por disco. Esta herramienta también se encarga de los requisitos de ancho de banda de red y de la infraestructura de Azure necesaria para una replicación y conmutación por error de prueba correctas. [Obtenga más información](site-recovery-deployment-planner.md) sobre la ejecución de la herramienta.

Este es el segundo tutorial de la serie. Asegúrese de que [ha configurado los componentes de Azure](tutorial-prepare-azure.md) tal como se describe en el tutorial anterior.

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparación de una cuenta de detección automática

Site Recovery necesita acceso a los servidores de VMware para:

- Detectar automáticamente las máquinas virtuales. Se requiere al menos una cuenta de solo lectura.
- Coordinar la replicación, la conmutación por error y la conmutación por recuperación. Necesita una cuenta que pueda ejecutar operaciones como la creación y la eliminación de discos, así como la activación de máquinas virtuales.

Cree la cuenta como se indica a continuación:

1. Para usar una cuenta dedicada, cree un rol en el nivel de vCenter. Asigne al rol un nombre como **Azure_Site_Recovery**.
2. Asigne al rol los permisos que se resumen en la tabla siguiente.
3. Cree un usuario en el servidor vCenter o el host de vSphere. Asigne el rol al usuario.

### <a name="vmware-account-permissions"></a>Permisos de cuenta de VMware

**Task** | **Rol o permisos** | **Detalles**
--- | --- | ---
**Detección de máquina virtual** | Al menos un usuario de solo lectura<br/><br/> Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).
**Replicación completa, conmutación por error, conmutación por recuperación** |  Cree un rol (Azure_Site_Recovery) con los permisos necesarios y, después, asígnelo a un grupo o usuario de VMware.<br/><br/> Objeto de centro de datos –> Propagar a objeto secundario, rol = Azure_Site_Recovery<br/><br/> Almacén de datos -> Asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual<br/><br/> Red -> Asignación de red<br/><br/> Recursos-> Asignar máquina virtual al grupo de recursos, migrar apagado de máquina virtual, migrar máquinas virtuales encendidas<br/><br/> Tareas -> Crear tarea, actualizar tarea<br/><br/> Máquina virtual -> Configuración<br/><br/> Máquina virtual -> Interactuar -> Responder a pregunta, conexión de dispositivos, configurar soporte de CD, Configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware<br/><br/> Máquina virtual -> Inventario -> Crear, registrar, anular registro<br/><br/> Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual<br/><br/> Máquina virtual -> Instantáneas -> Quitar instantáneas | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Preparación de una cuenta para la instalación de Mobility Service

Mobility Service debe instalarse en la máquina que quiera replicar. Site Recovery instala este servicio automáticamente cuando se habilita la replicación para la máquina virtual. Para la instalación automática, debe preparar una cuenta que Site Recovery vaya a usar para obtener acceso a la máquina virtual. Deberá especificar esta cuenta al configurar la recuperación ante desastres en la consola de Azure.

1. Prepare un dominio o cuenta local con permisos para instalar en la máquina virtual.
2. Para instalar en máquinas virtuales Windows, si no usa una cuenta de dominio, deshabilite el control Acceso de usuarios remotos en la máquina local.
   - En el Registro, en **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, agregue la entrada DWORD **LocalAccountTokenFilterPolicy** con un valor de 1.
3. Para instalar en máquinas virtuales Linux, prepare una cuenta raíz en el servidor Linux de origen.


## <a name="check-vmware-server-requirements"></a>Comprobación de los requisitos del servidor de VMware

Asegúrese de que los servidores VMware cumplen los requisitos siguientes.

**Componente** | **Requisito**
--- | ---
**Servidor vCenter** | vCenter 6.5, 6.0 o 5.5
**Host de vSphere** | vSphere 6.5, 6.0 y 5.5

## <a name="check-vmware-vm-requirements"></a>Comprobación de los requisitos de las máquinas virtuales de VMware

Asegúrese de que la máquina virtual cumple los requisitos de Azure resumidos en la tabla siguiente.

**Requisito de la máquina virtual** | **Detalles**
--- | ---
**Tamaño del disco del sistema operativo** | Hasta 2048 GB
**Número de discos del sistema operativo** | 1
**Número de discos de datos** | 64 o menos
**Tamaño de VHD del disco de datos** | Hasta 4095 GB
**Adaptadores de red** | Se admiten varios adaptadores
**VHD compartido** | No compatible
**Disco FC** | No compatible
**Formato de disco duro** | VHD o VHDX<br/><br/> Aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en el entorno local, las máquinas virtuales siguen usando el formato VHDX.
**BitLocker** | No compatible. Deshabilítelo antes de habilitar la replicación para una máquina virtual.
**Nombre de la máquina virtual** | Entre 1 y 63 caracteres.<br/><br/> Restringido a letras, números y guiones. El nombre de la VM debe empezar y terminar con una letra o un número.
**Tipo de máquina virtual** | Generación 1: Linux o Windows<br/><br/>Generación 2: solo Windows

La máquina virtual también debe ejecutar un sistema operativo admitido. Vea la [matriz de compatibilidad de Site Recovery](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para obtener una lista completa de las versiones compatibles.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Durante una conmutación por error, es posible que quiera conectarse a las máquinas virtuales replicadas en Azure desde la red local.

Para conectarse a máquinas virtuales Windows mediante RDP después de la conmutación por error, haga lo siguiente:

1. Para obtener acceso a través de Internet, habilite RDP en la máquina virtual local antes de la conmutación por error. Asegúrese de que se hayan agregado las reglas de TCP y UDP para el perfil **Público**, y que RDP se permite en **Firewall de Windows** > **Aplicaciones permitidas** para todos los perfiles.
2. Para obtener acceso a través de VPN de sitio a sitio, habilite RDP en la máquina local. RDP debe permitirse en **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **dominio y privadas**.
   Compruebe que la directiva SAN del sistema operativo está establecida en **OnlineAll**. [Más información](https://support.microsoft.com/kb/3031135). No debe haber actualizaciones de Windows pendientes en la máquina virtual cuando se desencadene una conmutación por error. Si hay, no podrá iniciar sesión en la máquina virtual hasta que se complete la actualización.
3. En la máquina virtual de Microsoft Azure después de la conmutación por error, compruebe los **Diagnósticos de arranque** para ver una captura de pantalla de la máquina virtual. Si no puede conectarse, compruebe que se está ejecutando la máquina virtual y revise estas [sugerencias de solución de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Para conectarse a máquinas virtuales Linux mediante SSH después de la conmutación por error, haga lo siguiente:

1. En el equipo local antes de la conmutación por error, compruebe que el servicio de Secure Shell está configurado para iniciarse automáticamente en el arranque del sistema. Compruebe que las reglas de firewall permiten una conexión SSH.

2. En la máquina virtual de Azure después de la conmutación por error, permita las conexiones entrantes al puerto SSH para las reglas del grupo de seguridad de red de la máquina virtual conmutada por error y para la subred de Azure a la que esta se conecta.
   [Agregue una dirección IP pública](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) para la máquina virtual. Puede comprobar los **Diagnósticos de arranque** para ver una captura de pantalla de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configurar la recuperación ante desastres en Azure para máquinas virtuales de VMware](tutorial-vmware-to-azure.md)
