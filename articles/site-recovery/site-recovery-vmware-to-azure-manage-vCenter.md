---
title: " Administración de un servidor VMware vCenter en Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe como agregar y administrar VMware vCenter en Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 091f0884417535427c52beee7bcdc5ed1dd83315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Administración de VMware vCenter Server en Azure Site Recovery
En este artículo se describen las diversas operaciones de Site Recovery que se pueden realizar en un servidor VMware vCenter.

## <a name="prerequisites"></a>Requisitos previos

**Compatibilidad con un host de VMware vCenter y VMware vSphere ESX** | **Detalles** |
|--- | --- |
|**Servidores de VMware locales** | Uno o más servidores de VMware vSphere, con 6.0, 5.5 o 5.1 y las últimas actualizaciones. Los servidores se deberían encontrar en la misma red que el servidor de configuración (o en un servidor de procesos independiente).<br/><br/> Se recomienda un servidor vCenter para administrar hosts, con 6.0 o 5.5 con las últimas actualizaciones. Solo se admiten las características que estén disponibles en 5.5 cuando se implementa la versión 6.0.|

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparación de una cuenta de detección automática
Site Recovery necesita acceso a VMware para que el servidor de procesos detecte automáticamente máquinas virtuales, así como para la conmutación por error y por recuperación de máquinas virtuales.

* **Migración**: si solo desea quiere máquinas virtuales de VMware a Azure, sin conmutarlas nunca por recuperación, puede usar una cuenta de VMware con un rol de solo lectura. Este rol puede ejecutar la conmutación por error, pero no puede apagar las máquinas de origen protegidas. Esto no es necesario para la migración.
* **Replicación/recuperación**: si quiere implementar la replicación completa (replicación, conmutación por error, conmutación por recuperación), la cuenta debe poder ejecutar operaciones como la creación y eliminación de discos, el encendido de máquinas virtuales, etc.
* **Detección automática**: se requiere al menos una cuenta de solo lectura.


|**Tareas** | **Cuenta/rol necesarios** | **Permisos** | **Detalles**|
|--- | --- | --- | ---|
|**El servidor de procesos detecta automáticamente máquinas virtuales de VMware** | Necesita al menos un usuario de solo lectura. | Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).|
|**Conmutación por error** | Necesita al menos un usuario de solo lectura. | Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).<br/><br/> Es útil para la migración, pero no para la replicación completa, la conmutación por error o la conmutación por recuperación.|
|**Conmutación por error y conmutación por recuperación** | Se recomienda crear un rol (AzureSiteRecoveryRole) con los permisos necesarios y, después, asignarlo a un grupo o usuario de VMware. | Objeto de centro de datos –> Propagar a objeto secundario, rol=AzureSiteRecoveryRole<br/><br/> Almacén de datos -> Asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual<br/><br/> Red -> Asignación de red<br/><br/> Recursos-> Asignar máquina virtual al grupo de recursos, migrar apagado de máquina virtual, migrar máquinas virtuales encendidas<br/><br/> Tareas -> Crear tarea, actualizar tarea<br/><br/> Máquina virtual -> Configuración<br/><br/> Máquina virtual -> Interactuar -> Responder a pregunta, conexión de dispositivos, configurar soporte de CD, Configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware<br/><br/> Máquina virtual -> Inventario -> Crear, registrar, anular registro<br/><br/> Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual<br/><br/> Máquina virtual -> Instantáneas -> Quitar instantáneas | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>Creación de una cuenta para conectarse a un host de VMware vCenter Server/VMware vSphere EXSi
1. Inicie sesión en el servidor de configuración y ejecute cspsconfigtool.exe mediante el acceso directo colocado en el Escritorio.
2. En **Administrar cuenta**, haga clic en la pestaña **Agregar cuenta**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Proporcione los detalles de la cuenta y haga clic en Aceptar para agregarla. La cuenta debe tener los privilegios enumerados en la sección [Preparación de una cuenta de detección automática](#prepare-an-account-for-automatic-discovery).

  >[!NOTE]
  En unos 15 minutos la información de la cuenta se sincronizará con el servicio Site Recovery.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>Asociación de un host de VMware vCenter/VMware vSphere ESX (agregar vCenter)
* En Azure Portal, vaya a *YourRecoveryServicesVault* > **Infraestructura de recuperación del sitio** > **Servidores de configuración** > *ConfigurationServer*
* En la página de detalles del servidor de configuración, haga clic en el botón +vCenter.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>Modificación de las credenciales usadas para conectarse al servidor vCenter/host de vSphere ESXi

1. Inicie sesión en el servidor de configuración y ejecute cspsconfigtool.exe.
2. En **Administrar cuenta**, haga clic en la pestaña **Agregar cuenta**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Proporcione los detalles de la nueva cuenta y haga clic en Aceptar para agregar la cuenta. La cuenta debe tener los privilegios enumerados en la sección [Preparación de una cuenta de detección automática](#prepare-an-account-for-automatic-discovery).
4. En Azure Portal, vaya a *YourRecoveryServicesVault* > **Infraestructura de recuperación del sitio** > **Servidores de configuración** > *ConfigurationServer*
5. En la página de detalles del servidor de configuración, haga clic en el botón **Actualizar el servidor**.
6. Cuando finalice el trabajo de actualización del servidor, seleccione el servidor vCenter para abrir la página de resumen de vCenter.
7. Seleccione la cuenta recién agregada en el campo **vCenter server/vSphere host account** (Cuenta de servidor vCenter/host de vSphere) y haga clic en el botón **Guardar**.

  ![modify-account](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Eliminación de un servidor vCenter en Azure Site Recovery
1. En Azure Portal, vaya a *YourRecoveryServicesVault* > **Infraestructura de recuperación del sitio** > **Servidores de configuración** > *ConfigurationServer*
2. En la página de detalles del servidor de configuración, seleccione el servidor de vCenter para abrir la página de resumen de vCenter.
3. Haga clic en el botón **Eliminar** para eliminar el servidor vCenter.

  ![delete-account](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Si tiene que modificar los detalles de dirección IP/FQDN de vCenter o el puerto, debe eliminar el servidor vCenter y agregarlo de nuevo.
