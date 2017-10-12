---
title: "Preparación de los recursos de VMware locales para la replicación en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para replicar cargas de trabajo que se ejecutan en máquinas virtuales de VMware en Azure Storage"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>Paso 6: Preparación de la replicación de VMware local a Azure

Siga las instrucciones de este artículo para preparar los servidores de VMware locales para interactuar con Azure Site Recovery y prepare las máquinas virtuales de VMWare para la instalación del servicio Mobility. El agente del servicio Mobility debe instalarse en todas las máquinas virtuales locales que desee replicar en Azure.

## <a name="prepare-for-automatic-discovery"></a>Preparación para la detección automática

Site Recovery detecta automáticamente las máquinas virtuales en ejecución en los hosts de vSphere ESXi (con o sin un servidor vCenter). Para la detección automática, Site Recovery necesita una cuenta para acceder a los hosts y servidores:

1. Para usar una cuenta especializada, cree un rol (en el nivel de vCenter, con los permisos que se indican en la siguiente tabla). Asígnele un nombre como **Azure_Site_Recovery**.
2. Después, cree un usuario en el servidor vCenter o en el host de vSphere y asigne el rol al usuario. Especifique esta cuenta de usuario durante la implementación de Site Recovery.


### <a name="vmware-account-permissions"></a>Permisos de cuenta de VMware

Site Recovery necesita acceso a VMware para que el servidor de procesos detecte automáticamente máquinas virtuales, así como para la conmutación por error y por recuperación de máquinas virtuales.

- **Migración**: si solo desea migrar máquinas virtuales de VMware a Azure, sin conmutarlas nunca por recuperación, puede usar una cuenta de VMware con un rol de solo lectura. Este rol puede ejecutar la conmutación por error, pero no puede apagar las máquinas de origen protegidas. Esto no es necesario para la migración.
- **Replicación/recuperación**: si desea implementar la replicación completa (replicación, conmutación por error, conmutación por recuperación), la cuenta debe poder ejecutar operaciones como la creación y eliminación de discos, el encendido de máquinas virtuales, etc.
- **Detección automática**: se requiere al menos una cuenta de solo lectura.


**Task** | **Cuenta/rol necesarios** | **Permisos** | **Detalles**
--- | --- | --- | ---
**El servidor de procesos detecta automáticamente máquinas virtuales de VMware** | Necesita al menos un usuario de solo lectura. | Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).
**Conmutación por error** | Necesita al menos un usuario de solo lectura. | Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).<br/><br/> Es útil para la migración, pero no para la replicación completa, la conmutación por error o la conmutación por recuperación.
**Conmutación por error y conmutación por recuperación** | Se recomienda crear un rol (Azure_Site_Recovery) con los permisos necesarios y, después, asignarlo a un grupo o usuario de VMware. | Objeto de centro de datos –> Propagar a objeto secundario, rol = Azure_Site_Recovery<br/><br/> Almacén de datos -> Asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual<br/><br/> Red -> Asignación de red<br/><br/> Recursos-> Asignar máquina virtual al grupo de recursos, migrar apagado de máquina virtual, migrar máquinas virtuales encendidas<br/><br/> Tareas -> Crear tarea, actualizar tarea<br/><br/> Máquina virtual -> Configuración<br/><br/> Máquina virtual -> Interactuar -> Responder a pregunta, conexión de dispositivos, configurar soporte de CD, Configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware<br/><br/> Máquina virtual -> Inventario -> Crear, registrar, anular registro<br/><br/> Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual<br/><br/> Máquina virtual -> Instantáneas -> Quitar instantáneas | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>Preparación para la instalación de inserción del servicio Mobility

El servicio Mobility debe estar instalado en todas las máquinas virtuales que quiera replicar. Hay varias maneras de instalar el servicio, por ejemplo, la instalación manual, la instalación de inserción desde el servidor de procesos de Site Recovery y la instalación mediante métodos como System Center Configuration Manager.

Si desea utilizar la instalación de inserción, debe preparar una cuenta que Site Recovery pueda utilizar para acceder a la máquina virtual.

- Puede usar una cuenta local o de dominio.
- Para Windows, si no utiliza una cuenta de dominio, debe deshabilitar Access Control de usuario remoto en la máquina local. Para hacerlo, en el Registro, en **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, agregue la entrada DWORD **LocalAccountTokenFilterPolicy** con un valor de 1.
- Si desea agregar la entrada del Registro de Windows desde una CLI, escriba:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para Linux, la cuenta debe ser una raíz en el servidor Linux de origen.



## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 7: Creación de un almacén](vmware-walkthrough-create-vault.md)
