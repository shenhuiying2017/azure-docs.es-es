---
title: Uso del Azure Backup Server para hacer copia de seguridad de una granja de SharePoint en Azure | Microsoft Docs
description: "Uso del Azure Backup Server para hacer copia de seguridad de los datos de SharePoint y restaurarlos. En este artículo se proporciona la información sobre cómo configurar la granja de SharePoint para almacenar los datos deseados en Azure. Puede restaurar los datos protegidos de SharePoint desde disco o desde Azure."
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
ms.assetid: 34ba87a4-91f1-4054-a4a1-272af1e15496
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: 3ed000affd326eb1bd7c99773ec021ad6e03cc3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Realización de una copia de seguridad de una granja de SharePoint en Azure
La copia de seguridad de una granja de SharePoint en Microsoft Azure se crea mediante el Azure Backup Server (MABS) casi de la misma manera que realiza la copia de seguridad de otros orígenes de datos. Copia de seguridad de Azure ofrece flexibilidad en la programación de copias de seguridad para crear puntos de copia de seguridad diarios, semanales, mensuales o anuales, y le ofrece diferentes opciones de directiva de retención para varios puntos de copia de seguridad. También ofrece la posibilidad de almacenar copias en discos locales para conseguir objetivos de tiempo de recuperación (RTO) más rápidos y de almacenar copias en Azure, para una retención económica más a largo plazo.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Las versiones compatibles de SharePoint y relacionadas con escenarios de protección
Copia de seguridad de Azure para DPM admite los siguientes escenarios:

| Carga de trabajo | Versión | Implementación de SharePoint | Protección y recuperación |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint implementado como un servidor físico o una máquina virtual de Hyper-V/VmWare <br> -------------- <br> SQL AlwaysOn | Opciones de protección de recuperación de la granja de SharePoint: granja de servidores de recuperación, base de datos y archivo, o elemento de la lista de puntos de recuperación de disco.  Recuperación de base de datos y granja de servidores a partir de puntos de recuperación de Azure. |

## <a name="before-you-start"></a>Antes de comenzar
Antes de realizar una copia de seguridad de una granja de SharePoint en Azure, hay algunas cuantas cosas que debe confirmar.

### <a name="prerequisites"></a>Requisitos previos
Antes de continuar, asegúrese de que ha [instalado y preparado el Azure Backup Server](backup-azure-microsoft-azure-backup.md) para proteger las cargas de trabajo.

### <a name="protection-agent"></a>Agente de protección
El agente de protección debe instalarse en el servidor que ejecuta SharePoint, en los servidores que ejecutan SQL Server y en todos los demás servidores que forman parte de la granja de SharePoint. Para obtener más información sobre cómo configurar el agente de protección, consulte [Programa de instalación del agente de protección](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  La única excepción es que solo instale al agente en un único servidor web front-end (WFE). DPM necesita el agente en un servidor WFE con el único fin de servir como punto de entrada para la protección.

### <a name="sharepoint-farm"></a>Granja de SharePoint
Para cada 10 millones de elementos del conjunto de servidores, debe haber al menos 2 GB de espacio en el volumen donde se encuentra la carpeta MABS. Este espacio se requiere para la generación del catálogo. Para que MABS pueda recuperar elementos específicos (colecciones de sitios, sitios, listas, bibliotecas de documentos, carpetas, documentos individuales y elementos de lista), la generación de catálogos crea una lista de las direcciones URL que están dentro de cada base de datos de contenido. Puede ver la lista de direcciones URL en el panel de elementos recuperables en el área de tareas de **recuperación** de la Consola de administrador MABS.

### <a name="sql-server"></a>SQL Server
MABS se ejecuta como una cuenta LocalSystem. Para realizar una copia de seguridad de las bases de datos SQL Server, MABS necesita privilegios de administrador del sistema en esa cuenta en el servidor que ejecuta SQL Server. Establezca NT AUTHORITY\SYSTEM en *sysadmin* en el servidor que ejecuta SQL Server antes de proceder con la copia de seguridad.

Si la granja de SharePoint tiene bases de datos SQL Server que están configuradas con alias de SQL Server, instale los componentes de cliente de SQL Server en el servidor web front-end que MABS vaya a proteger.

### <a name="sharepoint-server"></a>SharePoint Server
Si bien el rendimiento depende de muchos factores, como el tamaño de la granja de SharePoint, de forma orientativa, un servidor MABS puede proteger una granja de SharePoint de 25 TB.

### <a name="whats-not-supported"></a>Lo que no se admite
* Que MABS proteja una granja de SharePoint y no proteja índices de búsqueda o bases de datos de servicios de aplicaciones. Deberá configurar la protección de estas bases de datos por separado.
* Que MABS no proporcione copia de seguridad de bases de datos SQL Server de SharePoint hospedadas en recursos compartidos de servidor de archivos de escalabilidad horizontal (SOFS).

## <a name="configure-sharepoint-protection"></a>Configuración de la protección de SharePoint
Antes de poder usar MABS para proteger SharePoint, debe configurar el servicio VSS Writer de SharePoint (servicio WSS Writer) mediante **ConfigureSharePoint.exe**.

Puede encontrar **ConfigureSharePoint.exe** en la carpeta [ruta de instalación de MABS]\bin en el servidor web front-end. Esta herramienta proporciona al agente de protección las credenciales para la granja de servidores de SharePoint. Debe ejecutarlo en un solo servidor WFE. Si tiene varios servidores WFE, seleccione solo uno al configurar un grupo de protección.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Para configurar el servicio VSS Writer de SharePoint
1. En el servidor WFE, en un símbolo del sistema, vaya a [ubicación de instalación de MABS]\bin\
2. Escriba ConfigureSharePoint -EnableSharePointProtection.
3. Escriba las credenciales de administrador de la granja de servidores. Esta cuenta debe ser miembro del grupo de administradores local en el servidor WFE. Si el administrador de la granja no es un administrador local, conceda los permisos siguientes en el servidor WFE:
   * Conceda al grupo WSS_Admin_WPG control total sobre la carpeta DPM (%Program Files%\Microsoft Azure Backup\DPM).
   * Conceda al grupo WSS_Admin_WPG acceso de lectura a la clave del Registro de DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Deberá ejecutar ConfigureSharePoint.exe cada vez que haya un cambio en las credenciales de administrador de la granja de servidores de SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Realización de una copia de seguridad de una granja de SharePoint con MABS
Después de que haya configurado MABS y la granja de SharePoint tal y como se ha explicado anteriormente, SharePoint se puede proteger con MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Para proteger una granja de SharePoint
1. En la pestaña **Protección** de la Consola de administrador MABS, haga clic en **Nuevo**.
    ![Nueva pestaña de protección](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. En la página **Seleccionar tipo de grupo de protección** del asistente **Crear nuevo grupo de protección**, seleccione **Servidores** y luego haga clic en **Siguiente**.

    ![Seleccionar tipo de grupo de protección](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. En la pantalla **Seleccionar miembros del grupo**, active la casilla del servidor de SharePoint que quiere proteger y haga clic en **Siguiente**.

    ![Seleccionar a miembros del grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Con el agente de protección instalado, puede ver el servidor en el asistente. MABS también muestra su estructura. Como ha ejecutado ConfigureSharePoint.exe, MABS se comunica con el servicio VSS Writer de SharePoint y con sus bases de datos SQL Server correspondientes y reconoce la estructura de la granja de SharePoint, las bases de datos de contenido asociadas y cualquier elemento correspondiente.
   >
   >
4. En la pantalla **Seleccionar método de protección de datos**, escriba el nombre del **grupo de protección** y seleccione los *métodos de protección* que prefiera. Haga clic en **Next**.

    ![Seleccionar método de protección de datos](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > El método de protección en disco ayuda a cumplir objetivos de tiempo de recuperación corto.
   >
   >
5. En la pantalla **Especificar objetivos a corto plazo**, seleccione su **intervalo de retención** preferido y especifique cuándo quiere que se creen las copias de seguridad.

    ![Especificar objetivos a corto plazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Como la recuperación suele ser más necesaria para datos de menos de cinco días, seleccionamos un intervalo de retención de cinco días en el disco y nos aseguramos que la copia de seguridad se crea durante las horas que no son de producción para este ejemplo.
   >
   >
6. Revise el espacio en disco del grupo de almacenamiento asignado al grupo de protección y haga clic en **Siguiente**.
7. Para cada grupo de protección, MABS asigna espacio en disco para almacenar y administrar réplicas. En este punto, MABS debe crear una copia de los datos seleccionados. Seleccione cómo y cuándo quiere que se cree la réplica y haga clic en **Siguiente**.

    ![Elegir método de creación de réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Para asegurarse de que no se aplica el tráfico de red, seleccione una hora fuera del horario de producción.
   >
   >
8. MABS garantiza la integridad de los datos con comprobaciones de coherencia en la réplica. Hay dos opciones disponibles. Puede definir una programación para ejecutar comprobaciones de coherencia. También DPM puede ejecutar comprobaciones de coherencia automáticamente en la réplica cada vez que sea incoherente. Seleccione la opción que prefiera y haga clic en **Siguiente**.

    ![Comprobación de coherencia](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. En la pantalla **Especificar datos de protección en línea**, seleccione la granja de SharePoint que quiere proteger y haga clic en **Siguiente**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. En la pantalla **Especificar una programación de copia de seguridad en línea**, seleccione la programación que prefiera y haga clic en **Siguiente**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS ofrece un máximo de dos copias de seguridad diarias a Azure desde el punto de copia de seguridad del disco más reciente disponible. Copia de seguridad de Azure también puede controlar la cantidad de ancho de banda WAN que puede usarse para copias de seguridad en horas de máxima y mínima actividad mediante la [limitación de red de Copia de seguridad de Azure](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Según la programación de copia de seguridad seleccionada, en la página **Especificar la directiva de retención en línea** , seleccione la directiva de retención para los puntos de copia de seguridad diarios, semanales, mensuales y anuales.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS emplea un esquema de retención abuelo-padre-hijo en el que se puede elegir una directiva de retención diferente para distintos puntos de copia de seguridad.
    >
    >
12. Como ocurre con el disco, se debe crear una réplica de punto de referencia inicial en Azure. Seleccione la opción preferida para crear una copia de seguridad inicial en Azure y haga clic en **Siguiente**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Revise la configuración seleccionada en la página **Resumen** y haga clic en **Crear grupo**. Verá un mensaje de operación completada correctamente una vez que se haya creado el grupo de protección.

    ![Resumen](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restauración de un elemento de SharePoint desde un disco con MABS
En el ejemplo siguiente, el *elemento de recuperación de SharePoint* se eliminó accidentalmente y es necesario recuperarlo.
![Protección de SharePoint con MABS4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra la **Consola de administrador DPM**. Todas las granjas de SharePoint que están protegidas con DPM se muestran en la pestaña **Protección** .

    ![Protección de SharePoint con MABS3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para empezar a recuperar el elemento, seleccione la pestaña **Recuperación** .

    ![Protección de SharePoint con MABS5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Puede buscar en SharePoint el *elemento de recuperación* mediante caracteres comodín dentro un intervalo de puntos de recuperación.

    ![Protección de SharePoint con MABS6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Seleccione el punto de recuperación adecuado en los resultados de búsqueda, haga clic con el botón derecho en él y elija **Recuperar**.
5. También puede desplazarse por varios puntos de recuperación y seleccionar una base de datos o un elemento para recuperar. Seleccione **Fecha > Hora de recuperación** y luego el elemento **Base de datos > Granja de SharePoint > Punto de recuperación > adecuado**.

    ![Protección de SharePoint con MABS7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Haga clic con el botón derecho en el elemento y seleccione**Recuperar** para abrir el **Asistente para recuperación**. Haga clic en **Siguiente**.

    ![Revisar selección de recuperación](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Seleccione el tipo de recuperación que quiere realizar y haga clic en **Siguiente**.

    ![Tipo de recuperación](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > La selección de **Recuperar en sitio original** en el ejemplo, recupera el elemento en el sitio de SharePoint original.
   >
   >
8. Seleccione el **proceso de recuperación** que quiere usar.

   * Seleccione **Recuperar sin una granja de servidores de recuperación** si la granja de SharePoint no ha cambiado y es la misma que el punto de recuperación que se restaura.
   * Seleccione **Recuperar con una granja de servidores de recuperación** si la granja de servidores de SharePoint ha cambiado desde que se creó el punto de recuperación.

     ![proceso de recuperación](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Proporcione una ubicación provisional para la instancia de SQL Server para recuperar la base de datos temporalmente. Proporcione también un recurso compartido de archivos de almacenamiento provisional en MABS y el servidor que ejecuta SharePoint para recuperar el elemento.

    ![Ubicación provisional1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS conecta la base de datos de contenido que hospeda el elemento de SharePoint con la instancia temporal de SQL Server. Desde la base de datos de contenido, recupera el elemento y lo coloca en la ubicación del archivo de almacenamiento provisional en MABS. Ahora, el elemento recuperado en la ubicación de almacenamiento provisional debe exportarse a la ubicación provisional de la granja de SharePoint.

    ![Ubicación provisional2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Seleccione **Especificar opciones de recuperación**y aplique la configuración de seguridad a la granja de SharePoint o aplique la configuración de seguridad del punto de recuperación. Haga clic en **Siguiente**.

    ![Opciones de recuperación](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Puede limitar el uso de ancho de banda de red. Esto minimiza el impacto en el servidor de producción durante las horas de producción.
    >
    >
11. Revise la información de resumen y haga clic en **Recuperar** para empezar la recuperación del archivo.

    ![Resumen de recuperación](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Ahora seleccione la pestaña **Supervisión** en la **Consola de administrador MABS** para ver el **Estado** de la recuperación.

    ![Estado de recuperación](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > El archivo ya se ha restaurado. Puede actualizar el sitio de SharePoint para comprobar el archivo restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restauración de una base de datos de SharePoint de Azure con DPM
1. Para recuperar una base de datos de contenido de SharePoint, desplácese por los diversos puntos de recuperación (tal como se mostró anteriormente) y seleccione aquel que quiera recuperar.

    ![Protección de SharePoint con MABS8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Haga doble clic en el punto de recuperación de SharePoint para mostrar la información de catálogo de SharePoint disponible.

   > [!NOTE]
   > Como la granja de SharePoint está protegida para la retención a largo plazo en Azure, no hay información de catálogo (metadatos) disponible en MABS. Como resultado, cada vez que deba recuperar una base de datos de contenido de SharePoint en un momento dado, deberá volver a catalogar la granja de SharePoint.
   >
   >
3. Haga clic en **Volver a catalogar**.

    ![Protección de SharePoint con MABS10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Aparece la ventana de estado **Volver a catalogar la nube** .

    ![Protección de SharePoint con MABS11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Cuando finaliza la catalogación, el estado cambia a *Correcto*. Haga clic en **Cerrar**.

    ![Protección de SharePoint con MABS12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Haga clic en el objeto de SharePoint que se muestra en la pestaña **Recuperación** de MABS para obtener la estructura de la base de datos de contenido. Haga clic con el botón derecho en el elemento y luego haga clic en **Recuperar**.

    ![Protección de SharePoint con MABS13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. En este momento, siga el [los pasos de recuperación anteriormente en este artículo](#restore-a-sharepoint-item-from-disk-using-dpm) para recuperar una base de datos de contenido de SharePoint desde el disco.

## <a name="faqs"></a>Preguntas más frecuentes
P: ¿Puedo recuperar un elemento de SharePoint en la ubicación original si SharePoint está configurado con SQL AlwaysOn (con protección en disco)?<br>
R: Sí, se puede recuperar el elemento en el sitio de SharePoint original.

P: ¿Puedo recuperar una base de datos de SharePoint en la ubicación original si SharePoint está configurada con SQL AlwaysOn?<br>
R: Como las bases de datos de SharePoint están configuradas en SQL AlwaysOn, no se pueden modificar a menos que se quite el grupo de disponibilidad. En consecuencia, MABS no puede restaurar la base de datos en la ubicación original. Puede recuperar una base de datos SQL Server en otra instancia de SQL Server.

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre la protección de SharePoint con MABS; consulte [Serie de vídeos: protección de SharePoint con DPM](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
