---
title: "Recuperación de datos de otro Azure Backup Server | Microsoft Docs"
description: "Recupere los datos que ha protegido en un almacén de Azure Backup desde cualquier Azure Backup Server registrado en dicho almacén."
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: adigan;giridham;trinadhk;markgal
ms.translationtype: Human Translation
ms.sourcegitcommit: 14cc190a7d1cde1181a6f26ef83095bc601f7fbb
ms.openlocfilehash: 36c4e1865c99dd1c55be798e1d310a02f2af0864
ms.contentlocale: es-es
ms.lasthandoff: 01/28/2017


---
# Recuperación de datos de otro Azure Backup Server en el almacén de copia de seguridad
<a id="recovering-data-from-another-azure-backup-server-in-the-backup-vault" class="xliff"></a>
Ahora puede recuperar los datos que ha protegido en un almacén de Azure Backup desde cualquier Azure Backup Server registrado en dicho almacén. El proceso para hacerlo está completamente integrado en la consola de administración de ABS y es similar a los otros flujos de trabajo de recuperación.

> [!NOTE]
> Tanto este artículo como los siguientes pasos también se pueden aplicar al [paquete acumulativo de actualizaciones 7 de System Center Data Protection Manager] (https://support.microsoft.com/en-us/kb/3065246) con el [agente de Azure Backup más reciente](http://aka.ms/azurebackup_agent).
>
>

## Recuperación de datos de otro Azure Backup Server
<a id="recover-data-from-another-azure-backup-server" class="xliff"></a>
Para recuperar datos de otro Azure Backup Server:

1. En la pestaña **Recuperación** de la consola de administración de ABS, haga clic en **'Agregar DPM externo'** (en la parte superior izquierda de la pantalla).   
    ![Agregar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Descargue las nuevas **credenciales del almacén** asociado al **Azure Backup Server** en donde se recuperan los datos, elija el Azure Backup Server en la lista de servidores registrados en el almacén de copia de seguridad y escriba la **frase de contraseña de cifrado** asociada al servidor cuyos datos se recuperan.

    ![Credenciales externas de DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Solo los servidores de copia de seguridad de Azure asociados al mismo almacén de registro pueden recuperar datos unos de otros.
   >
   >

    Una vez agregado correctamente el Azure Backup Server externo, puede examinar los datos del servidor externo y el Azure Backup Server local en la pestaña **Recuperación**.
3. Vaya a la lista disponible de servidores de producción protegidos por el Azure Backup Server externo y seleccione el origen de datos adecuado.

    ![Ir al servidor DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Seleccione el **mes y año** en la lista desplegable **Puntos de recuperación**, seleccione la **fecha de recuperación** en que se creó el punto de recuperación y seleccione la **hora de recuperación**.

    Aparecerá una lista de archivos y carpetas en el panel inferior que se puede examinar y recuperar en cualquier ubicación.

    ![Puntos de recuperación del servidor DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Haga clic con el botón derecho en el elemento apropiado y haga clic en **Recuperar**.

    ![Recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/recover.png)
6. Revise la **Selección de recuperación**. Compruebe los datos y la hora de la copia de seguridad que se va a recuperar, así como el origen desde el que se creó la copia de seguridad. Si la selección no es correcta, haga clic en **Cancelar** a fin de volver a la pestaña de recuperación para seleccionar el punto de recuperación adecuado. Si la selección es correcta, haga clic en **Siguiente**.

    ![Resumen de recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Seleccione **Recuperar en una ubicación alternativa**. **Examinar** para ir a la ubicación correcta para la recuperación.

    ![Ubicación alternativa de la recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Elija una de las siguientes opciones: **Crear copia**, **Omitir** o **Sobrescribir**.

   * **Crear copia** creará una copia del archivo en caso de que haya un conflicto de nombres.
   * **Omitir** omitirá la recuperación del archivo en caso de que haya un conflicto de nombres.
   * **Sobrescribir** sobrescribirá la copia existente en la ubicación especificada en caso de que haya un conflicto de nombres.

     Elija la opción adecuada para **Restaurar seguridad**. Puede aplicar la configuración de seguridad del equipo de destino donde se están recuperando los datos o la configuración de seguridad aplicable al producto en el momento en que se creó el punto de recuperación.

     Identifique si se enviará una **notificación** una vez que se complete la recuperación correctamente.

     ![Notificaciones de recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. En la pantalla **Resumen** se muestran las opciones elegidas hasta el momento. Al hacer clic en **'Recuperar'**, se recuperarán los datos en la ubicación local adecuada.

    ![Resumen de las opciones de recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > El trabajo de recuperación se puede supervisar en la pestaña **Supervisión** del Azure Backup Server.
   >
   >

    ![Supervisión de la recuperación](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Puede hacer clic en **Borrar DPM externo** en la pestaña **Recuperación** del servidor DPM para quitar la vista del servidor DPM externo.

    ![Borrar DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## Solución de mensajes de error
<a id="troubleshooting-error-messages" class="xliff"></a>
| Nº | Mensaje de error | Pasos para solucionar problemas |
|:---:|:--- |:--- |
| 1. |Este servidor no está registrado en el almacén especificado por las credenciales del almacén. |**Causa:** este error aparece cuando el archivo de credenciales del almacén seleccionado no pertenece al almacén de copia de seguridad asociado al Azure Backup Server en el que se intenta la recuperación. <br> **Resolución:** descargue el archivo de credenciales del almacén del almacén de copia de seguridad en el que está registrado el Azure Backup Server. |
| 2. |Los datos recuperables no están disponibles o el servidor seleccionado no es un servidor DPM. |**Causa:** no hay ningún otro Azure Backup Server registrado en el almacén de copia de seguridad, los servidores aún no han cargado los metadatos o el servidor seleccionado no es un Azure Backup Server (también llamado servidor de Windows o cliente de Windows). <br> **Resolución:** si hay otros servidores de copia de seguridad de Azure registrados en el almacén de copia de seguridad, asegúrese de que está instalado el agente de Azure Backup más reciente. <br>Si hay otros servidores de copia de seguridad de Azure registrados en el almacén de copia de seguridad, espere un día después de la instalación para iniciar el proceso de recuperación. Por la noche se cargarán en la nube los metadatos de todas las copias de seguridad protegidas. Los datos estarán disponibles para la recuperación. |
| 3. |Ningún otro servidor DPM está registrado en este almacén. |**Causa:** no hay ningún otro Azure Backup Server registrado en el almacén desde el que se intenta realizar la recuperación.<br>**Resolución:** si hay otros servidores de copia de seguridad de Azure registrados en el almacén de copia de seguridad, asegúrese de que está instalado el agente de Azure Backup más reciente.<br>Si hay otros servidores de copia de seguridad de Azure registrados en el almacén de copia de seguridad, espere un día después de la instalación para iniciar el proceso de recuperación. Por la noche se cargarán en la nube los metadatos de todas las copias de seguridad protegidas. Los datos estarán disponibles para la recuperación. |
| 4. |La frase de contraseña de cifrado especificada no coincide con la frase de contraseña asociada al siguiente servidor: **<server name>** |**Causa:** la frase de contraseña de cifrado que se usa en el proceso de cifrado de los datos del Azure Backup Server que se recuperan no coincide con la proporcionada. El agente no puede descifrar los datos. Por lo tanto, se produce un error en la recuperación.<br>**Resolución:** especifique la misma frase de contraseña de cifrado asociada al Azure Backup Server cuyos datos se recuperan. |

## Preguntas más frecuentes:
<a id="frequently-asked-questions" class="xliff"></a>
1. **¿Por qué no puedo agregar un servidor DPM externo desde otro servidor DPM después de instalar el paquete acumulativo de actualizaciones 7 y el agente de Azure Backup más reciente? (Se aplica en caso de que se utilice SC DPM 2012 R2)**

    A) En el caso de los servidores DPM existentes con orígenes de datos protegidos en la nube (con un paquete acumulativo anterior al Paquete acumulativo de actualizaciones 7), tendrá que esperar al menos un día después de instalar UR7 y el agente de Copia de seguridad de Azure más reciente para empezar a *agregar el servidor DPM externo*. Esto es necesario para cargar los metadatos de los grupos de protección de DPM en Azure. Esto se produce la primera vez durante una tarea nocturna.
2. **¿Cuál es la versión mínima del agente de copia de seguridad de Azure necesario?**

    A) la versión mínima del agente de copia de seguridad de Azure para habilitar esta característica es 2.0.8719.0.  Para comprobar la versión del agente de Azure Backup, navegue hasta el Panel de control **>** Todos los elementos de Panel de control **>** Programas y características **>** Agente de Microsoft Azure Recovery Services. Si la versión es anterior a la 2.0.8719.0, descargue el [agente de Copia de seguridad de Azure más reciente](https://go.microsoft.com/fwLink/?LinkID=288905) e instálelo.

    ![Borrar DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## Pasos siguientes:
<a id="next-steps" class="xliff"></a>
•   [Preguntas más frecuentes de Azure Backup](backup-azure-backup-faq.md)

