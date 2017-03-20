---
title: "Azure Portal: creación y consulta de instancias únicas de SQL Database | Microsoft Docs"
description: "Aprenda a crear un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y bases de datos en Azure Portal. También aprenderá a consultar de una instancia de Azure SQL Database desde Azure Portal."
keywords: "tutorial de base de datos SQL, creación de una base de datos SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 248885d041378301c5202263d24fdc9609e293d6
ms.lasthandoff: 03/15/2017


---
# <a name="create-and-query-a-single-azure-sql-database-in-the-azure-portal"></a>Creación y consulta de instancias únicas de Azure SQL Database en Azure Portal

Las instancias de Azure SQL Database pueden crearse a través de Azure Portal. Este método proporciona una interfaz de usuario basada en el explorador para crear y configurar instancias de Azure SQL Database y todos los recursos de Azure asociados.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en el portal de Azure

Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Creación de una Base de datos SQL

Las instancias de Azure SQL Database se crean en un grupo de recursos de Azure, en un servidor lógico, y con un conjunto definido de recursos de proceso y almacenamiento. Siga estos pasos para crear una instancia de SQL Database que contenga los datos de ejemplo de Adventure Works LT. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. En la hoja **Nuevo**, seleccione **Bases de datos** y, desde la hoja **Bases de datos**, en **SQL Database**.

3. Rellene el formulario de SQL Database con la información obligatoria: 
   - Nombre de la base de datos: escriba un nombre para la base de datos
   - Suscripción: seleccione la suscripción
   - Grupo de recursos: seleccione uno nuevo o uno existente
   - Origen: seleccione **Sample (AdventureWorksLT)** [Ejemplo (AdventureWorksLT)]
   - Servidor: cree un nuevo servidor (el nombre del **Servidor** debe ser único)
   - Grupo elástico: seleccione **Ahora no** para este inicio rápido
   - Plan de tarifa: seleccione **20 DTU** y **250** GB de almacenamiento
   - Intercalación: no se puede cambiar este valor al importar la base de datos de ejemplo 
   - Anclar al panel: seleccione la casilla

      ![crear base de datos](./media/sql-database-get-started/create-database-s1.png)

4. Cuando haya terminado, haga clic en **Crear**. El aprovisionamiento tarda unos minutos.
5. Una vez que haya terminado la implementación de la instancia de **SQL Database**, seleccione **SQL Databases** en el panel o en el menú izquierdo y haga clic en la nueva base de datos en la página **SQL Databases**. Se abre una página de información general de la base de datos, que muestra el nombre completo del servidor (por ejemplo, **mynewserver20170313.database.windows.net**) y proporciona opciones para otras configuraciones.

      ![new-sql database](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="create-a-server-level-firewall-rule"></a>Crear una regla de firewall de nivel de servidor

El servicio SQL Database crea un firewall que impide que aplicaciones y herramientas externas se conecten a la base de datos y al servidor nuevos. Siga estos pasos para crear una regla de firewall de nivel de servidor de SQL Database para que la dirección IP habilite la conectividad externa a través de este. 

1. Haga clic en **Set server firewall** (Configurar firewall de servidor) en la barra de herramientas para la base de datos. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

      ![regla de firewall del servidor](./media/sql-database-get-started/server-firewall-rule.png) 

2. Haga clic en **Agregar IP de cliente** en la barra de herramientas y haga clic en **Guardar**. Se creará una regla de firewall de nivel de servidor para la dirección IP actual.

3. Haga clic en **Aceptar** y en la **X** para cerrar la página de configuración de firewall.

Ahora puede conectarse a la base de datos y a su servidor mediante SQL Server Management Studio u otra herramienta que elija.

## <a name="query-the-sql-database"></a>Consulta a SQL Database

Siga estos pasos para consultar la base de datos con el editor de consultas en Azure Portal. 

1. En la barra de herramientas de la página de SQL Database de la base de datos, haga clic en **Herramientas**. Se abrirá la página **Editor de consultas** (versión preliminar).

     ![menú herramientas](./media/sql-database-get-started/tools-menu.png) 

2. Haga clic en **Editor de consultas (versión preliminar)**, en la casilla de verificación **Términos de vista previa** y en **Aceptar**. Se abrirá el editor de consultas.

3. Haga clic en **Inicio de sesión** y, cuando se le pida, seleccione **autenticación de servidor SQL Server** y proporcione el inicio de sesión y la contraseña del administrador del servidor.
4. Haga clic en **Aceptar** para iniciar sesión.

5. Una vez autenticado, escriba la consulta en la ventana de consulta, por ejemplo:

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Haga clic en **Ejecución** y revise los resultados de la consulta en el panel **Resultados**.

    ![resultados del editor de consultas](./media/sql-database-get-started/query-editor-results.png)

7. Haga clic en la **X** para cerrar la página del Editor de consultas.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede guardar los recursos creados en esta guía de inicio rápido para tutoriales posteriores o usar los siguientes pasos para eliminarlos en Azure Portal.

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en **myResourceGroup**. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba **myResourceGroup** en el cuadro de texto y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- Para conectarse y consultar mediante SQL Server Management Studio, consulte el artículo de [Conexión y consultas con SSMS](sql-database-connect-query-ssms.md).
- Para conectarse con Visual Studio, consulte el artículo de [Conexión y consultas con Visual Studio](sql-database-connect-query.md).
- Para información técnica general de SQL Database, consulte el artículo [Acerca del servicio SQL Database](sql-database-technical-overview.md).

