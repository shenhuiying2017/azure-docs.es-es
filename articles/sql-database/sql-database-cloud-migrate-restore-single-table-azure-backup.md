<properties
	pageTitle="Restaurar una única tabla de la copia de seguridad de Base de datos SQL de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo restaurar una única tabla de la copia de seguridad de Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="daleche"/>


# Cómo restaurar una única tabla de la copia de seguridad de Base de datos SQL de Azure

Puede pasarle que haya modificado algunos datos por error en una base de datos SQL y ahora desee recuperar la única tabla afectada. En este artículo se describe cómo restaurar una única tabla de una base de datos de una de las copias de seguridad que Base de datos SQL de Azure realiza automáticamente, en función del nivel de rendimiento que haya seleccionado.

## Pasos de preparación: cambiar el nombre de la tabla y restaurar una copia de la base de datos
1. Identifique la tabla en la base de datos SQL de Azure que desee reemplazar por la copia restaurada. Use Microsoft SQL Management Studio para cambiar el nombre de la tabla. Por ejemplo, cambie el nombre de la tabla como &lt;nombre de la tabla&gt;\_old.

	**Nota** Para evitar que se bloquee, asegúrese de que no haya ninguna actividad ejecutándose en la tabla a la que le está cambiando el nombre. Si encuentra algún problema, realice este procedimiento durante una ventana de mantenimiento.

2. Restaure una copia de seguridad de su base de datos de un punto temporal anterior que desee recuperar. Para ello, consulte los pasos de [Recuperar una base de datos SQL de Azure de un error de usuario](../sql-database/sql-database-user-error-recovery.md).

	**Notas**:
	- El nombre de la base de datos restaurada tendrá el formato NombreBaseDeDatos+MarcaDeTiempo; por ejemplo, **Adventureworks2012\_2016-01-01T22-12Z**. Con este paso no se sobrescribirá el nombre de la base de datos existente en el servidor. Se trata de una medida de seguridad, diseñada para permitir que el usuario compruebe la base de datos restaurada antes de deshacerse de su base de datos actual y cambiar el nombre de la base de datos restaurada para su uso en producción.
	- En todos los niveles de rendimiento, del Basic al Premium, el servicio realiza una copia de seguridad automática, con métricas variables de retención de copia de seguridad, dependiendo del nivel:

| Restauración de BD | Nivel Basic | Niveles Standard | Niveles Premium |
| :-- | :-- | :-- | :-- |
| Restauración a un momento dado | Cualquier punto de restauración en 7 días|Cualquier punto de restauración en 14 días| Cualquier punto de restauración en 35 días|

## Copiar la tabla de la base de datos restaurada mediante la herramienta de migración de Base de datos SQL
1. Descargue e instale el [Asistente para migración de Base de datos SQL](https://sqlazuremw.codeplex.com).

2. Abra el Asistente para migración de Base de datos SQL; en la página **Select Process (Seleccionar proceso)**, seleccione **Database (Base de datos) en la sección Analyze/Migrate (Analizar o migrar)** y, a continuación, haga clic en **Next (Siguiente)**. ![Asistente para migración de Base de datos SQL: seleccionar proceso](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. En el cuadro de diálogo **Connect to Server (Conectar con el servidor)**, aplique la configuración siguiente:
 - **Server name (Nombre del servidor)**: su instancia de SQL de Azure
 - **Authentication (Autenticación)**: **SQL Server Authentication (Autenticación de SQL Server)**. Escriba sus credenciales de inicio de sesión.
 - **Database (Base de datos)**: **Master DB (List all databases) (BD maestra (Enumerar todas las bases de datos))**.
 - **Nota** De forma predeterminada, el asistente guarda la información de inicio de sesión. Si no quiere que lo haga, seleccione **Forget Login Information (No recordar la información de inicio de sesión)**. ![Asistente para migración de base de datos SQL: seleccionar origen, paso 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. En el cuadro de diálogo **Select Source (Seleccionar origen)**, seleccione el nombre de la base de datos que ha restaurado en la sección **Pasos de preparación** como el origen y, a continuación, haga clic en **Next (Siguiente)**.

	![Asistente para migración de base de datos SQL: seleccionar origen, paso 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. En el cuadro de diálogo **Choose Objects (Elegir objetos)**, seleccione la opción **Select specific database objects (Seleccionar objetos de base de datos específicos)** y, a continuación, seleccione la tabla (o tablas) que quiera migrar al servidor de destino. ![Asistente para migración de base de datos SQL: elegir objetos](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. En la página **Script Wizard Summary (Resumen del Asistente para scripts)**, haga clic en **Yes (Sí)** cuando se le pregunte si está listo para generar un script SQL. También tiene la opción de guardar el script TSQL para usarlo más adelante. ![Asistente para migración de base de datos SQL: resumen del Asistente de scripts](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. En la página **Results Summary (Resumen de resultados)**, haga clic en **Next (Siguiente)**. ![Asistente para migración de base de datos SQL: resumen de resultados](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. En la página **Setup Target Server Connection (Configurar conexión del servidor de destino)**, haga clic en **Connect to Server (Conectar al servidor)** y, a continuación, escriba los detalles de la manera siguiente:
	- **Server Name (Nombre del servidor)**: instancia de servidor de destino
	- **Authentication (Autenticación)**: **SQL Server authentication (Autenticación de SQL Server)**. Escriba sus credenciales de inicio de sesión.
	- **Database (Base de datos)**: **Master DB (List all databases) (BD maestra (Enumerar todas las bases de datos))**. Esta opción enumera todas las bases de datos del servidor de destino.

	![Asistente para migración de base de datos SQL: configurar conexión del servidor de destino](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Haga clic en **Connect (Conectar)**, seleccione la base de datos de destino a la que desea mover la tabla y, a continuación, haga clic en **Next (Siguiente)**. Esto debe finalizar con la ejecución del script generado anteriormente, y debería ver una copia de la tabla que se ha movido recientemente en la base de datos de destino.

## Paso de comprobación
1. Consulte y pruebe la tabla copiada recientemente para asegurarse de que los datos estén intactos. Tras la confirmación, puede deshacerse de la tabla a la que ha cambiado el nombre en la sección **Pasos de preparación** (por ejemplo, &lt;nombre de la tabla&gt;\_old).

<!---HONumber=AcomDC_0309_2016-->