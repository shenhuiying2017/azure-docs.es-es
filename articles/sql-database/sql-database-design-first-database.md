---
title: "Diseño de la primera base de datos de Azure SQL Database | Microsoft Docs"
description: "Obtenga información sobre cómo crear su primera base de datos de Azure SQL Database."
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/23/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 313bcf4fbc0ab7f251dd62b1e2151afef8392a55
ms.lasthandoff: 03/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>Diseño de su primera base de datos de Azure SQL

En este tutorial usará Azure Portal para crear una base de datos en un servidor nuevo con un firewall de nivel de servidor. Luego, usará SQL Server Management Studio para crear una tabla, cargar datos en esa tabla, consultar la tabla y agregarle un índice. Por último, usará las copias de seguridad automatizadas del servicio de SQL Database para restaurar la base de datos a un momento dado anterior antes de agregar esta tabla nueva.

Para completar el tutorial, asegúrese de tener instalada la versión más reciente de [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 

## <a name="step-1---log-in-to-the-azure-portal"></a>Paso 1: Iniciar sesión en Azure Portal

Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

## <a name="step-2---create-a-sql-database"></a>Paso 2: Crear una base de datos SQL Database

Se crea una base de datos SQL de Azure con un conjunto definido de [recursos de proceso y almacenamiento](sql-database-service-tiers.md). La base de datos se crea dentro de un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) y en un [servidor lógico de Azure SQL Database](sql-database-features.md). 

Siga estos pasos para crear una instancia de SQL Database que contenga los datos de ejemplo de Adventure Works LT. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **SQL Database**.

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

## <a name="step-3---create-a-server-level-firewall-rule"></a>Paso 3: Crear una regla de firewall de nivel de servidor

El servicio SQL Database crea un firewall que impide que aplicaciones y herramientas externas se conecten a la base de datos y al servidor. Siga estos pasos para crear una [regla de firewall de nivel de servidor de SQL Database](sql-database-firewall-configure.md) para que la dirección IP habilite la conectividad externa a través de este. 

1. Haga clic en **Set server firewall** (Configurar firewall de servidor) en la barra de herramientas para la base de datos. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

      ![regla de firewall del servidor](./media/sql-database-get-started/server-firewall-rule.png) 

2. Haga clic en **Agregar IP de cliente** en la barra de herramientas y haga clic en **Guardar**. Se creará una regla de firewall de nivel de servidor para la dirección IP actual.

3. Haga clic en **Aceptar** y en la **X** para cerrar la página de configuración de firewall.

Ahora puede conectarse a la base de datos y a su servidor mediante SQL Server Management Studio u otra herramienta que elija.

## <a name="step-4---get-connection-information"></a>Paso 4: Obtener información de la conexión

Obtenga el nombre completo del servidor de Azure SQL Database en Azure Portal. Utilice el nombre completo del servidor para conectarse al servidor mediante SQL Server Management Studio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En el panel **Essentials** de la página de Azure Portal de la base de datos, busque y copie el **nombre del servidor**.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="step-5---connect-to-the-server-using-ssms"></a>Paso 5: Conectarse al servidor con SSMS

Use SQL Server Management Studio para establecer una conexión con un servidor de Azure SQL Database.

1. Escriba **SSMS** en el cuadro de búsqueda de Windows y, después, haga clic en **Entrar** para abrir SSMS.

2. En el cuadro de diálogo **Conectar con el servidor**, especifique la siguiente información:
   - **Tipo de servidor**: especifique el motor de base de datos
   - **Nombre de servidor**: escriba el nombre completo del servidor, como **mynewserver20170313.database.windows.net**
   - **Autenticación**: especifique la autenticación de SQL Server
   - **Inicio de sesión**: escriba la cuenta de administrador del servidor
   - **Contraseña**: escriba la contraseña de la cuenta de administrador del servidor
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Haga clic en **Conectar**. Se abre la ventana del Explorador de objetos en SSMS. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. En el Explorador de objetos, expanda **Bases de datos** y, después, expanda **mySampleDatabase** para ver los objetos de la base de datos de ejemplo.

## <a name="step-6---create-and-query-a-table"></a>Paso 6: Crear y consultar una tabla 
1. En el Explorador de objetos, haga clic con el botón derecho en **mySampleDatabase** y luego en **Nueva consulta**. Se abre una ventana de consulta en blanco que está conectada a la base de datos.
2. Ejecute la consulta siguiente en la ventana de consulta:

   ```sql 
   CREATE TABLE [dbo].[Students]
   (
     [student_id] int, 
     [name] varchar(100),
     [age] int,
     [email] varchar(100),
     [AddressID] int REFERENCES [SalesLT].[Address] (AddressID)
   );
   ```

   Una vez que se complete la consulta, habrá creado una tabla vacía llamada Estudiantes en la base de datos.

3. Ejecute la consulta siguiente en la ventana de consulta de SSMS: 

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   ```

   La tabla Estudiantes no devuelve ningún dato.

## <a name="step-7---load-data-into-the-table"></a>Paso 7: Cargar datos en la tabla 
1. Abra una ventana de símbolo del sistema.

2. Ejecute el comando de PowerShell siguiente para descargar un archivo de texto de ejemplo en el directorio actual.

   ```powershell
   powershell -command "& { (New-Object Net.WebClient).DownloadFile('https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData.txt', 'SampleStudentData.txt'); echo 'Download complete' }" 
   ``` 

3. Cuando se completa la descarga, ejecute el comando siguiente para insertar 1000 filas en la tabla Estudiantes y reemplace los valores de **ServerName**, **DatabaseName**, **UserName** y **Password** por los valores correspondientes al entorno.

   ```bcp
   bcp Students in SampleStudentData.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

De este modo, cargó los datos de ejemplo en la tabla que creó anteriormente.

## <a name="step-8---add-an-index-to-a-table"></a>Paso 8: Agregar un índice a una tabla
Para que la búsqueda de valores específicos en la tabla sea más eficaz, cree un índice en la tabla Estudiantes. Un índice organiza los datos de manera que ahora se deben analizar todos los datos para encontrar un valor específico.

1. Ejecute la consulta siguiente en la ventana de consulta de SSMS:

   ```sql 
   CREATE NONCLUSTERED INDEX IX_Age ON Students (age);
   ```

2. Ejecute la consulta siguiente en la ventana de consulta de SSMS:

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   WHERE age > 20
   ```

   Esta consulta devuelve el nombre, la edad y el correo electrónico de los estudiantes mayores de 20 años.

## <a name="step-9---restore-a-database-to-a-point-in-time"></a>Paso 9: Restaurar una base de datos a un momento dado 
Las bases de datos de Azure tienen [copias de seguridad continuas](sql-database-automated-backups.md) que se crean automáticamente cada 5 a 10 minutos. Estas copias de seguridad le permiten restaurar la base de datos a un momento dado anterior. Restaurar una base de datos a un momento dado distinto crea una base de datos duplicada en el mismo servidor que la base de datos original a partir del momento dado que especifique (dentro del período de retención para el nivel de servicio). Los pasos siguientes restauran la base de datos de ejemplo a un punto antes de que se agregara la tabla **Estudiantes**. 

1. En la página SQL Database de la base de datos, haga clic en **Restaurar** en la barra de herramientas. Se abre la página de **restauración**.

    <img src="./media/sql-database-design-first-database/restore.png" alt="restore" style="width: 780px;" />

2. Rellene el formulario de **restauración** con la información necesaria:
    * Nombre de la base de datos: escriba un nombre para la base de datos 
    * Momento dado: seleccione la pestaña **Momento dado** en el formulario de restauración 
    * Punto de restauración: seleccione una hora antes de que se modificara la base de datos
    * Servidor de destino: no puede modificar este valor cuando restaura una base de datos 
    * Grupo de bases de datos elásticas: seleccione **Ninguno**  
    * Plan de tarifa: seleccione **20 DTU** y **250 GB** de almacenamiento

    <img src="./media/sql-database-design-first-database/restore-point.png" alt="restore-point" style="width: 780px;" />

3. Haga clic en **Aceptar** para restaurar la base de datos a un momento dado antes de que se agregara la tabla *Estudiantes*.

## <a name="next-steps"></a>Pasos siguientes 
Para ejemplos de tareas comunes de PowerShell, consulte los [ejemplos de PowerShell de SQL Database](sql-database-powershell-samples.md)

