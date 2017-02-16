---
title: "Restaurar una única tabla de la copia de seguridad de Azure SQL Database | Microsoft Docs"
description: "Obtenga información sobre cómo restaurar una única tabla de la copia de seguridad de Base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: dalechen
manager: felixwu
editor: 
ms.assetid: 340b41bd-9df8-47fb-adfc-03216de38a5e
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: dbdcfc9760df41ec1f52406b91cc211fc5ad8ef7


---
# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Restauración de una única tabla a partir de una copia de seguridad de Base de datos SQL de Azure
Puede pasarle que haya modificado algunos datos por error en una base de datos SQL y ahora desee recuperar la única tabla afectada. En este artículo se describe cómo restaurar una única tabla de una base de datos desde una de las [copias de seguridad automáticas](sql-database-automated-backups.md)de Base de datos SQL.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Pasos de preparación: cambiar el nombre de la tabla y restaurar una copia de la base de datos
1. Identifique la tabla en la base de datos SQL de Azure que desee reemplazar por la copia restaurada. Use Microsoft SQL Management Studio para cambiar el nombre de la tabla. Por ejemplo, cambie el nombre de la tabla como &lt;nombre de la tabla&gt;_old.
   
   > [!NOTE]
   > Para evitar que se bloquee, asegúrese de que no haya ninguna actividad ejecutándose en la tabla a la que le está cambiando el nombre. Si encuentra algún problema, realice este procedimiento durante una ventana de mantenimiento.
   >

2. Restaure una copia de seguridad de su base de datos al punto temporal que desee recuperar; para ello, siga los pasos descritos en [Restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore).
   
   > [!NOTE]
   > El nombre de la base de datos restaurada tendrá el formato nombreBaseDeDatos+MarcaDeTiempo; por ejemplo, **AdventureWorks2012_2016-01-01T22-12Z**. Con este paso no se sobrescribirá el nombre de la base de datos existente en el servidor. Se trata de una medida de seguridad, diseñada para permitirle comprobar la base de datos restaurada antes de deshacerse de su base de datos actual y cambiar el nombre de la base de datos restaurada para su uso en producción.
   
## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copiar la tabla de la base de datos restaurada mediante la herramienta de migración de Base de datos SQL

1. Descargue e instale el [Asistente para migración de Base de datos SQL](https://sqlazuremw.codeplex.com).
2. Abra el Asistente para migración de SQL Database; en la página **Select Process** (Seleccionar proceso), seleccione **Base de datos en la sección Analyze/Migrate** (Analizar o migrar) y, después, haga clic en **Siguiente**.

   ![Asistente para migración de Base de datos SQL: seleccionar proceso](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)

3. En el cuadro de diálogo **Conectar con el servidor** , aplique la configuración siguiente:

   * Nombre del servidor: **su servidor SQL**
   * Autenticación: **Autenticación de SQL Server**
   * Inicio de sesión: **sus datos de inicio de sesión**
   * Contraseña: **su contraseña**
   * **Master DB (List all databases)** [BD maestra (Enumerar todas las bases de datos)]
   
   > [!NOTE]
   > De forma predeterminada, el asistente guarda la información de inicio de sesión. Si no quiere que lo haga, seleccione **Forget Login Information (No recordar la información de inicio de sesión)**.
   >
   
     ![Asistente para migración de base de datos SQL: seleccionar origen, paso 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. En el cuadro de diálogo **Seleccionar origen**, seleccione el nombre de la base de datos que ha restaurado en la sección **Pasos de preparación** como el origen y, después, haga clic en **Siguiente**.
   
    ![Asistente para migración de base de datos SQL: seleccionar origen, paso 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)
5. En el cuadro de diálogo **Elegir objetos**, seleccione la opción **Seleccionar objetos de base de datos específicos** y, después, seleccione la tabla (o tablas) que quiera migrar al servidor de destino.
   ![Asistente para migración de base de datos SQL: elegir objetos](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)
6. En la página **Resumen del Asistente para scripts**, haga clic en **Sí** cuando se le pregunte si está listo para generar un script de SQL. También tiene la opción de guardar el script TSQL para usarlo más adelante.
   ![Asistente para migración de base de datos SQL: resumen del Asistente de scripts](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)
7. En la página **Results Summary** (Resumen de resultados), haga clic en **Siguiente**.
   ![Asistente para migración de base de datos SQL: resumen de resultados](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)
8. En la página **Setup Target Server Connection** (Configurar conexión del servidor de destino), haga clic en **Conectar con el servidor** y, después, escriba los detalles de la manera siguiente:
   
   * **Nombre del servidor**: instancia de servidor de destino
   * **Autenticación**: **Autenticación de SQL Server**. Escriba sus credenciales de inicio de sesión.
   * **Base de datos**: **Master DB (List all databases)** [BD maestra (Enumerar todas las bases de datos)]. Esta opción enumera todas las bases de datos del servidor de destino.
     
     ![Asistente para migración de base de datos SQL: configurar conexión del servidor de destino](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)
9. Haga clic en **Conectar**, seleccione la base de datos de destino a la que desea mover la tabla y, después, haga clic en **Siguiente**. Esto debe finalizar con la ejecución del script generado anteriormente, y debería ver una copia de la tabla que se ha movido recientemente en la base de datos de destino.

## <a name="verification-step"></a>Paso de comprobación

- Consulte y pruebe la tabla copiada recientemente para asegurarse de que los datos estén intactos. Tras la confirmación, puede deshacerse de la tabla a la que ha cambiado el nombre en la sección **Pasos de preparación**. (por ejemplo, &lt;nombre de la tabla&gt;_old).

## <a name="next-steps"></a>Pasos siguientes
[Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)




<!--HONumber=Dec16_HO2-->


