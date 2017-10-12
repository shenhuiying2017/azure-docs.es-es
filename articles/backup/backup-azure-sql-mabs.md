---
title: Azure Backup para cargas de trabajo SQL Server con el servidor de copia de seguridad de Azure | Microsoft Docs
description: "Introducción a la copia de seguridad de bases de datos SQL Server mediante el servidor de copia de seguridad de Azure"
services: backup
documentationcenter: 
author: pvrk
manager: Shivamg
editor: 
ms.assetid: c8b1f7ec-26b1-4ef0-a3f2-91aec959daea
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: 2af9ebaa8f52690ed63406cbd85b77544d2d900d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Copia de seguridad de SQL Server en Azure con el servidor de copia de seguridad de Azure
Este artículo le guiará por los pasos de configuración de la copia de seguridad de bases de datos de SQL Server mediante el servidor de copia de seguridad de Microsoft Azure (MABS).

La administración de la copia de seguridad de bases de datos de SQL Server en Azure implica tres pasos:

1. Cree una directiva de copia de seguridad para proteger las bases de datos SQL Server en Azure.
2. Creación de copias de seguridad a petición en Azure.
3. Recuperación de la base de datos de Azure.

## <a name="before-you-start"></a>Antes de comenzar
Antes de comenzar, asegúrese de que ha [instalado y preparado el servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Crear una directiva de copia de seguridad para proteger las bases de datos SQL Server en Azure
1. En la IU del servidor de copia de seguridad de Azure, haga clic en el área de trabajo **Protección**.
2. En la cinta de herramientas, haga clic en **Nuevo** para crear un nuevo grupo de protección.

    ![Creación de un grupo de protección](./media/backup-azure-backup-sql/protection-group.png)
3. MABS muestra la pantalla de inicio con instrucciones sobre cómo crear un **grupo de protección**. Haga clic en **Siguiente**.
4. Seleccione **Servidores**.

    ![Selección de tipo de grupo de protección - 'Servidores'](./media/backup-azure-backup-sql/pg-servers.png)
5. Expanda la máquina de SQL Server en la que se encuentran las bases de datos en las que se van a realizar copias de seguridad. MABS muestra varios orígenes de datos de los que se puede hacer copias de seguridad desde ese servidor. Expanda **Todos los recursos compartidos SQL** y seleccione las bases de datos (en este caso, seleccionamos ReportServer$MSDPM2012 y ReportServer$MSDPM2012TempDB) para realizar copias de seguridad. Haga clic en **Siguiente**.

    ![Selección de base de datos SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Proporcione un nombre para el grupo de protección y active la casilla **Quiero protección en línea** .

    ![Método de protección de datos: disco a corto plazo y en línea de Azure](./media/backup-azure-backup-sql/pg-name.png)
7. En la pantalla **Especificar objetivos a corto plazo** , incluya las entradas necesarias para crear puntos de copia de seguridad en el disco.

    Aquí vemos que **Duración de retención** está establecida en *5 días*, **Frecuencia de la sincronización** está establecida en una vez cada *15 minutos*, que es la frecuencia con la que se realiza la copia de seguridad. **Copia de seguridad completa rápida** está establecido en *8:00 p.m*.

    ![Objetivos a corto plazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > A las 20:00 (de acuerdo con la entrada de la pantalla) se crea un punto de copia de seguridad cada día mediante la transferencia de los datos modificados desde el punto de copia de seguridad de las 20:00 del día anterior. Este proceso se denomina **Copia de seguridad completa rápida**. Mientras los registros transaccionales se sincronizan cada 15 minutos, si es necesario recuperar la base de datos a las 9:00 p.m., entonces se crea el punto mediante la reproducción de los registros de la última copia de seguridad completa rápida (8 p.m. en este caso).
   >
   >

8. Haga clic en **Siguiente**

    MABS muestra el espacio de almacenamiento total disponible y el uso del espacio de disco potencial.

    ![Asignación de disco](./media/backup-azure-backup-sql/pg-storage.png)

    De forma predeterminada, MABS crea un volumen por origen de datos (base de datos SQL Server) que se usa para la copia de seguridad inicial. Con este enfoque, el Administrador de discos lógicos (LDM) limita la protección de MABS a 300 orígenes de datos (bases de datos SQL Server). Para evitar esta limitación, seleccione la opción **Colocar datos en bloque de almacenamiento de DPM**. Si utiliza esta opción, MABS usa un único volumen para varios orígenes de datos, que permite a MABS proteger hasta 2000 bases de datos SQL.

    Si la opción **Expandir automáticamente los volúmenes** está seleccionada, MABS puede tener en cuenta el aumento del volumen de copia de seguridad a medida que crecen los datos de producción. Si la opción **Expandir automáticamente los volúmenes** no está seleccionada, MABS limitará el almacenamiento de copia de seguridad usado para los orígenes de datos del grupo de protección.
9. Los administradores tienen la opción de transferir esta copia de seguridad inicial manualmente (fuera de red) para evitar la congestión del ancho de banda o a través de la red. También pueden configurar la hora a la que se puede producir la transferencia inicial. Haga clic en **Siguiente**.

    ![Método de replicación inicial](./media/backup-azure-backup-sql/pg-manual.png)

    La copia de seguridad inicial requiere la transferencia de todo el origen de datos (base de datos SQL Server) desde el servidor de producción (máquina SQL Server) a MABS. Estos datos pueden ser grandes y la transferencia de los datos a través de la red podría superar el ancho de banda. Por este motivo, los administradores pueden elegir transferir la copia de seguridad inicial: **Manualmente** (mediante un soporte físico extraíble) para evitar la congestión del ancho de banda, o **Automáticamente a través de la red** (en un momento determinado).

    Una vez completada la copia de seguridad inicial, el resto de las copias de seguridad son copias de seguridad incrementales sobre la copia de seguridad inicial. Las copias de seguridad incrementales tienden a ser pequeñas y se transfieren fácilmente a través de la red.
10. Elija cuándo quiere que se ejecute la comprobación de coherencia y haga clic en **Siguiente**.

    ![Comprobación de coherencia](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS puede realizar una comprobación de coherencia para comprobar la integridad del punto de copia de seguridad. Calcula la suma de comprobación del archivo de copia de seguridad en el servidor de producción (máquina de SQL Server en este escenario) y los datos de copia de seguridad para ese archivo en MABS. En caso de conflicto, se supone que el archivo de copia de seguridad de MABS está dañado. MABS rectifica los datos de copia de seguridad mediante el envío de los bloques correspondientes a la suma de comprobación no coincidente. Como la comprobación de coherencia es una operación que requiere un rendimiento intensivo, los administradores tienen la opción de programarla o ejecutarla automáticamente.
11. Para especificar la protección en línea de los orígenes de datos, seleccione las bases de datos que se van proteger en Azure y haga clic en **Siguiente**.

    ![Selección de orígenes de datos](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Los administradores pueden elegir las programaciones de copia de seguridad y las directivas de retención que se ajusten a las directivas de la organización.

    ![Programación y retención](./media/backup-azure-backup-sql/pg-schedule.png)

    En este ejemplo, las copias de seguridad se realizan una vez a las 12:00 p.m. y a las 8 p.m. (parte inferior de la pantalla)

    > [!NOTE]
    > Es recomendable tener algunos puntos de recuperación a corto plazo en disco para una recuperación rápida. Estos puntos de recuperación se utilizan para la "recuperación operacional". Azure actúa como una ubicación válida fuera de sitio con unos contratos de nivel de servicio mayores y una disponibilidad garantizada.
    >
    >

    **Práctica recomendada**: asegúrese de que se programan copias de seguridad de Azure tras realizar copias de seguridad de disco local con DPM. Esto permitirá que la última copia de seguridad de disco se copie en Azure.

13. Seleccione la programación de la directiva de retención. Se proporcionan detalles sobre el funcionamiento de la directiva de retención en [Usar la copia de seguridad de Azure para cambiar su infraestructura de cintas](backup-azure-backup-cloud-as-tape.md).

    ![Directiva de retención](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    En este ejemplo:

    * Las copias de seguridad se realizan una vez a las 12:00 p.m. y a las 8 p.m. (parte inferior de la pantalla) y se conservan durante 180 días.
    * La copia de seguridad es el sábado a las 12:00 P.M. se conserva durante 104 semanas
    * La copia de seguridad es el último sábado de 12:00 P.M. se conserva durante 60 meses
    * La copia de seguridad es el último sábado de marzo a las 12:00 P.M. se conserva durante 10 años
14. Haga clic en **Siguiente** y seleccione la opción adecuada para transferir la copia de seguridad inicial a Azure. Puede elegir **Automáticamente a través de la red** o **Copia de seguridad sin conexión**.

    * **Automáticamente a través de la red** transferirá los datos de copia de seguridad a Azure en función de la programación seleccionada para la copia de seguridad.
    * Se explica cómo funciona **Copia de seguridad sin conexión** en [Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](backup-azure-backup-import-export.md).

    Seleccione el mecanismo de transferencia correspondiente para enviar la copia de seguridad inicial a Azure y haga clic en **Siguiente**.
15. Una vez revisados los detalles de la directiva en la pantalla **Resumen**, haga clic en el botón **Crear grupo** para completar el flujo de trabajo. Puede hacer clic en el botón **Cerrar** y supervisar el progreso del trabajo en el área de trabajo de supervisión.

    ![Creación de un grupo de protección en curso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Copia de seguridad a petición de una base de datos SQL Server
Aunque los pasos anteriores crean una directiva de copia de seguridad, solo se crea un “punto de recuperación” cuando se produce la primera copia de seguridad. En lugar de esperar a que se inicie el programador, los pasos siguientes activarán la creación de un punto de recuperación manualmente.

1. Espere hasta que el estado del grupo de protección muestre **Correcto** para la base de datos antes de crear el punto de recuperación.

    ![Miembros del grupo de protección](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Haga clic con el botón derecho en la base de datos y seleccione **Crear punto de recuperación**.

    ![Creación de punto de recuperación en línea](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Elija **Protección en línea** en el menú desplegable y haga clic en **Aceptar**. Se iniciará la creación de un punto de recuperación en Azure.

    ![Crear punto de recuperación](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Puede ver el progreso del trabajo en el área de trabajo **Supervisión** donde encontrará un trabajo en curso como el descrito en la ilustración siguiente.

    ![Consola de supervisión](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperación de una base de datos SQL Server de Azure
Los pasos siguientes son necesarios para recuperar una entidad protegida (base de datos SQL) de Azure.

1. Abra el consola de administración del servidor DPM. Vaya al área de trabajo **Recuperación** donde podrá ver los servidores de los que DPM realiza una copia de seguridad. Examine la base de datos requerida (en este caso ReportServer$MSDPM2012). Seleccione una hora en **Recuperación desde** que finalice con **En línea**.

    ![Selección de punto de recuperación](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Haga clic con el botón secundario en el nombre de la base de datos y haga clic en **Recuperar**.

    ![Recuperación desde Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM muestra los detalles del punto de recuperación. Haga clic en **Siguiente**. Para sobrescribir la base de datos, seleccione el tipo de recuperación **Recuperar en instancia original de servidor SQL Server**. Haga clic en **Siguiente**.

    ![Recuperación en ubicación original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    En este ejemplo, DPM permite la recuperación de la base de datos en otra instancia de SQL Server o en una carpeta de red independiente.
4. En la pantalla **Especificar opciones de recuperación** , puede seleccionar las opciones de recuperación como Límite de uso del ancho de banda de red para limitar el ancho de banda usado por la recuperación. Haga clic en **Siguiente**.
5. En la pantalla **Resumen** , podrá ver todas las configuraciones de recuperación proporcionadas hasta ahora. Haga clic en **Recuperar**.

    El estado de recuperación muestra que la base de datos se está recuperando. Puede hacer clic en **Cerrar** para cerrar el asistente y ver el progreso en el área de trabajo **Supervisión**.

    ![Inicio de proceso de recuperación](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Una vez completada la recuperación, la base de datos restaurada será coherente con la aplicación.

### <a name="next-steps"></a>Pasos siguientes:
•   [Preguntas más frecuentes de Azure Backup](backup-azure-backup-faq.md)
