<properties urlDisplayName="How to create and provision" pageTitle="Introducción a Base de datos SQL de Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="jeffryg"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="jeffreyg" />


#Introducción a Base de datos SQL de Microsoft Azure

En este tutorial aprenderá los fundamentos de la administración de Base de datos SQL de Microsoft Azure mediante el Portal de administración de Azure. Si no tiene experiencia en la administración de bases de datos, puede seguir estas lecciones para aprender lo básico en aproximadamente 30 minutos. 

En este tutorial se supone que no tiene una experiencia previa en SQL Server o en Base de datos SQL de Azure. Una vez completado este tutorial, dispondrá de una base de datos de ejemplo en Azure y sabrá cómo realizar tareas de administración básicas mediante el Portal de administración.

Creará y realizará el aprovisionamiento de una base de datos de ejemplo en Azure, un sistema de consultas y datos de usuario mediante Excel.


##Tabla de contenido##

* [Paso 1: Crear una cuenta de Microsoft Azure](#Subscribe)
* [Paso 2: Conectarse a Azure y crear una base de datos](#Subscribe)
* [Paso 3: Configurar el firewall](#ConfigFirewall)
* [Paso 4: Agregar datos y un esquema mediante el script Transact-SQL](#AddData)
* [Paso 5: Crear el esquema](#createschema)
* [Paso 6: Insertar datos](#insertData)
* [Paso 7: Ejemplo de consulta y datos del sistema en el Portal de administración para Base de datos SQL](#QueryDBSysData)
* [Paso 8: Crear un inicio de sesión de base de datos y asignar permisos](#DBLogin)
* [Paso 9: Conectar desde una aplicación](#ClientConnection)


<h2 id="Subscribe">Paso 1: Crear una cuenta de Microsoft Azure</h2>

1. Abra un explorador web y diríjase a [http://azure.microsoft.com](http://azure.microsoft.com).
Para comenzar con una cuenta gratuita, haga clic en Evaluación gratuita en la esquina superior derecha y siga los pasos.

2. Se ha creado su cuenta. Ya está listo para comenzar.


<h2 id="Connect">Paso 2: Conectarse a Azure y crear una base de datos</h2>


1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com). Debe ver un panel de navegación con el siguiente aspecto:

	![Navigation pane][Image1]

2. Haga clic en **Nuevo** en la parte inferior de la página. Cuando haga clic en **Nuevo**, aparecerá una lista en la pantalla que le mostrará los elementos que puede crear.

3. Haga clic en **Base de datos SQL** y, a continuación, en **Creación personalizada**. 

	![Navigation pane][Image2]

Si selecciona esta opción, puede crear un nuevo servidor y una base de datos SQL a la vez con usted como administrador. Como administrador del sistema, puede realizar más tareas, incluida la conexión al Portal de administración para Base de datos SQL, que realizará más tarde en este tutorial.  

4.  Aparece la página Configuración de base de datos cuando haga clic en **Creación personalizada**. En esa página, proporcione información básica que crea una base de datos SQL vacía en el servidor. La adición de tablas y datos se tratará en un paso posterior. 

    Complete la página Database Settings de la siguiente forma:

	![Navigation pane][Image3]

* Especifique **School** como nombre de la base de datos. 

* Use la configuración predeterminada para edición, tamaño máximo e intercalación. 

* Seleccione **Nuevo servidor de Base de datos SQL**. Si selecciona un nuevo servidor, se agrega una segunda página que usaremos para configurar la cuenta de administración y la región. 

* Una vez que se haya completado esta tarea, haga clic en la flecha para ir a la siguiente página.


7. Rellene los campos de Server Settings de la siguiente forma: 

	![Navigation pane][Image4]

* Especifique un nombre de administrador con una sola palabra sin espacios. Base de datos SQL usa la autenticación SQL a través de una conexión cifrada para validar la identidad de usuario. Se creará un inicio de sesión de autenticación de SQL Server con permisos de administrador con el nombre que proporcione. El nombre de administrador no puede ser un usuario de Windows ni debe ser un nombre de usuario Live ID. La autenticación de Windows no es compatible con Base de datos SQL.

* Proporcione una contraseña segura con más de ocho caracteres que contenga una combinación de valores en mayúsculas y minúsculas, y un número o símbolo. Utilice la burbuja de ayuda para obtener detalles de la complejidad de la contraseña.

* Seleccione una región. La región determina la ubicación geográfica del servidor. Las regiones no pueden cambiarse fácilmente, por lo que debe seleccionar la apropiada para el servidor. Seleccione la ubicación que le sea más próxima. Si mantiene la base de datos y la aplicación de Azure en la misma región, evita que se produzcan costes relacionados con el ancho de banda y la latencia de datos.

* Asegúrese de que la casilla **Permitir que los servicios de Azure accedan a este servidor**esté activada para poder conectarse a la base de datos mediante el Portal de administración para Base de datos SQL, Excel en Office 365 o Azure SQL Reporting.

* Haga clic en la marca de verificación en la parte inferior de la página cuando haya finalizado.

Tenga en cuenta que no especificó un nombre de servidor. Puesto que el servidor de Base de datos SQL debe ser accesible en todo el mundo, Base de datos SQL configura las entradas DNS apropiadas cuando se crea el servidor. El nombre generado garantiza que no existen colisiones de nombres con otras entradas DNS. No puede cambiar el nombre del servidor de Base de datos SQL.

Para ver el nombre del servidor que hospeda la base de datos **School** que acaba de crear, haga clic en **Bases de datos SQL** en el panel de navegación izquierdo y después haga clic en la base de datos **School** en la vista de lista de **Bases de datos SQL**. En la página **Inicio rápido**, desplácese hacia abajo para ver el nombre del servidor.

En el próximo paso, configurará el firewall de manera que las conexiones de las aplicaciones que se ejecuten en su equipo puedan obtener acceso a las bases de datos en el servidor de Base de datos SQL.



<h2 id="ConfigFirewall">Paso 3: Configurar el firewall</h2>

Para configurar el firewall para que se permitan las conexiones, especifique la información en la página del servidor.

**Nota:** el servicio de Base de datos SQL solo está disponible con el puerto TCP 1433 que usa el protocolo TDS. Por lo tanto, asegúrese de que el firewall de la red y el equipo local permitan la comunicación TCP de salida en el puerto 1433. Para obtener más información, consulte [Firewall de Base de datos SQL](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx).


1. En el panel de navegación de la izquierda, haga clic en **Bases de datos SQL**.

2. Haga clic en **Servidores** en la parte superior de la página. A continuación, haga clic en el servidor que acaba de crear para abrir la página del servidor.

3. En la página del servidor, haga clic en **Configurar** para abrir la configuración de **Direcciones IP permitidas** y, a continuación, haga clic en el vínculo **Agregar a las direcciones IP permitidas**. Esto creará una nueva regla de cortafuegos para permitir las solicitudes de conexión desde el enrutador o desde el servidor proxy en el que se escucha su dispositivo.

4. Puede crear reglas de cortafuegos adicionales si especifica el nombre de una regla, junto con sus valores de intervalo IP de inicio y fin.

5. Para habilitar las interacciones entre este servidor y otros servicios de Azure, haga clic en **Sí** a la opción **Servicios de Microsoft Azure**. 

7. Para guardar los cambios, haga clic en **GUARDAR** en la parte inferior de la página.

6. Una vez que haya guardado la regla, la página tendrá un aspecto similar al que se muestra en la siguiente captura de pantalla.

	![Navigation pane][Image7]

Ahora dispone de un servidor de Base de datos SQL en Azure, una regla de firewall que permite el acceso al servidor, un objeto de base de datos y un inicio de sesión de administrador. Pero todavía no tiene una base de datos operativa en la que pueda realizar consultas. Para ello su base de datos necesita tener un esquema y datos reales.

Esta tutorial utiliza únicamente las herramientas disponibles, por ello usará la ventana de consulta del Portal de administración para Base de datos SQL para ejecutar un script Transact-SQL que crea una base de datos predefinida.

A medida que aumenten sus conocimientos, querrá explorar formas adicionales de crear una base de datos, incluidos los enfoques programáticos o la superficie de diseño de SQL Server Data Tools. Si ya dispone de una base de datos de SQL Server que se ejecuta en un servidor local, puede migrar fácilmente la base de datos al servidor de Azure que desea configurar. Use los vínculos al final de este tutorial para descubrir cómo. 



<h2 id="AddData">Paso 4: Agregar datos y un esquema mediante el script Transact-SQL</h2>

En este paso, ejecutará dos scripts. El primero crea un esquema que define tablas, columnas y relaciones. El segundo script agrega los datos. Cada paso se realiza de forma independiente en una conexión independiente. Si ha integrado bases de datos en SQL Server antes, una de las diferencias que notará en Base de datos SQL es que los comandos CREATE e INSERT deben ejecutarse en lotes separados. Base de datos SQL impone este requisito para reducir los ataques contra los datos cuando se encuentran en tránsito. 

**Nota:** los valores de datos y el esquema proceden de este [artículo de MSDN](http://msdn.microsoft.com/en-us/library/windowsazure/ee621790.aspx "MSDN article") y se han modificado para que funcionen con Base de datos SQL.

1. Vaya a la página principal. En el [Portal de administración](http://manage.windowsazure.com), aparece la base de datos **School** en la lista de elementos de la página principal.

	![Navigation pane][Image8]

2. Haga clic en **School** para seleccionarla y, a continuación, haga clic en **Administrar** en la parte inferior de la página. Se abrirá el Portal de administración para Base de datos SQL. Este portal es independiente del Portal de administración de Azure. Usará este portal para ejecutar los comandos Transact-SQL y las consultas.

3. Especifique el nombre y la contraseña de inicio de sesión en la base de datos **School**. Este es el inicio de sesión de administrador que especificó cuando creó el servidor.

4. Haga clic en **Nueva consulta** en la cinta del portal de administración para Base de datos SQL. Aparece una ventana de consulta vacía en el espacio de trabajo. En el siguiente paso usará esta ventana para copiar una serie de scripts predefinidos que agregarán la estructura y los datos a la base de datos vacía.



<h2 id="createschema">Paso 5: Crear el esquema</h2>

En este paso, creará el esquema mediante el siguiente script. El script comprueba primero si existe una tabla con el mismo nombre para asegurarse de que no habrá una colisión de nombre sy crea la tabla mediante la instrucción [CREATE TABLE](http://msdn.microsoft.com/en-us/library/windowsazure/ee336258.aspx) . Además, este script usa la instrucción [ALTER TABLE](http://msdn.microsoft.com/en-us/library/windowsazure/ee336286.aspx) para especificar la clave principal y las relaciones de tabla.

Copie el script y péguelo en la ventana de consulta. Haga clic en **Ejecutar** en la parte superior de la ventana para ejecutar el script.

<div style="width:auto; height:600px; overflow:auto"><pre>
	-- Create the Department table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
		AND type in (N'U'))
   	BEGIN
  	CREATE TABLE [dbo].[Department](
		[DepartmentID] [int] NOT NULL,
		[Name] [nvarchar](50) NOT NULL,
		[Budget] [money] NOT NULL,
		[StartDate] [datetime] NOT NULL,
		[Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
	[DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
	GO

	-- Create the Person table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Person](
		[PersonID] [int] IDENTITY(1,1) NOT NULL,
		[LastName] [nvarchar](50) NOT NULL,
		[FirstName] [nvarchar](50) NOT NULL,
		[HireDate] [datetime] NULL,
		[EnrollmentDate] [datetime] NULL,
 	 CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED 	
	(
	[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnsiteCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnsiteCourse](
		[CourseID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Days] [nvarchar](50) NOT NULL,
		[Time] [smalldatetime] NOT NULL,
 	 CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnlineCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnlineCourse](
		[CourseID] [int] NOT NULL,
		[URL] [nvarchar](100) NOT NULL,
 	 CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	--Create the StudentGrade table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[StudentGrade](
		[EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
		[CourseID] [int] NOT NULL,
		[StudentID] [int] NOT NULL,
		[Grade] [decimal](3, 2) NULL,
	 CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
	(
		[EnrollmentID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the CourseInstructor table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[CourseInstructor](
		[CourseID] [int] NOT NULL,
		[PersonID] [int] NOT NULL,
	 CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC,
		[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the Course table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Course](
		[CourseID] [int] NOT NULL,
		[Title] [nvarchar](100) NOT NULL,
		[Credits] [int] NOT NULL,
		[DepartmentID] [int] NOT NULL,
 	 CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Create the OfficeAssignment table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OfficeAssignment](
		[InstructorID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Timestamp] [timestamp] NOT NULL,
 	 CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
	(
		[InstructorID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Define the relationship between OnsiteCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
	ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
	GO

	-- Define the relationship between OnlineCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
	ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
	GO
	-- Define the relationship between StudentGrade and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
	GO

	--Define the relationship between StudentGrade and Student.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))	
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
	GO

	-- Define the relationship between CourseInstructor and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
  	 WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
  	 AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
  	 CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
 	  CONSTRAINT [FK_CourseInstructor_Course];
	GO

	-- Define the relationship between CourseInstructor and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
 	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
	   AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
 	  CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
  	 CONSTRAINT [FK_CourseInstructor_Person];
	GO

	-- Define the relationship between Course and Department.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
	ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
	REFERENCES [dbo].[Department] ([DepartmentID]);
	GO
	ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
	GO

	--Define the relationship between OfficeAssignment and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
 	  AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
	ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
 	  CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[OfficeAssignment] CHECK 
   	 CONSTRAINT [FK_OfficeAssignment_Person];
	GO
</pre></div>



<h2 id="insertData">Paso 6: Insertar datos</h2>

Abra una nueva ventana de consulta y, a continuación, pegue el siguiente script. Ejecute el script para insertar datos. Este script usa la instrucción [INSERT](http://msdn.microsoft.com/en-us/library/windowsazure/ee336284.aspx) para agregar valores a cada columna.

<div style="width:auto; height:600px; overflow:auto"><pre>
	-- Insert data into the Person table.
	SET IDENTITY_INSERT dbo.Person ON;
	GO
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (6, 'Li', 'Yan', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (28, 'White', 'Anthony', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
	GO
	SET IDENTITY_INSERT dbo.Person OFF;
	GO
	
</pre></div>


<h2 id="QueryDBSysData">Paso 7: Ejemplo de consulta y datos del sistema en el Portal de administración para Base de datos SQL</h2>

Para comprobar su trabajo, ejecute una consulta que devuelva los datos que acaba de especificar. Ejecute también procedimientos almacenados integrados y vistas de administración de datos que proporcionen información sobre las bases de datos que se ejecutan en el servidor de Base de datos SQL.

<h4 id="QueryDB">Datos de ejemplo de consultas</h4>

En una nueva ventana de consulta, copie y ejecute el siguiente script Transact-SQL para recuperar algunos de los datos que acaba de agregar.


<div style="width:auto; height:auto; overflow:auto"><pre>
	SELECT * From Person
</pre></div>

Verá un conjunto de resultados con 34 filas de la tabla de personas que incluye PersonID, LastName, FirstName, HireDate y EnrollmentDate.


<h4 id="QuerySys">Datos del sistema de consultas</h4>

También puede usar vistas del sistema y procedimientos almacenados integrados para obtener información del servidor. Para cumplir los objetivos de este tutorial, se probarán algunos comandos.

Ejecute el siguiente comando para conocer qué bases de datos se encuentran disponibles en el servidor. 

	SELECT * FROM sys.databases  

Ejecute este comando para devolver una lista de los usuarios conectados actualmente al servidor.

	SELECT user_name(),suser_sname()

Ejecute este procedimiento almacenado para devolver una lista de todos los objetos en la base de datos **School**.

	EXEC SP_help

No cierre la conexión del portal a la base de datos **School**. La volverá a necesitar de nuevo en unos minutos.



<h2 id="DBLogin">Paso 8: Crear un inicio de sesión de base de datos y asignar permisos</h2>

En Base de datos SQL, puede crear inicios de sesión y conceder permisos mediante Transact-SQL. En esta lección, realizará los siguientes procedimientos mediante Transact-SQL:


1. Crear un inicio de sesión de autenticación de SQL Server
2. Crear un usuario de base de datos y
3. Conceder permisos a través de la pertenencia a roles.

Se usa un inicio de sesión de autenticación de SQL Server para las conexiones de servidor. Todos los usuarios que obtengan acceso a una base de datos en un servidor de Base de datos SQL lo hacen mediante un nombre y una contraseña de inicio de sesión de autenticación de SQL Server. 

Para crear un inicio de sesión, primero debe conectarse a la base de datos **master**.

<h4 id="CreateLogin">Crear un inicio de sesión de autenticación de SQL Server</h4>

1. En el [Portal de administración](http://manage.windowsazure.com), seleccione **Bases de datos SQL**, haga clic en **Servidores**, elija el servidor y, a continuación, haga clic en la flecha blanca para abrir la
página del servidor.

2. En la página Inicio rápido, haga clic en **Administrar servidor** para abrir una nueva conexión al Portal de administración para Base de datos SQL. 

3. Especifique **master** como la base de datos con la cual conectarse y, a continuación, inicie sesión con su nombre de usuario y contraseña. Este es el inicio de sesión de administrador que especificó cuando creó el servidor.

4. Se abrirá el portal de administración de Base de datos SQL en una nueva ventana del explorador y se habrá conectado a **master**.

5. Si se produce un error en la página similar al siguiente, ignórelo. Haga clic en **Nueva consulta** para abrir una ventana de consulta que le permita ejecutar comandos Transact-SQL en la base de datos **master**.

	![Navigation pane][Image15]

6. Copie y pegue el siguiente comando en la ventana de consulta.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7. Ejecute el comando para crear un nuevo inicio de sesión de SQL Server con el nombre 'SQLDBLogin'.


<h4 id="CreateDBuser">Crear un usuario de base de datos y asignar permisos</h4>

Una vez creado un inicio de sesión de autenticación de SQL Server, el siguiente paso es asignar la base de datos y los niveles de permiso asociados al inicio de sesión. Puede hacer esto mediante la creación de un **usuario de base de datos** en cada base de datos.

1. Vuelva a la página del portal de administración de Base de datos SQL que se conecta a la base de datos **School**. Si cerró la ventana del explorador, inicie una nueva conexión con la base de datos **School** mediante los pasos de la lección anterior, "Agregar datos y un esquema mediante el script Transact-SQL". 

	En la página del portal de administración de Base de datos SQL, puede visualizarse el nombre de la base de datos **School** en la esquina superior izquierda.

	![Navigation pane][Image12]

2. Haga clic en **Nueva consulta** para abrir una nueva ventana de consulta y copie la siguiente instrucción. 

	    CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3. Ejecute el script. Este script crea un nuevo usuario de base de datos basado en el inicio de sesión.

   A continuación, asignará permisos con el rol db_datareader. Los usuarios de base de datos asignados a este rol pueden leer todos los datos de todas las tablas de usuario en la base de datos. 

4. Abra una nueva ventana de consulta y, a continuación, especifique y ejecute la siguiente instrucción. Esta instrucción ejecuta un procedimiento almacenado integrado que asigna el rol db_datareader al nuevo usuario que acaba de crear. 

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

Ahora dispone de un inicio de sesión de autenticación de SQL Server que cuenta con permiso de solo lectura en la base de datos **School**. Use estos pasos para crear otros inicios de sesión de autenticación de SQL Server para permitir diferentes niveles de acceso a los datos.


<h2 id="ClientConnection">Paso 9: Conectarse desde una aplicación</h2>

Puede usar ADO.NET para conectarse a Base de datos SQL de Microsoft Azure. A diferencia de una conexión local, necesita considerar las limitaciones u otros errores de servicio que pudieran dar término a una conexión o bloquear conexiones nuevas de manera temporal. Esta condición se denomina "error transitorio". Para administrar los errores transitorios, implemente una estrategia de reintento. Cuando se realiza una conexión a Base de datos SQL de Azure, el [Bloque de aplicación Control de errores transitorios](http://go.microsoft.com/fwlink/?LinkId=519356), parte de Biblioteca empresarial 6, abril de 2013, cuenta con estrategias de detección que identifican una condición de error transitorio.

<h4>Aplicación de consola C# de ejemplo</h4>


	static void Main(string[] args)
    {
        //NOTE: Use appropriate exception handling in a production application.

        //Replace
        //  builder["Server"]: {servername} = Your Azure SQL Database server name
        //  builder["User ID"]: {username}@{servername} = Your Azure SQL Database user name and server name
        //  builder["Password"]: {password} = Your Azure SQL Database password

        System.Data.SqlClient.SqlConnectionStringBuilder builder = new System.Data.SqlClient.SqlConnectionStringBuilder();
        builder["Server"] = "{servername}";
        builder["User ID"] = "{username}@{servername}";
        builder["Password"] = "{password}";

        builder["Database"] = "AdventureWorks2012";
        builder["Trusted_Connection"] = false;
        builder["Integrated Security"] = false;
        builder["Encrypt"] = true;

        //1. Define an Exponential Backoff retry strategy for Azure SQL Database throttling (ExponentialBackoff Class). An exponential back-off strategy will gracefully back off the load on the service.
        int retryCount = 4;
        int minBackoffDelayMilliseconds = 2000;
        int maxBackoffDelayMilliseconds = 8000;
        int deltaBackoffMilliseconds = 2000;

        ExponentialBackoff exponentialBackoffStrategy = 
          new ExponentialBackoff("exponentialBackoffStrategy",
              retryCount,
              TimeSpan.FromMilliseconds(minBackoffDelayMilliseconds), 
              TimeSpan.FromMilliseconds(maxBackoffDelayMilliseconds),
              TimeSpan.FromMilliseconds(deltaBackoffMilliseconds));

        //2. Set a default strategy to Exponential Backoff.
        RetryManager manager = new RetryManager(new List<RetryStrategy>
        {  
            exponentialBackoffStrategy 
        }, "exponentialBackoffStrategy");

        //3. Set a default Retry Manager. A RetryManager provides retry functionality, or if you are using declarative configuration, you can invoke the RetryPolicyFactory.CreateDefault
            RetryManager.SetDefault(manager);

        //4. Define a default SQL Connection retry policy and SQL Command retry policy. A policy provides a retry mechanism for unreliable actions and transient conditions.
        RetryPolicy retryConnectionPolicy = manager.GetDefaultSqlConnectionRetryPolicy();
        RetryPolicy retryCommandPolicy = manager.GetDefaultSqlCommandRetryPolicy();

        //5. Create a function that will retry the connection using a ReliableSqlConnection.
        retryConnectionPolicy.ExecuteAction(() =>
        {
            using (ReliableSqlConnection connection = new ReliableSqlConnection(builder.ConnectionString))
            {
                connection.Open();

                IDbCommand command = connection.CreateCommand();
                command.CommandText = "SELECT Name FROM Production.Product";

                //6. Create a function that will retry the command calling ExecuteCommand() from the ReliableSqlConnection
                retryCommandPolicy.ExecuteAction(() =>
                {
                    using (IDataReader reader = connection.ExecuteCommand<IDataReader>(command))
                    {
                        while (reader.Read())
                        {
                            string name = reader.GetString(0);

                            Console.WriteLine(name);
                        }
                    }
                });                  
            }
        });

        Console.ReadLine();
    }



<h2 id="NextSteps">Pasos siguientes</h2>

Ahora que está familiarizado con Base de datos SQL y los portales de administración, puede probar otras herramientas y técnicas usadas por los administradores de bases de datos SQL Server.

Para administrar activamente su nueva base de datos, considere instalar y usar SQL Server Management Studio. Management Studio es la herramienta de administración de base de datos principal para la administración de bases de datos SQL Server, incluidas las que se ejecutan en Azure. Use Management Studio para guardar consultas y usarlas más adelante, agregar nuevas tablas y procedimientos almacenados y perfeccionar sus conocimientos de Transact-SQL en un entorno de scripting enriquecido que incluya un comprobador de sintaxis, IntelliSense y plantillas. Para comenzar, siga las instrucciones que se describen en [Administración de bases de datos SQL con SQL Server Management Studio](http://www.azure.microsoft.com/en-us/documentation/articles/sql-database-manage-azure-ssms/).

Contar con un buen dominio del lenguaje de definición de datos y de las consultas Transact-SQL es fundamental para los administradores de bases de datos. Si no tiene experiencia en Transact-SQL, comience por el [Tutorial: Escribir instrucciones Transact-SQL](http://msdn.microsoft.com/en-us/library/ms365303.aspx) para adquirir algunos conocimientos básicos.

Existen otros métodos para mover una base de datos local a una Base de datos SQL. Si ya dispone de bases de datos, o si ha descargado bases de datos de ejemplo con las que practicar, pruebe los siguientes enfoques alternativos:

* [Migración de bases de datos a Base de datos SQL](http://msdn.microsoft.com/en-us/library/windowsazure/ee730904.aspx)
* [Copia de bases de datos en Base de datos SQL](http://msdn.microsoft.com/en-us/library/windowsazure/ff951624.aspx)
* [Implementar una Base de datos SQL en una máquina virtual de Azure](http://msdn.microsoft.com/en-us/library/dn195938(v=sql.120).aspx)



[Image1]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
[Image2]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
[Image3]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
[Image4]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
[Image5]: ./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG
[Image6]: ./media/sql-database-get-started/6DBConfigFirewall_SQLTut.PNG
[Image7]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
[Image8]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
[Image9]: ./media/sql-database-get-started/9dblistschool_SQLTut.PNG
[Image10]: ./media/sql-database-get-started/10dbportalmanagebutton_SQLTut.PNG
[Image11]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
[Image12]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
[Image13]: ./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG
[Image14]: ./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG
[Image15]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
[Image16]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
[Image17]: ./media/sql-database-get-started/17ExcelSelect_SQLTut.PNG
[Image18]: ./media/sql-database-get-started/18ExcelTable_SQLTut.PNG
[Image19]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
[Image20]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG


<!--HONumber=35_1-->
