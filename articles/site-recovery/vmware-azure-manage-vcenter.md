---
title: " Administración de servidores VMware vCenter en Azure Site Recovery | Microsoft Docs"
description: En este artículo se describe como agregar y administrar VMware vCenter en Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: be415340da09043eccd361b0168bb304d8904bef
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852309"
---
# <a name="manage-vmware-vcenter-servers"></a>Administración de servidores VMware vCenter 

En este artículo se describen las diversas operaciones de Site Recovery que se pueden realizar en un servidor VMware vCenter. Compruebe los [requisitos previos](vmware-physical-azure-support-matrix.md#replicated-machines) antes de empezar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configuración de una cuenta de detección automática

Site Recovery necesita acceso a VMware para que el servidor de procesos detecte automáticamente máquinas virtuales, así como para la conmutación por error y por recuperación de máquinas virtuales. Cree una cuenta de acceso de la manera siguiente:

1. Inicie sesión en la máquina del servidor de configuración.
2. Abra el archivo cspsconfigtool.exe mediante el acceso directo del escritorio.
3. En **Administrar cuenta**, haga clic en la pestaña **Agregar cuenta**.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Proporcione los detalles de la cuenta y haga clic en **Aceptar** para agregarla.  La cuenta debe tener los privilegios que se resumen en la tabla siguiente. 

En unos 15 minutos la información de la cuenta se sincronizará con el servicio Site Recovery.

### <a name="account-permissions"></a>Permisos de la cuenta

|**Task** | **Cuenta** | **permisos** | **Detalles**|
|--- | --- | --- | ---|
|**Detección y migración automáticas (sin conmutación por recuperación)** | Necesita al menos un usuario de solo lectura. | Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).|
|**Replicación y conmutación por error** | Necesita al menos un usuario de solo lectura.| Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).<br/><br/> Es útil para la migración, pero no para la replicación completa, la conmutación por error o la conmutación por recuperación.|
|**Replicación, conmutación por error y conmutación por recuperación** | Se recomienda crear un rol (AzureSiteRecoveryRole) con los permisos necesarios y, después, asignarlo a un grupo o usuario de VMware. | Objeto de centro de datos –> Propagar a objeto secundario, rol=AzureSiteRecoveryRole<br/><br/> Almacén de datos -> Asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual<br/><br/> Red -> Asignación de red<br/><br/> Recursos-> Asignar máquina virtual al grupo de recursos, migrar apagado de máquina virtual, migrar máquinas virtuales encendidas<br/><br/> Tareas -> Crear tarea, actualizar tarea<br/><br/> Máquina virtual -> Configuración<br/><br/> Máquina virtual -> Interactuar -> Responder a pregunta, conexión de dispositivos, configurar soporte de CD, Configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware<br/><br/> Máquina virtual -> Inventario -> Crear, registrar, anular registro<br/><br/> Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual<br/><br/> Máquina virtual -> Instantáneas -> Quitar instantáneas | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).|


## <a name="add-vmware-server-to-the-vault"></a>Adición de un servidor VMware al almacén

1. En Azure Portal, abra el almacén > **Infraestructura de Site Recovery** > **Servidores de configuración** y abra el servidor de configuración.
2. En la página **Detalles**, haga clic en **+vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modificación de credenciales

Modificación de las credenciales usadas para conectarse al servidor vCenter o host de ESXi de la siguiente manera:

1. Inicie sesión en el servidor de configuración y ejecute cspsconfigtool.exe desde el escritorio.
2. En **Administrar cuenta**, haga clic en la pestaña **Agregar cuenta**.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Proporcione los detalles de la nueva cuenta y haga clic en **Aceptar** para agregarla. La cuenta debe tener los privilegios enumerados [anteriormente](#account-permissions).
4. En Azure Portal, abra el almacén > **Infraestructura de Site Recovery** > **Servidores de configuración** y abra el servidor de configuración.
5. En la página **Detalles**, haga clic en **Actualizar servidor**.
6. Cuando finalice el trabajo de actualización del servidor, seleccione la instancia de vCenter Server para abrir la página **Resumen** de vCenter.
7. Seleccione la cuenta recién agregada en el campo **vCenter server/vSphere host account** (Cuenta de servidor vCenter/host de vSphere) y haga clic en **Guardar**.

    ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Eliminación de un servidor de vCenter

1. En Azure Portal, abra el almacén > **Infraestructura de Site Recovery** > **Servidores de configuración** y abra el servidor de configuración.
2. En la página **Detalles**, seleccione el servidor de vCenter.
3. Haga clic en el botón **Eliminar**.

  ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Si tiene que modificar los detalles de dirección IP, FQDN o el puerto de vCenter, debe eliminar el servidor de vCenter y agregarlo de nuevo.
