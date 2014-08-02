<properties  linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create & provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="" solutions="" writer="" manager="" editor="" />

# Introducción a Base de datos SQL de Azure

En este tutorial aprenderá los fundamentos de la administración de Base de datos SQL de Azure mediante el Portal de administración de Azure. Si no tiene experiencia en la administración de bases de datos, puede seguir estas lecciones para aprender lo básico en aproximadamente 30 minutos.

En este tutorial se supone que no tiene una experiencia previa en SQL Server o en Base de datos SQL de Azure. Una vez completado este tutorial, dispondrá de una base de datos de ejemplo en Azure y sabrá cómo realizar tareas de administración básicas mediante el Portal de administración.

Creará y realizará el aprovisionamiento de una base de datos de ejemplo en Azure, un sistema de consultas y datos de usuario mediante Excel y otras aplicaciones.

## Tabla de contenido

* [Paso 1: Crear una cuenta de Azure](#Subscribe)
* [Paso 2: Conectarse a Azure y crear una base de datos](#Connect)
* [Paso 3: Configurar el firewall](#ConfigFirewall)
* [Paso 4: Agregar datos y un esquema mediante el script
  Transact-SQL](#AddData)
* [Paso 5: Crear el esquema](#createschema)
* [Paso 6: Insertar datos](#insertData)
* [Paso 7: Ejemplo de consulta y datos del sistema en el Portal de
  administración para Base de datos SQL](#QueryDBSysData)
* [Paso 8: Crear un inicio de sesión de base de datos y asignar
  permisos](#DBLogin)
* [Paso 9: Conectar desde otras aplicaciones](#ClientConnection)
* [Paso 10: Configurar SQL Data Sync](#ConfigureDataSync)

<h2><a  id="Subscribe" ></a>Paso 1: Crear una cuenta de Azure</h2>


1.  Abra un explorador web y diríjase a
    [http://www.windowsazure.com][1]. Para comenzar con una cuenta
    gratuita, haga clic en Free Trial en la esquina superior derecha y
    siga los pasos.

2.  Se ha creado su cuenta. Ya está listo para comenzar.

<h2><a  id="Connect" ></a>Paso 2: Conectarse a Azure y crear una base de datos</h2>


1.  Inicie sesión en el [Portal de administración][2]. Debe ver un panel de navegación con el siguiente aspecto:
    
    ![Image1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2.  Haga clic en **New** en la parte inferior de la página. Cuando haga clic en **New**, aparecerá una lista en la pantalla que le mostrará los elementos que puede crear.

3.  Haga clic en **Base de datos SQL** y, a continuación, en **Custom Create**.
    
    ![Image2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)
    
    Si selecciona esta opción, puede crear un nuevo servidor a la vez con usted como administrador. Como administrador del sistema, puede realizar más tareas, incluida la conexión al Portal de administración para Base de datos SQL, que realizará más tarde en este tutorial.

4.  Aparecerá la página Database Settings cuando haga clic en **Custom Create**. En esa página, se proporciona información básica que crea una base de datos vacía en el servidor. La adición de tablas y datos se tratará en un paso posterior.
    
    Complete la página Database Settings de la siguiente forma:
    
    ![Image3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)
    
    * Especifique **School** como nombre de la base de datos.
    
    * Use la configuración predeterminada para edición, tamaño máximo e intercalación.
    
    * Seleccione **New SQL Database Server**. Si selecciona un nuevo servidor, se agrega una segunda página que usaremos para configurar la cuenta de administración y la región.
    
    * Una vez que se haya completado esta tarea, haga clic en la flecha para ir a la siguiente página.

5.  Rellene los campos de Server Settings de la siguiente forma:
    
    ![Image4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)
    
    * Especifique un nombre de administrador con una sola palabra sin espacios. Base de datos SQL usa la autenticación SQL a través de una conexión cifrada para validar la identidad de usuario. Se creará un inicio de sesión de autenticación de SQL Server con permisos de administrador con el nombre que proporcione. El nombre de administrador no puede ser un usuario de Windows ni debe ser un Windows Live ID. La autenticación de Windows no es compatible con Base de datos SQL.
    
    * Proporcione una contraseña segura con más de ocho caracteres que contenga una combinación de valores en mayúsculas y minúsculas, y un número o símbolo.
    
    * Seleccione una región. La región determina la ubicación geográfica del servidor. Las regiones no pueden cambiarse fácilmente, por lo que debe seleccionar la apropiada para el servidor. Seleccione la ubicación más cercana a usted o a sus usuarios. Si mantiene la base de datos y la aplicación de Azure en la misma región, evita que se produzcan costes relacionados con el ancho de banda y la latencia de datos.
    
    * Asegúrese de que la casilla **Allow Azure Services to access this server** esté activada para poder conectarse a la base de datos mediante el Portal de administración para Base de datos SQL, Excel en Office 365 o Azure SQL Reporting.
    
    * Haga clic en la marca de verificación en la parte inferior de la página cuando haya finalizado.
    
    Tenga en cuenta que no especificó un nombre de servidor. Puesto que el servidor de Base de datos SQL debe ser accesible en todo el mundo, Base de datos SQL configura las entradas DNS apropiadas cuando se crea el servidor. El nombre generado garantiza que no existen colisiones de nombres con otras entradas DNS. No puede cambiar el nombre del servidor de Base de datos SQL.
    
    En el próximo paso, configurará el firewall de manera que las conexiones de las aplicaciones que se ejecuten en su equipo puedan obtener acceso a las bases de datos en el servidor de Base de datos SQL.

<h2><a  id="ConfigFirewall" ></a>Paso 3: Configurar el firewall</h2>


Para configurar el firewall para que se permitan las conexiones, especifique la información en la página del servidor.

**Nota:** el servicio de Base de datos SQL solo está disponible con el puerto TCP 1433 que usa el protocolo TDS. Por lo tanto, asegúrese de que el firewall de la red y el equipo local permitan la comunicación TCP de salida en el puerto 1433. Para obtener más información, consulte [Azure SQL Database Firewall][3].

1.  En el panel de navegación de la izquierda, haga clic en **SQL Databases**.

2.  Haga clic en **Servers** en la parte superior de la página. A continuación, haga clic en el servidor que acaba de crear hasta que vea una flecha blanca a la derecha. Haga clic en la flecha para abrir la página del servidor.
    
    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3.  En la página del servidor, haga clic en **Configure** para abrir los valores de configuración del firewall y especifique la regla de la siguiente forma:
    
    ![Image6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)
    
    * Copie la dirección IP del cliente actual. Esta es la dirección IP que está escuchando el servidor proxy o el router. Base de datos SQL detecta la dirección IP que usa la conexión actual para que pueda crear una regla de firewall para aceptar solicitudes de conexión desde ese dispositivo.
    
    * Pegue la dirección IP en el intervalo de inicio y finalización. Después, si se producen errores de conexión que indican que el intervalo es demasiado estrecho, puede editar la regla para ampliarlo.
    
    * Especifique un nombre para la regla de firewall, como el nombre del equipo o la compañía.
    
    * Haga clic en la marca de verificación para guardar la regla.
    
    Una vez que haya guardado la regla, la página tendrá un aspecto similar al que se muestra en la siguiente captura de pantalla.
    
    ![Image7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)

4.  Haga clic en **Save** en la parte inferior de la página para completar el paso. Si no consigue ver **Save**, actualice la página del explorador.

Ahora dispone de un servidor de Base de datos SQL en Azure, una regla de firewall que permite el acceso al servidor, un objeto de base de datos y un inicio de sesión de administrador. A continuación, usará la ventana de consulta del Portal de administración para Base de datos SQL para ejecutar un script Transact-SQL para crear una base de datos predefinida.

A medida que aumenten sus conocimientos, querrá explorar formas adicionales de crear una base de datos, incluidos los enfoques programáticos o sobre el diseñador de SQL Server Data Tools. Si ya dispone de una base de datos de SQL Server que se ejecuta en un servidor local, puede migrar fácilmente la base de datos al servidor de Azure que desea configurar. Use los vínculos al final de este tutorial para descubrir cómo.

<h2><a  id="AddData" ></a>Paso 4: Agregar datos y un esquema mediante el script Transact-SQL</h2>


En este paso, ejecutará dos scripts. El primero crea un esquema que define tablas, columnas y relaciones. El segundo script agrega los datos. Cada paso se realiza de forma independiente en una conexión independiente. Si ha integrado bases de datos en SQL Server antes, una de las diferencias que notará en Base de datos SQL es que los comandos CREATE e INSERT deben ejecutarse en lotes separados. Base de datos SQL impone este requisito para reducir los ataques contra los datos cuando se encuentran en tránsito.

**Nota:** los valores de datos y el esquema proceden de este [artículo de MSDN][4] y se han modificado para que funcionen con Base de datos SQL.

1.  Vaya a la página principal. En el [Portal de administración][2], aparece la base de datos **School** en la lista de elementos de la página principal.
    
    ![Image8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)

2.  Haga clic en **School** para ver una flecha blanca a la derecha. Haga clic en la flecha para abrir la página de la base de datos.
    
    ![Image9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3.  Haga clic en **Manage** en la parte inferior de la página. Si no aparece, actualice la ventana del explorador. Se abrirá el Portal de administración para Base de datos SQL. Este portal es independiente del Portal de administración de Azure. Usará este portal para ejecutar los comandos Transact-SQL y las consultas.
    
    ![Image10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)

4.  Especifique el nombre y la contraseña de inicio de sesión del administrador. Este es el inicio de sesión de administrador que especificó cuando creó el servidor.
    
    ![Image11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

5.  Haga clic en **New Query** en Portal de administración para Base de datos SQL. Aparecerá una ventana de consulta vacía en el espacio de trabajo. En el siguiente paso usará esta ventana para copiar una serie de scripts predefinidos que agregarán la estructura y los datos a la base de datos vacía.
    
    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

<h2><a  id="createschema" ></a>Paso 5: Crear el esquema</h2>


En este paso, creará el esquema mediante el siguiente script. El script comprueba primero si existe una tabla con el mismo nombre para asegurarse de que no habrá una colisión de nombres y crea la tabla mediante la instrucción [CREATE TABLE (Base de datos SQL de Azure)][5]. Posteriormente, el script usa la instrucción [ALTER TABLE (Base de datos SQL de Azure)][6] para especificar la clave principal y las relaciones de tabla.

Copie el script y péguelo en la ventana de consulta. Haga clic en
**Run** en la parte superior de la ventana para ejecutar el script.

 
<div  style="width:auto; height:600px; overflow:auto"><pre>
	-- Crear la tabla Department.
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

	-- Crear la tabla Person.
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

	-- Crear la tabla OnsiteCourse.
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

	-- Crear la tabla OnlineCourse.
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

	--Crear la tabla StudentGrade.
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

	-- Crear la tabla CourseInstructor.
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

	-- Crear la tabla Course.
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

	-- Crear la tabla OfficeAssignment.
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

	-- Definir la relación entre OnsiteCourse y Course.
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

	-- Definir la relación entre OnlineCourse y Course.
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
	-- Definir la relación entre StudentGrade y Course.
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

	--Definir la relación entre between StudentGrade y Student.
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

	-- Definir la relación entre CourseInstructor y Course.
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

	-- Definir la relación entre CourseInstructor y Person.
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

	-- Definir la relación entre Course y Department.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
	ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
	REFERENCES [dbo].[Department] ([DepartmentID]);
	GO
	ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
	GO

	--Definir la relación entre OfficeAssignment y Person.
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

 <h2><a  id="insertData" ></a>Paso 6: Insertar datos</h2>


Abra una nueva ventana de consulta y, a continuación, pegue el siguiente script. Ejecute el script para insertar datos. Este script usa la instrucción [INSERT (Base de datos SQL de Azure)][7] para agregar valores a cada columna.

 
<div  style="width:auto; height:600px; overflow:auto"><pre>
	-- Insertar datos en la tabla Person.
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
	-- Insertar datos en la tabla Department.
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
	GO
	-- Insertar datos en la tabla Course.
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1050, 'Chemistry', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1061, 'Physics', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1045, 'Calculus', 4, 7);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2030, 'Poetry', 2, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2021, 'Composition', 3, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2042, 'Literature', 4, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4022, 'Microeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4041, 'Macroeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4061, 'Quantitative', 2, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (3141, 'Trigonometry', 4, 7);
	GO
	-- Insertar datos en la tabla OnlineCourse.
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2030, 'http://www.fineartschool.net/Poetry');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2021, 'http://www.fineartschool.net/Composition');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
	--Insert data into OnsiteCourse table.
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1050, '123 Smith', 'MTWH', '11:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1061, '234 Smith', 'TWHF', '13:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1045, '121 Smith','MWHF', '15:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4061, '22 Williams', 'TH', '11:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (2042, '225 Adams', 'MTWH', '11:00');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4022, '23 Williams', 'MWF', '9:00');
	-- Insertar datos en la tabla CourseInstructor.
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1050, 1);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1061, 31);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1045, 5);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2030, 4);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2021, 27);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2042, 25);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4022, 18);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4041, 32);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4061, 34);
	GO
	--Insertar datos en la tabla OfficeAssignment.
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (1, '17 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (4, '29 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (5, '37 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (18, '143 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (25, '57 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (27, '271 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (31, '131 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (32, '203 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (34, '213 Smith');
	-- Insertar datos en la tabla StudentGrade.
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 2, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 2, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 3, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 3, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 6, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 6, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 7, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 7, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 9, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 10, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 11, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 15, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 16, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 17, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 19, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 20, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 21, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 22, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 22, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 22, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 23, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 23, 1.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 24, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 25, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 26, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 26, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 27, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 28, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 28, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 29, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 30, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 30, 4);
	GO
</pre></div>

 <h2><a  id="QueryDBSysData" ></a>Paso 7: Ejemplo de consulta y datos del sistema en el Portal de administración para Base de datos SQL</h2>


Para comprobar su trabajo, ejecute una consulta que devuelva los datos que acaba de especificar. Ejecute también procedimientos almacenados integrados y vistas de administración de datos que proporcionen información sobre las bases de datos que se ejecutan en el servidor de Base de datos SQL.

<h4><a  id="QueryDB" ></a>Datos de ejemplo de consultas</h4>


En una nueva ventana de consulta, copie y ejecute el siguiente script Transact-SQL para recuperar algunos de los datos que acaba de agregar.

 
<div  style="width:auto; height:auto; overflow:auto"><pre>
	SELECT
		Course.Title as "Course Title"
  		,Department.Name as "Department"
  		,Person.LastName as "Instructor"
  		,OnsiteCourse.Location as "Location"
  		,OnsiteCourse.Days as "Days"
  		,OnsiteCourse.Time as "Time"
	FROM
 	 Course
 	 INNER JOIN Department
  	  ON Course.DepartmentID = Department.DepartmentID
 	 INNER JOIN CourseInstructor
 	   ON Course.CourseID = CourseInstructor.CourseID
 	 INNER JOIN Person
 	   ON CourseInstructor.PersonID = Person.PersonID
 	 INNER JOIN OnsiteCourse
		ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
</pre></div>

 Debe ver un conjunto de resultados con el aspecto de la siguiente
ilustración:

![Image13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)

<h4><a  id="QuerySys" ></a>Datos del sistema de consultas</h4>


También puede usar vistas del sistema y procedimientos almacenados integrados para obtener información del servidor. Para cumplir los objetivos de este tutorial, se probarán algunos comandos.

Ejecute el siguiente comando para conocer qué bases de datos se encuentran disponibles en el servidor.

    SELECT * FROM sys.databases  

Ejecute este comando para devolver una lista de los usuarios conectados actualmente al servidor.

    SELECT user_name(),suser_sname()

Ejecute este procedimiento almacenado para devolver una lista de todos los objetos en la base de datos **School**.

    EXEC SP_help

No cierre la conexión del portal a la base de datos **School**. La volverá a necesitar de nuevo en unos minutos.

<h2><a  id="DBLogin" ></a>Paso 8: Crear un inicio de sesión de base de datos y asignar permisos</h2>


En Base de datos SQL, puede crear inicios de sesión y conceder permisos mediante Transact-SQL. En esta lección, realizará los siguientes procedimientos mediante Transact-SQL: creación de un inicio de sesión de autenticación de SQL Server, creación de un usuario de base de datos y concesión de permisos a través de la pertenencia a roles.

Se usa un inicio de sesión de autenticación de SQL Server para las conexiones de servidor. Todos los usuarios que obtengan acceso a una base de datos en un servidor de Base de datos SQL lo hacen mediante un nombre y una contraseña de inicio de sesión de autenticación de SQL Server.

Para crear un inicio de sesión, primero debe conectase a la base de datos maestra (**master**).

<h4><a  id="CreateLogin" ></a>Crear un inicio de sesión de autenticación de SQL Server</h4>


1.  En el [Portal de administración][2], seleccione **SQL Databases**, haga clic en **Servers**, seleccione el servidor y, a continuación, haga clic en la flecha blanca para abrir la página del servidor.
    
    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2.  En la página Quick Start, haga clic en **Manage Server** para abrir una nueva conexión al Portal de administración para Base de datos SQL.

3.  Especifique el nombre y la contraseña del administrador. Este es el inicio de sesión de administrador que especificó cuando creó el servidor.
    
    ![Image20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

4.  Se abrirá el portal de administración de Base de datos SQL en una nueva ventana del explorador. Haga clic en **Select a Database** en la parte superior y haga clic en **master**.
    
    ![Image14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5.  Si se produce un error en la página similar al siguiente, ignórelo. Haga clic en **New Query** para abrir una ventana de consulta que le permita ejecutar comandos Transact-SQL en la base de datos maestra  (**master**).
    
    ![Image15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6.  Copie y pegue el siguiente comando en la ventana de consulta.
    
         CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  Ejecute el comando para crear un nuevo inicio de sesión de SQL Server con el nombre \'SQLDBLogin\'.

<h4><a  id="CreateDBuser" ></a>Crear un usuario de base de datos y asignar permisos</h4>


Una vez creado un inicio de sesión de autenticación de SQL Server, el siguiente paso es asignar la base de datos y los niveles de permiso asociados al inicio de sesión. Puede hacer esto mediante la creación de un **usuario de base de datos** en cada base de datos.

1.  Vuelva a la página del portal de administración de Base de datos SQL que se conecta con la base de datos **School**. Si cerró la ventana del explorador, inicie una nueva conexión con la base de datos **School** mediante los pasos de la lección anterior, "Agregar datos y un esquema mediante el script Transact-SQL".
    
    En la página del portal de administración de Base de datos SQL, puede visualizarse el nombre de la base de datos **School** en la esquina superior izquierda.
    
    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2.  Haga clic en **New Query** para abrir una nueva ventana de consulta y copie la siguiente instrucción.
    
         CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  Ejecute el script. Este script crea un nuevo usuario de base de datos basado en el inicio de sesión.
    
    A continuación, asignará permisos con el rol db\_datareader. Los usuarios de base de datos asignados a este rol pueden leer todos los datos de todas las tablas de usuario en la base de datos.

4.  Abra una nueva ventana de consulta y, a continuación, especifique y ejecute la siguiente instrucción. Esta instrucción ejecuta un procedimiento almacenado integrado que asigna el rol db\_datareader al nuevo usuario que acaba de crear.
    
         EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';
    
    Ahora dispone de un inicio de sesión de autenticación de SQL Server que cuenta con permiso de solo lectura en la base de datos **School**. Use estos pasos para crear otros inicios de sesión de autenticación de SQL Server para permitir diferentes niveles de acceso a los datos.

<h2><a  id="ClientConnection" ></a>Paso 9: Conectar desde otras aplicaciones</h2>


Ahora que dispone de una base de datos operacional, puede conectarla desde un libro de Excel.

<h4>Conectar desde Excel</h4>


Si tiene Excel 2010 instalado en su equipo, puede usar los siguientes pasos para conectarse a su base de datos de ejemplo.

1.  En Excel, en la pestaña Data, haga clic en **From Other Sources** y, a continuación, haga clic en **From SQL Server**.

2.  En el Asistente para la conexión de datos, especifique el nombre de dominio completo del servidor de Base de datos SQL, seguido del inicio de sesión de autenticación de SQL Server que disponga de permiso para obtener acceso a la base de datos.

Puede encontrar el nombre de servidor en la página **Database** en **Quick Links**. También puede encontrarse el nombre de servidor en el Portal de administración de Azure, en Base de datos SQL, en la página Server, en el Panel o en **Manage URL**.

El nombre de servidor consta de una serie de letras y números seguidos de \'.database.windows.net\'. Especifique este nombre en el asistente para la conexión de base de datos. No incluya el prefijo http:// o https:// cuando especifique el nombre.

Especifique un inicio de sesión de autenticación de SQL Server. Para propósitos de prueba, puede usar el inicio de sesión de administrador que creó cuando configuró el servidor. Para obtener acceso a datos de forma normal, use un inicio de sesión de usuario de base de datos similar al que acaba de crear.

![Image16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)

1.  En la siguiente página, seleccione la base de datos **School** y, a continuación, seleccione **Course**. Haga clic en **Finish**.
    
    ![Image17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

2.  Aparecerá el cuadro de diálogo Import Data que le solicitará que seleccione cómo y dónde importar los datos. Con las opciones predeterminadas seleccionadas, haga clic en **OK**.
    
    ![Image19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)

3.  En la hoja de cálculo, debe ver una tabla similar a la siguiente:
    
    ![Image18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

Si usa Excel, puede importar solo una tabla a la vez. Un mejor enfoque es usar el complemento PowerPivot para Excel, que le permite importar y trabajar con varias tablas como un conjunto de datos único. Trabajar con PowerPivot se encuentra fuera del ámbito de este tutorial, pero puede obtener más información al respecto en este [sitio web de Microsoft][8].

<h2><a  id="ConfigureDataSync" ></a>Paso 10: Configurar SQL Data Sync</h2>


<h4>SQL Data Sync</h4>


Ahora que ha creado sus instancias de Base de datos SQL, puede aprovechar SQL Data Sync para mantener los datos de gran valor sincronizados en varias ubicaciones.

SQL Data Sync es una característica de Base de datos SQL que le permite sincronizar datos seleccionados, de forma programada o bajo demanda, sin escribir códigos o scripts. SQL Data Sync es compatible con la sincronización entre instancias de Base de datos SQL o topologías híbridas que incluyan bases de datos SQL e instancias de SQL Server.

Para obtener más información acerca de SQL Data Sync, consulte [SQL Data Sync (versión preliminar)][9].

<h2><a  id="NextSteps" ></a>Pasos siguientes</h2>


Ahora que está familiarizado con Base de datos SQL y los portales de administración, puede probar otras herramientas y técnicas usadas por los administradores de bases de datos SQL Server.

Para administrar activamente su nueva base de datos, considere instalar y usar SQL Server Management Studio. Management Studio es la herramienta de administración de base de datos principal para la administración de bases de datos SQL Server, incluidas las que se ejecutan en Azure. Use Management Studio para guardar consultas y usarlas más adelante, agregar nuevas tablas y procedimientos almacenados y perfeccionar sus conocimientos de Transact-SQL en un entorno de scripting enriquecido que incluya un comprobador de sintaxis, IntelliSense y plantillas. Para comenzar, siga las instrucciones que se describen en [Administración de bases de datos SQL con SQL Server Management Studio][10].

Contar con un buen dominio del lenguaje de definición de datos y de las consultas Transact-SQL es fundamental para los administradores de bases de datos. Si no tiene experiencia en Transact-SQL, comience por el
[Tutorial: Escribir instrucciones Transact-SQL][11] para adquirir
algunos conocimientos básicos.

Existen otros métodos para mover una base de datos local a Base de datos SQL. Si ya dispone de bases de datos, o si ha descargado bases de datos de ejemplo con las que practicar, pruebe los siguientes enfoques alternativos:

* [Migrar bases de datos a bases de datos SQL de Azure (anteriormente SQL Azure)][12]
* [Copiar bases de datos en Base de datos SQL de Azure][13]



[1]: http://www.windowsazure.com
[2]: http://manage.windowsazure.com
[3]: http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621790.aspx "MSDN article"
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336258.aspx
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336286.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336284.aspx
[8]: http://www.microsoft.com/en-us/bi/powerpivot.aspx
[9]: http://go.microsoft.com/fwlink/?LinkId=274959
[10]: http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/
[11]: http://msdn.microsoft.com/en-us/library/ms365303.aspx
[12]: http://msdn.microsoft.com/en-us/library/windowsazure/ee730904.aspx
[13]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951624.aspx