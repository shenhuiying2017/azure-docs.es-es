---
title: "Eliminación de un almacén de Azure Backup | Microsoft Docs"
description: 'How to delete an Azure Backup vault. Troubleshooting why you can''t delete a backup vault. '
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: cfreeman
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2016
ms.author: markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 704c21aef6ed8b476bbd14f05bce89dc5b708c21


---
# <a name="delete-an-azure-backup-vault"></a>Eliminación de un almacén de Azure Backup
El servicio Azure Backup tiene dos tipos de almacenes: el almacén de Backup y el almacén de Recovery Services. El almacén de Backup fue el primero. A continuación, surgió el almacén de Recovery Services para admitir las implementaciones expandidas de Resource Manager. Debido a las funcionalidades expandidas y las dependencias de información que deben almacenarse en el almacén, eliminar un almacén de Recovery Services puede parecer más difícil de lo es.

| **Tipo de implementación** | **Portal** | **Nombre del almacén** |
| --- | --- | --- |
| Clásico |Clásico |Almacén de copia de seguridad |
| Resource Manager |Las tablas de Azure |Almacén de Servicios de recuperación |

> [!NOTE]
> Los almacenes de copia de seguridad no pueden proteger soluciones implementadas con Resource Manager. Sin embargo, puede usar un almacén de Recovery Services para proteger los servidores y las máquinas virtuales implementados con el modelo clásico.  
> 
> 

En este artículo, utilizaremos el término almacén para referirnos de forma genérica al almacén de Backup o al almacén de Recovery Services. Usamos el nombre formal, el almacén de Backup o el almacén de Recovery Services cuando es necesario distinguir entre los almacenes.

## <a name="deleting-a-recovery-services-vault"></a>Eliminación de un almacén de Recovery Services
Eliminar un almacén de Recovery Services es un proceso en un paso, *siempre y cuando el almacén no contenga ningún recurso*. Antes de poder eliminar un almacén de Recovery Services, debe quitar o eliminar todos los recursos del almacén. Si intenta eliminar un almacén que contiene recursos, obtendrá un error similar a la siguiente imagen:

![Error de eliminación del almacén](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Hasta que haya borrado los recursos del almacén, al hacer clic en **Reintentar** se produce el mismo error. Si está atascado en este mensaje de error, haga clic en **Cancelar** y siga estos pasos para eliminar los recursos del almacén de Recovery Services.

### <a name="removing-the-items-from-a-vault-protecting-a-vm"></a>Eliminación de elementos de un almacén que protege una máquina virtual
Si ya tiene abierto el almacén de Recovery Services, vaya al segundo paso.

1. Abra Azure Portal y, desde el panel, abra el almacén que desea eliminar.
   
   Si el almacén de Recovery Services no está anclado al panel, en el menú Concentrador, haga clic en **Más servicios** y, en la lista de recursos, escriba **Recovery Services**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de Servicios de recuperación**.
   
   ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>
   
   Se muestra la lista de almacenes de Servicios de recuperación. En la lista, seleccione el almacén que desea eliminar.
   
   ![elegir el almacén en la lista](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. En la vista de almacén, examine el panel **Essentials** . Para eliminar un almacén, no puede haber ningún elemento protegido. Si ve un número que no sea cero en **Elementos de copia de seguridad** o en **Servidores de administración de copias de seguridad**, debe quitar estos elementos para poder eliminar el almacén.
   
    ![Consulta de los elementos protegidos en el panel Essentials](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)
   
    Las máquinas virtuales y los archivos o carpetas se consideran elementos de Backup y se enumeran en el área **Elementos de copia de seguridad** del panel Essentials. Se muestra un servidor DPM en el área **Servidores de administración de copias de seguridad** del panel Essentials. **Elementos replicados** pertenecen al servicio Azure Site Recovery.
3. Para empezar a quitar los elementos protegidos del almacén, búsquelos en el almacén. En el panel del almacén, haga clic en **Configuración**, **Elementos de copia de seguridad** para abrir la hoja.
   
    ![elegir el almacén en la lista](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)
   
    La hoja **Elementos de copia de seguridad** tiene listas independientes, en función del tipo de elemento: Azure Virtual Machines o carpetas y archivos (consulte la imagen). La lista Tipo de elemento predeterminada que se muestra es Azure Virtual Machines. Para ver la lista de elementos Archivos y carpetas del almacén, seleccione **Archivos y carpetas** en el menú desplegable.
4. Antes de poder eliminar un elemento del almacén que protege una máquina virtual, debe detener el trabajo de copia de seguridad del elemento y eliminar los datos del punto de recuperación. Para cada elemento del almacén, siga estos pasos:
   
    a. En la hoja **Elementos de copia de seguridad**, haga clic con el botón derecho en el elemento y, en el menú contextual, seleccione **Detener copia de seguridad**.
   
    ![detener el trabajo de copia de seguridad](./media/backup-azure-delete-vault/stop-the-backup-process.png)
   
    Se abre la hoja Detener copia de seguridad.
   
    b. En la hoja **Detener copia de seguridad**, en el menú **Elija una opción**, seleccione **Eliminar datos de copia de seguridad** > escriba el nombre del elemento > y haga clic en **Detener copia de seguridad**.
   
      Escriba el nombre del elemento para confirmar que desea eliminarlo. El botón **Detener copia de seguridad** no se activará hasta que confirme el elemento que se va a detener. Si no ve el cuadro de diálogo para escribir el nombre del elemento de copia de seguridad, ha elegido la opción **Retener datos de copia de seguridad** .
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)
   
      Opcionalmente, puede proporcionar el motivo por el qué va a eliminar los datos y agregar comentarios. Tras hacer clic en **Detener copia de seguridad**, permita que el trabajo de eliminación se complete antes de intentar eliminar el almacén. Para comprobar que el trabajo se ha completado, compruebe los mensajes de Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Una vez finalizado el trabajo, recibirá un mensaje que indica que se ha detenido el proceso de copia de seguridad y se han eliminado los datos de copia de seguridad del elemento.
   
    c. Después de eliminar un elemento de la lista, en el menú **Elementos de copia de seguridad**, haga clic en **Actualizar** para ver los restantes elementos del almacén.
   
      ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/empty-items-list.png)
   
      Cuando no haya ningún elemento en la lista, desplácese al panel **Essentials** en la hoja del almacén de Backup. No debería haber **elementos de copia de seguridad**, **servidores de administración de copias de seguridad** ni **elementos replicados** en la lista. Si siguen aparecen elementos en el almacén, vuelva al paso tres y elija otra lista de tipos de elemento.  
5. Cuando no hay ningún elemento más en la barra de herramientas de almacén, haga clic en **Eliminar**.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-vault.png)
6. Cuando se le pida que confirme si desea eliminar el almacén, haga clic en **Sí**.
   
    Se elimina el almacén y el portal vuelve al menú **Nuevo servicio** .

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>¿Qué ocurre si he detenido el proceso de copia de seguridad pero he retenido los datos?
Si ha detenido el proceso de copia de seguridad, pero accidentalmente *retuvo* los datos, debe eliminar los datos de copia de seguridad antes de poder eliminar el almacén. Para eliminar los datos de copia de seguridad:

1. En la hoja **Elementos de copia de seguridad**, haga clic con el botón derecho en el elemento y, en el menú contextual, haga clic en **Eliminar datos de copia de seguridad**.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-backup-data-menu.png)
   
    Se abre la hoja **Eliminar datos de copia de seguridad** .
2. En la hoja **Eliminar datos de copia de seguridad**, escriba el nombre del elemento y haga clic en **Eliminar**.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-retained-vault.png)
   
    Una vez eliminados los datos, vaya al paso 4c y continúe con el proceso.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Eliminación de un almacén usado para proteger un servidor DPM
Antes de poder eliminar un almacén usado para proteger un servidor DPM, debe borrar los puntos de recuperación que se han creado y, después, anular el registro del servidor del almacén.

Para eliminar los datos asociados a un grupo de protección:

1. En la Consola de administrador DPM, haga clic en **Protección**, seleccione un grupo de protección, seleccione el miembro del grupo de protección y, en la cinta de herramientas, haga clic en **Quitar**. Debe seleccionar el miembro para que el botón **Quitar** aparezca en la cinta de herramientas. En el ejemplo, el miembro es **dummyvm9**. Si hay varios miembros del grupo de protección, mantenga presionada la tecla Ctrl para seleccionarlos.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)
   
    Se abre el cuadro de diálogo **Detener la protección** .
2. En el cuadro de diálogo **Detener protección**, seleccione **Eliminar datos protegidos** y haga clic en **Detener protección**.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)
   
    No desea retener los datos protegidos porque necesita borrar el almacén para eliminarlos. Según cuántos puntos de recuperación y la cantidad de datos que hay en el grupo de protección, eliminar los datos puede tardar desde unos segundos hasta unos pocos minutos. El cuadro de diálogo **Detener la protección** muestra el estado cuando se ha completado el trabajo.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Repita este proceso para todos los miembros de todos los grupos de protección.
   
    Debe quitar todos los datos protegidos y los grupos de protección.
4. Después de eliminar todos los miembros del grupo de protección, cambie a Azure Portal. Abra el panel del almacén y asegúrese de que **Elementos de copia de seguridad**, **Servidores de administración de copias de seguridad** y **Elementos replicados están vacíos**. En la barra de herramientas del almacén, haga clic en **Eliminar**.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-vault.png)
   
    Si hay servidores de administración de Backup registrados en el almacén, no podrá eliminar el almacén, ni siquiera aunque no haya ningún dato en el almacén. Si cree que ha eliminado los servidores de administración de Backup asociados al almacén, pero todavía hay servidores en el panel **Essentials** , consulte [Búsqueda de servidores de administración de Backup registrados en el almacén](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Cuando se le pida que confirme si desea eliminar el almacén, haga clic en **Sí**.
   
    Se elimina el almacén y el portal vuelve al menú **Nuevo servicio** .

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Eliminación de un almacén usado para proteger un servidor de producción
Antes de poder eliminar un almacén usado para proteger un servidor de producción, debe eliminar o anular el registro del servidor del almacén.

Para eliminar el servidor de producción asociado con el almacén:

1. En Azure Portal, abra el panel del almacén y haga clic en **Configuración** > **Infraestructura de copia de seguridad** > **Servidores de producción**.
   
    ![abrir la hoja Servidores de producción](./media/backup-azure-delete-vault/delete-production-server.png)
   
    Se abre la hoja **Servidores de producción** , donde se muestran todos los servidores de producción del almacén.
   
    ![lista de servidores de producción](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. En la hoja **Servidores de producción**, haga doble clic en el servidor y haga clic en **Eliminar**.
   
    ![eliminar servidor de producción ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)
   
    Se abre la hoja **Eliminar** .
   
    ![eliminar servidor de producción ](./media/backup-azure-delete-vault/delete-blade.png)
3. En la hoja **Eliminar**, confirme el nombre del servidor que se va a eliminar y haga clic en **Eliminar**. Debe escribir correctamente el nombre del servidor para activar el botón **Eliminar** .
   
    Una vez que se ha eliminado el almacén, recibirá un mensaje que indica que se ha eliminado el almacén. Después de eliminar todos los servidores del almacén, desplácese al panel Essentials en el panel del almacén.
4. En el panel del almacén, asegúrese de que **Elementos de copia de seguridad**, **Servidores de administración de copias de seguridad** y **Elementos replicados están vacíos**. En la barra de herramientas del almacén, haga clic en **Eliminar**.
5. Cuando se le pida que confirme si desea eliminar el almacén, haga clic en **Sí**.
   
    Se elimina el almacén y el portal vuelve al menú **Nuevo servicio** .

## <a name="delete-a-backup-vault"></a>Eliminación de un almacén de Backup
Las instrucciones siguientes sirven para eliminar un almacén de Backup en el portal clásico. El almacén de Backup y el almacén de Recovery Services son los mismos: antes de poder eliminar el almacén, elimine los elementos y los datos retenidos.

1. Abra el portal clásico.
2. En la lista de almacenes de Backup, seleccione el almacén que desea eliminar.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)
   
    Se abre el panel del almacén. Consulte el número de instancias de Windows Server o Azure Virtual Machines asociadas al almacén. Consulte también el espacio de almacenamiento total utilizado en Azure. Debe detener todos los trabajos de copia de seguridad y eliminar los datos existentes antes de eliminar el almacén.
3. Haga clic en la pestaña **Elementos protegidos** y, después, en **Detener la protección**
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)
   
    Aparece el cuadro de diálogo **Detener la protección de "su almacén"** .
4. En el cuadro de diálogo **Detener la protección de "su almacén"**, seleccione **Eliminar los datos de copia de seguridad asociados** y haga clic en la ![marca de verificación](./media/backup-azure-delete-vault/checkmark.png). <br/>
    Si lo desea, puede elegir un motivo para detener la protección y proporcionar un comentario.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)
   
    Después de eliminar los elementos del almacén, este estará vacío.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. En la lista de pestañas, haga clic en **Elementos registrados**. Para cada elemento registrado en el almacén, seleccione el elemento y haga clic en **Anular registro**.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/classic-portal-unregister.png)
6. En la lista de pestañas, haga clic en **Panel** para abrir esa pestaña. Compruebe que no hay ningún servidor registrado ni Azure Virtual Machines protegidas en la nube. Compruebe también que no hay ningún dato en el almacenamiento. Haga clic en **Eliminar** para eliminar el almacén.
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)
   
    Se abre la pantalla de confirmación Eliminar almacén de credenciales de copia de seguridad. Seleccione una opción indicando el motivo por el qué está eliminando el almacén y haga clic en  ![marca de verificación](./media/backup-azure-delete-vault/checkmark.png). <br/>
   
    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)
   
    Se elimina el almacén y se vuelve al panel del portal clásico.

### <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Búsqueda de servidores de administración de Backup registrados en el almacén
Si tiene varios servidores registrados en un almacén, puede ser difícil recordarlos. Para ver los servidores registrados en el almacén y eliminarlos:

1. Abra el panel del almacén.
2. En el panel **Información esencial**, haga clic en **Configuración** para abrir la hoja.
   
    ![abrir hoja Configuración](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. En la **hoja Configuración**, haga clic en **Infraestructura de copia de seguridad**.
4. En la hoja **Infraestructura de copia de seguridad**, haga clic en **Servidores de administración de copias de seguridad**. Se abre la hoja Servidores de administración de copias de seguridad.
   
    ![lista Servidores de administración de copias de seguridad](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Para eliminar un servidor de la lista, haga clic con el botón derecho en el nombre del servidor y haga clic en **Eliminar**.
    Se abre la hoja **Eliminar** .
6. En la hoja **Eliminar** , proporcione el nombre del servidor. Si es un nombre largo, puede copiarlo y pegarlo de la lista Servidores de administración de copias de seguridad. Después, haga clic en **Eliminar**.  




<!--HONumber=Nov16_HO3-->


