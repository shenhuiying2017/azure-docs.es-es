---
title: "Diseño de la primera base de datos de Azure SQL Database | Microsoft Docs"
description: "Obtenga información sobre cómo crear su primera base de datos de Azure SQL Database."
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/20/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 8af9ea0a76b9a0606284505195ee3f52b1964604
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>Diseño de su primera base de datos de Azure SQL

Azure SQL Database es una base de datos como servicio (DBaaS) relacional en Microsoft Cloud ("Azure"). En este tutorial, aprenderá a usar Azure Portal y [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) para: 

> [!div class="checklist"]
> * Crear una base de datos en Azure Portal
> * Establecer una regla de firewall de nivel de servidor en Azure Portal
> * Conéctese a la base de datos con SSMS
> * Crear tablas con SSMS
> * Carga masiva de datos con BCP
> * Consultar los datos con SSMS
> * Restaurar la base de datos a una [restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore) anterior en Azure Portal

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar el tutorial, asegúrese de tener instalada la última versión de [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS). 

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en el portal de Azure

Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database-in-the-azure-portal"></a>Crear una base de datos SQL en blanco en Azure Portal

Se crea una base de datos SQL de Azure con un conjunto definido de [recursos de proceso y almacenamiento](sql-database-service-tiers.md). La base de datos se crea dentro de un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) y en un [servidor lógico de Azure SQL Database](sql-database-features.md). 

Siga estos pasos para crear una instancia en blanco de SQL Database. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **SQL Database**. 

   ![crear una base de datos en blanco](./media/sql-database-design-first-database/create-empty-database.png)

3. Rellene el formulario de SQL Database con la siguiente información, como se muestra en la imagen anterior:   

   | Configuración       | Valor sugerido | Descripción | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nombre de la base de datos** | mySampleDatabase | Para conocer los nombres de base de datos válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos). | 
   | **Suscripción** | Su suscripción  | Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions). |
   | **Grupos de recursos** | myResourceGroup | Para conocer cuáles son los nombres de grupo de recursos válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). |
   | **Seleccionar origen** | Base de datos en blanco | Especifica que se debe crear una base de datos en blanco. |

4. Haga clic en **Servidor** para crear y configurar un servidor nuevo para la nueva base de datos. Rellene el **formulario de servidor nuevo** con la siguiente información: 

   | Configuración       | Valor sugerido | Descripción | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nombre del servidor** | Cualquier nombre globalmente único | Para conocer cuáles son los nombres de servidor válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). | 
   | **Inicio de sesión del administrador del servidor** | Cualquier nombre válido | Para conocer los nombres de inicio de sesión válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos).|
   | **Password** | Cualquier contraseña válida | La contraseña debe tener un mínimo de 8 caracteres y debe contener caracteres de tres de las siguientes categorías: caracteres en mayúsculas, caracteres en minúsculas, números y caracteres no alfanuméricos. |
   | **Ubicación** | Cualquier ubicación válida | Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/). |

   ![create database-server](./media//sql-database-design-first-database/create-database-server.png)

5. Haga clic en **Seleccionar**.

6. Haga clic en **Plan de tarifa** para especificar el tanto el nivel de rendimiento como el nivel de servicio de la nueva base de datos. Para este tutorial, seleccione **20 DTU** y **250** GB de almacenamiento.

   ![create database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Haga clic en **Apply**.  

8. Seleccione una **intercalación** para la base de datos en blanco (para este tutorial, use el valor predeterminado). Para más información sobre las intercalaciones, vea [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations) (Intercalaciones)

9. Haga clic en **Create** (Crear) para realizar el aprovisionamiento de la base de datos. El aprovisionamiento tarda un minuto y medio aproximadamente en completarse. 

10. En la barra de herramientas, haga clic en **Notificaciones** para supervisar el proceso de implementación.

   ![notificación](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creación de una regla de firewall de nivel a servidor en Azure Portal

El servicio SQL Database crea un firewall en el nivel de servidor, lo que impide que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos del servidor, a menos que se cree una regla de firewall para abrir el firewall para direcciones IP concretas. Siga estos pasos para crear una [regla de firewall de nivel de servidor de SQL Database](sql-database-firewall-configure.md) para la dirección IP de su cliente y habilite la conectividad externa a través de dicho firewall solo para su dirección IP. 

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. En ese caso, no puede conectarse al servidor de Azure SQL Database, salvo que el departamento de TI abra el puerto 1433.
>

1. Cuando se haya finalizado la implementación, haga clic en **Bases de datos SQL** en el menú de la izquierda y, después, haga clic en **mySampleDatabase** en la página **Bases de datos SQL**. Se abre la página de información general de la base de datos, que muestra el nombre completo del servidor (por ejemplo, **mynewserver20170313.database.windows.net**) y proporciona opciones para configurarlo aún más. Copie dicho nombre, ya que lo tendrá que usar más adelante,

   > [!IMPORTANT]
   > lo necesitará para conectarse a su servidor y a sus bases de datos en los inicios rápidos posteriores.
   > 

   ![nombre del servidor](./media/sql-database-get-started-portal/server-name.png) 

2. Haga clic en **Establecer el firewall del servidor** en la barra de herramientas, como se muestra en la imagen anterior. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

   ![regla de firewall del servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 


3. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP actual a la nueva regla de firewall. La regla de firewall puede abrir el puerto 1433 para una única dirección IP o un intervalo de direcciones IP.

4. Haga clic en **Guardar**. Se crea una regla de firewall de nivel de servidor para el puerto 1433 de la dirección IP actual en el servidor lógico.

   ![establecer regla de firewall del servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Haga clic en **Aceptar** y después cierre la página **Configuración de firewall**.

Ahora puede conectarse al servidor de SQL Database y a sus bases de datos mediante SQL Server Management Studio o cualquier otra herramienta que elija desde esta dirección IP usando la cuenta de administrador del servidor creada con anterioridad.

> [!IMPORTANT]
> De forma predeterminada, el acceso a través del firewall de SQL Database está habilitado para todos los servicios de Azure. Haga clic en **OFF** en esta página para deshabilitar todos los servicios de Azure.

## <a name="get-connection-information-in-the-azure-portal"></a>Obtener información de conexión en Azure Portal

Obtenga el nombre completo del servidor de Azure SQL Database en Azure Portal. Utilice el nombre completo del servidor para conectarse al servidor mediante SQL Server Management Studio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En el panel **Essentials** de la página de Azure Portal de la base de datos, busque y copie el **nombre del servidor**.

   ![información sobre la conexión](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Conéctese a la base de datos con SSMS

Use [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) para establecer una conexión con un servidor de Azure SQL Database.

1. Abra SQL Server Management Studio.

2. En el cuadro de diálogo **Conectar con el servidor**, especifique la siguiente información:

   | Configuración       | Valor sugerido | Descripción | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo de servidor | Motor de base de datos | Este valor es obligatorio |
   | Nombre de servidor | Nombre completo del servidor | Dicho nombre debe parecerse al siguiente: **mynewserver20170313.database.windows.net**. |
   | Autenticación | Autenticación de SQL Server | Autenticación de SQL es el único tipo de autenticación que hemos configurado en este tutorial. |
   | Inicio de sesión | La cuenta de administrador del servidor | Es la cuenta que especificó cuando creó el servidor. |
   | Password | La contraseña de la cuenta de administrador del servidor | Es la contraseña que especificó cuando creó el servidor. |

   ![conectar con el servidor](./media/sql-database-connect-query-ssms/connect.png)

3. Haga clic en **Opciones** en el cuadro de diálogo **Conectar con el servidor**. En la sección **Conectar con base de datos**, escriba **mySampleDatabase** para conectarse a esta base de datos.

   ![conectar a base de datos en el servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Haga clic en **Conectar**. Se abre la ventana del Explorador de objetos en SSMS. 

5. En el Explorador de objetos, expanda **Bases de datos** y, después, expanda **mySampleDatabase** para ver los objetos de la base de datos de ejemplo.

   ![Objetos de base de datos](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database-with-ssms"></a>Crear tablas en la base de datos con SSMS 

Cree un esquema de base de datos con cuatro tablas que modelan un sistema de administración de estudiantes para universidades con [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Persona
- Curso
- Estudiante
- Crédito que modela un sistema de administración de estudiantes para universidades

En el diagrama siguiente se muestra cómo estas tablas se relacionan entre sí. Algunas de estas tablas hacen referencia a columnas de otras tablas. Por ejemplo, la tabla Student hace referencia a la columna **PersonId** de la tabla **Person**. Estudie el diagrama para entender cómo se relacionan las tablas de este tutorial entre sí. Para obtener información detallada sobre cómo crear tablas de base de datos eficaces, vea [Creación de tablas de base de datos eficaces](https://msdn.microsoft.com/library/cc505842.aspx). Para obtener información sobre cómo elegir tipos de datos, vea [Data types](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) (Tipos de datos).

> [!NOTE]
> También puede usar el [Diseñador de tablas de SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx) para crear y diseñar las tablas. 

![Relaciones de tablas](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. En el Explorador de objetos, haga clic con el botón derecho en **mySampleDatabase** y luego en **Nueva consulta**. Se abre una ventana de consulta en blanco que está conectada a la base de datos.

2. En la ventana de consulta, ejecute la consulta siguiente para crear cuatro tablas en la base de datos: 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![Cree las tablas.](./media/sql-database-design-first-database/create-tables.png)

3. Expanda el nodo "tablas" en el Explorador de objetos de SQL Server Management Studio para ver las tablas que ha creado.

   ![crear tablas en SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables-with-ssms"></a>Cargar datos en las tablas con SSMS

1. Cree una carpeta denominada **SampleTableData** en la carpeta Descargas para almacenar datos de ejemplo para la base de datos. 

2. Haga clic con el botón derecho en los vínculos siguientes y guárdelos en la carpeta **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Abra una ventana del símbolo del sistema y navegue hasta la carpeta SampleTableData.

4. Ejecute los comandos siguientes para insertar datos de ejemplo en las tablas y reemplace los valores de **ServerName**, **DatabaseName**, **UserName** y **Password** por los valores correspondientes al entorno.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

De esta forma, ya ha cargado los datos de ejemplo en las tablas que ha creado anteriormente.

## <a name="query-the-tables-with-ssms"></a>Consultar las tablas con SSMS

Ejecute las siguientes consultas para recuperar información de las tablas de base de datos. Vea [Writing SQL Queries](https://technet.microsoft.com/library/bb264565.aspx) (Escribir consultas SQL) para más información sobre cómo escribir consultas SQL. La primera consulta combina las cuatro tablas para buscar todos los alumnos del profesor "Dominick Pope" que tienen una calificación superior al 75% de su clase. La segunda consulta combina las cuatro tablas y busca todos los cursos en los que nunca se ha matriculado "Noe Coleman".

1. En una ventana de consulta de SQL Server Management Studio, ejecute la siguiente consulta:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. En una ventana de consulta de SQL Server Management Studio, ejecute la siguiente consulta:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-portal"></a>Restaurar una base de datos a un momento anterior mediante Azure Portal

Imagine que ha eliminado accidentalmente una tabla. No se puede recuperar con facilidad. Azure SQL Database permite volver a cualquier momento dado en el período de los últimos 35 días y restaurar este punto en una nueva base de datos. Puede usar esta base de datos para recuperar los datos eliminados. Los pasos siguientes restauran la base de datos de ejemplo a un punto anterior a la eliminación de las tablas.

1. En la página SQL Database de la base de datos, haga clic en **Restaurar** en la barra de herramientas. Se abre la página de **restauración**.

   ![Restauración](./media/sql-database-design-first-database/restore.png)

2. Rellene el formulario de **restauración** con la información necesaria:
    * Nombre de la base de datos: escriba un nombre para la base de datos 
    * Momento dado: seleccione la pestaña **Momento dado** en el formulario de restauración 
    * Punto de restauración: seleccione una hora antes de que se modificara la base de datos
    * Servidor de destino: no puede modificar este valor cuando restaura una base de datos 
    * Grupo de bases de datos elásticas: seleccione **Ninguno**  
    * Plan de tarifa: seleccione **20 DTU** y **250 GB** de almacenamiento

   ![punto de restauración](./media/sql-database-design-first-database/restore-point.png)

3. Haga clic en **Aceptar** para restaurar la base de datos a [un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore) antes de que se agregaran las tablas. Restaurar una base de datos a un momento dado distinto crea una base de datos duplicada en el mismo servidor que la base de datos original a partir del momento dado que especifique, siempre que se encuentre dentro del período de retención para el [nivel de servicio](sql-database-service-tiers.md).

## <a name="next-steps"></a>Pasos siguientes 
En este tutorial ha aprendido las tareas básicas de una base de datos, como crear una base de datos y tablas, cargar y consultar datos, y restaurar la base de datos a un momento anterior en el tiempo. Ha aprendido a:
> [!div class="checklist"]
> * Crear una base de datos
> * Configurar una regla de firewall
> * Conectarse a la base de datos con [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Cree las tablas.
> * Realizar cargas masivas de datos
> * Consultar los datos
> * Restaurar la base de datos a un momento anterior en el tiempo mediante las funcionalidades de [restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore) de SQL Database. Para aprender a migrar datos, diríjase al siguiente tutorial.

> [!div class="nextstepaction"]
>[Migración de una base de datos SQL Server a Azure SQL Database](sql-database-migrate-your-sql-server-database.md)

