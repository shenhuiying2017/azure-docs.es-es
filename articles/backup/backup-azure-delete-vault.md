---
title: " Eliminación de un almacén de Recovery Services en Azure | Microsoft Docs "
description: "En este artículo se explica cómo eliminar un almacén de Recovery Services. El artículo incluye pasos para solucionar los problemas que se producen cuando se intenta eliminar un almacén, pero no puede."
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 4f4a92159b01b197984130c15195419e1b166fd3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-recovery-services-vault"></a>Eliminación de un almacén de Recovery Services
En este artículo se explica cómo eliminar un almacén de Recovery Services en Azure Portal. Si tenía almacenes de Backup, estos se han convertido en almacenes de Recovery Services.   

Eliminar un almacén de Recovery Services es un proceso en un paso, *siempre y cuando el almacén no contenga ningún recurso*. Antes de poder eliminar un almacén de Recovery Services, debe quitar o eliminar todos los recursos del almacén. Si intenta eliminar un almacén que contiene recursos, obtendrá un error similar al de la siguiente imagen:

![Error de eliminación del almacén](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Hasta que haya borrado los recursos del almacén, al hacer clic en **Reintentar** se produce el mismo error. Si está atascado en este mensaje de error, haga clic en **Cancelar** y siga estos pasos para eliminar los recursos del almacén.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>Eliminación de elementos de un almacén que protege una máquina virtual
Si ya tiene abierto el almacén de Recovery Services, vaya al segundo paso.

1. Abra Azure Portal y, desde el panel, abra el almacén que desea eliminar.

   Si el almacén de Recovery Services no está anclado al panel, en el menú Concentrador, haga clic en **Más servicios** y, en la lista de recursos, escriba **Recovery Services**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de Recovery Services**.

   ![Creación del almacén de Recovery Services, paso 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Se muestra la lista de almacenes de Recovery Services. En la lista, seleccione el almacén que desea eliminar.

   ![elegir el almacén en la lista](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. En la vista de almacén, examine el panel **Essentials** . Para eliminar un almacén, no puede haber ningún elemento protegido. Si en **Elementos de copia de seguridad** o **Servidores de administración de Backup** no aparece un cero, deberá quitar esos elementos. No se puede eliminar el almacén de credenciales si este contiene datos.

    ![Consulta de los elementos protegidos en el panel Essentials](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Las máquinas virtuales y los archivos o carpetas se consideran elementos de Backup y se enumeran en el área **Elementos de copia de seguridad** del panel Essentials. Se muestra un servidor DPM en el área **Servidores de administración de copias de seguridad** del panel Essentials. **Elementos replicados** pertenecen al servicio Azure Site Recovery.
3. Para empezar a quitar los elementos protegidos del almacén, búsquelos en el almacén. En el panel del almacén, haga clic en **Configuración**, **Elementos de copia de seguridad** para abrir ese menú.

    ![elegir el almacén en la lista](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    El menú **Elementos de copia de seguridad** tiene listas independientes, en función del tipo de elemento: Azure Virtual Machines o carpetas y archivos (consulte la imagen). La lista Tipo de elemento predeterminada que se muestra es Azure Virtual Machines. Para ver la lista de elementos Archivos y carpetas del almacén, seleccione **Archivos y carpetas** en el menú desplegable.
4. Antes de poder eliminar un elemento del almacén que protege una máquina virtual, debe detener el trabajo de copia de seguridad del elemento y eliminar los datos del punto de recuperación. Para cada elemento del almacén, siga estos pasos:

    a. En el menú **Elementos de copia de seguridad**, haga clic con el botón derecho en el elemento y, en el menú contextual, seleccione **Detener copia de seguridad**.

    ![detener el trabajo de copia de seguridad](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Se abre el menú Detener copia de seguridad.

    b. En el menú **Detener copia de seguridad**, en **Elija una opción**, seleccione **Eliminar datos de copia de seguridad** > escriba el nombre del elemento > y haga clic en **Detener copia de seguridad**.

    Escriba el nombre del elemento para verificar que desea eliminarlo. El botón **Detener copia de seguridad** se activa después de verificar el elemento. Si no ve el cuadro de diálogo para escribir el nombre del elemento de copia de seguridad, seleccione la opción **Retener datos de copia de seguridad**.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Opcionalmente, puede proporcionar el motivo por el qué va a eliminar los datos y agregar comentarios. Tras hacer clic en **Detener copia de seguridad**, permita que el trabajo de eliminación se complete antes de intentar eliminar el almacén. Para comprobar que el trabajo se ha completado, compruebe los mensajes de Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Una vez finalizado el trabajo, el servicio envía un mensaje: el proceso de copia de seguridad se ha detenido y se han eliminado los datos de copia de seguridad.

    c. Después de eliminar un elemento de la lista, en el menú **Elementos de copia de seguridad**, haga clic en **Actualizar** para ver los restantes elementos del almacén.

      ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/empty-items-list.png)

      Cuando no haya ningún elemento en la lista, desplácese al panel **Essentials** en el menú del almacén de Recovery Services. No debería haber **elementos de copia de seguridad**, **servidores de administración de copias de seguridad** ni **elementos replicados** en la lista. Si todavía aparecen elementos en el almacén, vuelva al paso 3 y elija otra lista de tipos de elemento.  
5. Cuando no hay ningún elemento más en la barra de herramientas de almacén, haga clic en **Eliminar**.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-vault.png)
6. Para verificar que desea eliminar el almacén, haga clic en **Sí**.

    Se elimina el almacén y el portal vuelve al menú **Nuevo servicio** .

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>¿Qué ocurre si he detenido el proceso de copia de seguridad pero he retenido los datos?
Si ha detenido el proceso de copia de seguridad, pero accidentalmente *retuvo* los datos, debe eliminar los datos de copia de seguridad antes de poder eliminar el almacén. Para eliminar los datos de copia de seguridad:

1. En el menú **Elementos de copia de seguridad**, haga clic con el botón derecho en el elemento y, en el menú contextual, haga clic en **Eliminar datos de copia de seguridad**.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Se abre el menú **Eliminar datos de copia de seguridad**.
2. En el menú **Eliminar datos de copia de seguridad**, escriba el nombre del elemento y haga clic en **Eliminar**.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Una vez eliminados los datos, vaya al paso 4c y continúe con el proceso.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Eliminación de un almacén usado para proteger un servidor DPM
Antes de poder eliminar un almacén usado para proteger un servidor DPM, debe borrar los puntos de recuperación que se han creado y, después, anular el registro del servidor del almacén.

Para eliminar los datos asociados a un grupo de protección:

1. En la Consola de administrador DPM, haga clic en **Protección**, seleccione un grupo de protección, seleccione el miembro de grupo de protección y, en la cinta de herramientas, haga clic en **Quitar**.

  Seleccione el miembro de grupo de protección para activar el botón **Quitar** en la cinta de herramientas. En el ejemplo, el miembro es **dummyvm9**. Para seleccionar varios miembros del grupo de protección, mantenga presionada la tecla Ctrl mientras hace clic en cada miembro.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    Se abre el cuadro de diálogo **Detener la protección** .
2. En el cuadro de diálogo **Detener protección**, seleccione **Eliminar datos protegidos** y haga clic en **Detener protección**.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Para eliminar un almacén, debe borrar o eliminar el almacén de datos protegidos. Si hay muchos puntos de recuperación y datos en el grupo de protección, se puede tardar varios minutos en eliminar los datos. El cuadro de diálogo **Detener la protección** muestra cuándo se ha completado el trabajo.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Repita este proceso para todos los miembros de todos los grupos de protección.

    Elimine todos los datos protegidos y grupos de protección.
4. Después de eliminar todos los miembros del grupo de protección, cambie a Azure Portal. Abra el panel del almacén y asegúrese de que **Elementos de copia de seguridad**, **Servidores de administración de copias de seguridad** y **Elementos replicados están vacíos**. En la barra de herramientas del almacén, haga clic en **Eliminar**.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-vault.png)

    Si hay servidores de administración de Backup registrados en el almacén, no puede eliminar el almacén, aunque no contenga ningún dato. Si ha eliminado los servidores de administración de Backup asociados al almacén, pero todavía aparecen servidores en el panel **Essentials**, vea [Búsqueda de servidores de administración de Backup registrados en el almacén](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Para verificar que desea eliminar el almacén, haga clic en **Sí**.

    Se elimina el almacén y el portal vuelve al menú **Nuevo servicio** .

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Eliminación de un almacén usado para proteger un servidor de producción
Antes de poder eliminar un almacén usado para proteger un servidor de producción, debe eliminar o anular el registro del servidor del almacén.

Para eliminar el servidor de producción asociado con el almacén:

1. En Azure Portal, abra el panel del almacén y haga clic en **Configuración** > **Infraestructura de copia de seguridad** > **Servidores de producción**.

    ![abrir el menú Servidores de producción](./media/backup-azure-delete-vault/delete-production-server.png)

    Se abre el menú **Servidores de producción**, donde se muestran todos los servidores de producción del almacén.

    ![lista de servidores de producción](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. En el menú **Servidores de producción**, haga clic con el botón derecho en el servidor y haga clic en **Eliminar**.

    ![eliminar servidor de producción ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Se abre el menú **Eliminar**.

    ![eliminar servidor de producción ](./media/backup-azure-delete-vault/delete-blade.png)
3. En el menú **Eliminar**, confirme el nombre del servidor y haga clic en **Eliminar**. Debe escribir correctamente el nombre del servidor para activar el botón **Eliminar**.

    Una vez que se ha eliminado el almacén, recibirá un mensaje que indica que se ha eliminado el almacén. Después de eliminar todos los servidores del almacén, desplácese al panel Essentials en el panel del almacén.
4. En el panel del almacén, asegúrese de que **Elementos de copia de seguridad**, **Servidores de administración de copias de seguridad** y **Elementos replicados están vacíos**. En la barra de herramientas del almacén, haga clic en **Eliminar**.
5. Para verificar que desea eliminar el almacén, haga clic en **Sí**.

    Se elimina el almacén y el portal vuelve al menú **Nuevo servicio** .

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Búsqueda de servidores de administración de Backup registrados en el almacén
Si tiene varios servidores registrados en un almacén, puede ser difícil recordarlos. Para ver los servidores registrados en el almacén y eliminarlos:

1. Abra el panel del almacén.
2. En el panel **Essentials**, haga clic en **Configuración** para abrir el menú.

    ![abrir menú de configuración](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. En el menú **Configuración**, haga clic en **Infraestructura de copia de seguridad**.
4. En el menú **Infraestructura de copia de seguridad**, haga clic en **Servidores de administración de copias de seguridad**. Se abre el menú Servidores de administración de copias de seguridad.

    ![lista Servidores de administración de copias de seguridad](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Para eliminar un servidor de la lista, haga clic con el botón derecho en el nombre del servidor y haga clic en **Eliminar**.
    Se abre el menú **Eliminar**.
6. En el menú **Eliminar**, proporcione el nombre del servidor. Si es un nombre largo, puede copiarlo y pegarlo de la lista Servidores de administración de copias de seguridad. Después, haga clic en **Eliminar**.  
