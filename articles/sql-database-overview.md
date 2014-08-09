

Administración de bases de datos SQL en Azure
=============================================

En esta guía se mostrará cómo realizar tareas administrativas para instancias de bases de datos y servidores lógicos en Base de datos SQL de Azure.

¿Qué es Base de datos SQL?
--------------------------

Base de datos SQL proporciona servicios de administración de bases de datos relacionales en Azure y se basa en la tecnología de SQL Server. Con Base de datos SQL se pueden aprovisionar e implementar instancias de base de datos, así como beneficiarse de un centro de datos distribuido que ofrece disponibilidad, escalabilidad y seguridad de clase empresarial con las ventajas de contar con funciones de protección de datos y recuperación automática integradas.

Tabla de contenido
------------------

-   [Inicio de sesión en Azure](#PreReq1)
-   [Configuración de Base de datos SQL](#PreReq2)
-   [Conexión mediante Management Studio](#PreReq3)
-   [Implementación de una base de datos en Azure.](#HowTo1)
-   [Incorporación de inicios de sesión y usuarios](#HowTo2)
-   [Escalación de una solución de base de datos SQL](#HowTo4)
-   [Supervisión de instancias de servidores lógicos y bases de datos](#HowTo3)
-   [Pasos siguientes](#NextSteps)

Inicio de sesión en Azure
-------------------------

Base de datos SQL proporciona servicios de administración, acceso y almacenamiento de datos relacionales en Azure. Para usarla, necesitará una suscripción a Azure.

1.  Abra un explorador web y diríjase a <http://www.windowsazure.com>. Para comenzar con una cuenta gratuita, haga clic en Evaluación gratuita en la esquina superior derecha y siga los pasos.

2.  Se ha creado su cuenta. Ya está listo para comenzar.

Creación y configuración de Base de datos SQL
---------------------------------------------

A continuación, se realizará un recorrido por los pasos para crear y configurar el servidor lógico. En el nuevo Portal de administración de Azure (Vista previa), los flujos de trabajo revisados le permiten crear primero una base de datos y, a continuación, crear un servidor.

En esta guía, creará primero el servidor. Es posible que prefiera este enfoque si dispone de bases de datos SQL Server existentes que quiera cargar.

### Creación de un servidor lógico

1.  Inicie sesión en <http://www.windowsazure.com>.

2.  Haga clic en **Base de datos SQL** y, a continuación, en **SERVERS** en la página principal de Base de datos SQL.

3.  Haga clic en **Add** en la parte inferior de la página.

4.  En Server Settings, especifique un nombre de administrador con una sola palabra sin espacios.

    Base de datos SQL usa la autenticación de SQL a través de una conexión cifrada. Se creará un nuevo inicio de sesión de autenticación de SQL Server asignado al rol del servidor fijo sysadmin con el nombre que proporcione.

    El inicio de sesión no puede ser una dirección de correo electrónico, una cuenta de usuario de Windows o un Windows Live ID. Las reclamaciones y la autenticación de Windows no son compatibles con Base de datos SQL.

5.  Proporcione una contraseña segura con más de ocho caracteres que contenga una combinación de valores en mayúsculas y minúsculas, y un número o símbolo.

6.  Seleccione una región. La región determina la ubicación geográfica del servidor. Las regiones no pueden cambiarse fácilmente, por lo que debe seleccionar la apropiada para el servidor. Seleccione la ubicación que le sea más próxima. Si mantiene la base de datos y la aplicación de Azure en la misma región, evita que se produzcan costes relacionados con el ancho de banda y la latencia de datos.

7.  Asegúrese de mantener la opción **Allow Services** seleccionada de manera que pueda conectarse a esta base de datos con el Portal de administración para Base de datos SQL, servicios de almacenamiento y otros servicios en Azure.

8.  Haga clic en la marca de verificación en la parte inferior de la página cuando haya finalizado.

Tenga en cuenta que no especificó un nombre de servidor. Base de datos SQL genera automáticamente el nombre del servidor para garantizar que no existen entradas DNS duplicadas. El nombre del servidor es una cadena alfanumérica de 10 caracteres. No puede cambiar el nombre del servidor de Base de datos SQL.

En el próximo paso, configurará el firewall de manera que se permita el acceso a las conexiones de las aplicaciones que se ejecuten en la red.

### Configuración del firewall para el servidor lógico

1.  Haga clic en **Bases de datos SQL**, haga clic en **Servers** y, a continuación, haga clic en el servidor que acaba de crear.

2.  Haga clic en **Configure**.

3.  Copie la dirección IP del cliente actual. Si está conectado desde una red, esta es la dirección IP que está escuchando el servidor proxy o el enrutador. Base de datos SQL detecta la dirección IP que usa la conexión actual para que pueda crear una regla de firewall para aceptar solicitudes de conexión desde ese dispositivo.

4.  Pegue la dirección IP en el intervalo de inicio y finalización. Después, si se producen errores de conexión que indican que el intervalo es demasiado estrecho, puede editar la regla para ampliarlo.

    Si los equipos cliente usan direcciones IP asignadas dinámicamente, debe especificar un intervalo que sea lo suficientemente amplio para que incluya direcciones IP asignadas a equipos en la red. Comience por un intervalo reducido y, a continuación, expándalo solo si lo necesita.

5.  Especifique un nombre para la regla de firewall, como el nombre del equipo o la compañía.

6.  Haga clic en la marca de verificación para guardar la regla.

7.  Haga clic en **Save** en la parte inferior de la página para completar el paso. Si no consigue ver **Save**, actualice la página del explorador.

Ahora dispone de un servidor lógico, una regla de firewall que permite conexiones entrantes desde la dirección IP, y un inicio de sesión de administrador. En el siguiente paso, cambiará al equipo local para completar los pasos de configuración restantes.

**Nota:** el servidor lógico que acaba de crear es transitorio y se hospedará dinámicamente en los servidores físicos de un centro de datos. Si elimina el servidor, debe saber antes que se trata de una acción irreversible. Asegúrese de realizar una copia de seguridad de las bases de datos que cargue posteriormente en el servidor.

Conexión mediante Management Studio
-----------------------------------

Management Studio es una herramienta administrativa que permite administrar varias instancias y servidores de SQL Server en un espacio de trabajo único. Si ya tiene una instancia SQL Server local, puede abrir una conexión tanto a la instancia local como a un servidor lógico en Azure para realizar las tareas de manera paralela.

Management Studio incluye características que no están actualmente disponibles en el portal de administración, como un comprobador de sintaxis y la posibilidad de guardar scripts y consultas con nombre para volver a usarlos. Base de datos SQL es un extremo de secuencia de datos tubular (TDS). Todas las herramientas que funcionan con TDS, incluido Management Studio, son válidas para las operaciones de Base de datos SQL. Los scripts que desarrolla para el servidor local se ejecutarán en un servidor lógico de Base de datos SQL.

En el siguiente paso, utilizará Management Studio para conectarse a un servidor lógico en Azure. Este paso requiere tener SQL Server Management Studio versión 2008 R2 o 2012. Si necesita ayuda para descargar o conectarse a Management Studio, consulte [Administración de Base de datos SQL con Management Studio](http://www.windowsazure.com/es-es/develop/net/common-tasks/sql-azure-management/) en este sitio.

Antes de poder conectarse, a veces es necesario crear una excepción de firewall que permita realizar solicitudes salientes en el puerto 1433 en el sistema local. Los equipos que están asegurados de forma predeterminada suelen tener el puerto 1433 cerrado.

### Configuración del firewall para un servidor local

1.  En el Firewall de Windows con seguridad avanzada, cree una nueva regla saliente.

2.  Seleccione **Puerto**, especifique TCP 1433, especifique **Permitir la conexión** y asegúrese de que el perfil **Público** esté seleccionado.

3.  Proporcione un nombre significativo, como *AzureSQLDatabase puerto (salida tcp) 1433*.

### Conexión a un servidor lógico

1.  En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y, a continuación, escriba el nombre del servidor lógico con el formato siguiente: *nombre del servidor*.database.windows.net

    También puede obtener el nombre completo del servidor en el portal de administración, en el panel del servidor, en Administrar dirección URL.

2.  En Autenticación, seleccione **Autenticación de SQL Server** y, a continuación, escriba el inicio de sesión de administrador que creó al configurar el servidor lógico.

3.  Haga clic en **Opciones**.

4.  En Conectar con base de datos, especifique **master**.

### Conexión a un servidor local

1.  En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y, a continuación, escriba el nombre de una instancia local con el formato siguiente: *nombre del servidor*\\*nombre de la instancia*. Si el servidor es local y una instancia predeterminada, escriba el *host local*.

2.  En Autenticación, seleccione **Autenticación Windows** y, a continuación, escriba una cuenta de Windows que pertenezca al rol sysadmin.

Implementación de una base de datos en Azure
--------------------------------------------

Existen distintos enfoques para trasladar una base de datos SQL Server local a Azure. En esta tarea, usará el Asistente de implementación de la base de datos en Base de datos SQL para cargar una base de datos de ejemplo.

La base de datos de ejemplo School es muy sencilla; todos sus objetos son compatibles con Base de datos SQL, por lo que no es necesario modificar o preparar la base de datos para la migración. Como nuevo administrador, intente implementar una base de datos simple antes de conocer el procedimiento para usar sus propias bases de datos.

**Nota:** consulte la guía de migración de Base de datos SQL para obtener información detallada sobre cómo preparar una base de datos local para la migración a Azure. Considere también descargar el Kit de aprendizaje para Azure. El kit incluye un ejercicio práctico que muestra un enfoque alternativo para la migración de la base de datos local.

### Creación de la base de datos School en un servidor local

Puede encontrar los scripts para la creación de esta base de datos en [Introducción a la administración de Base de datos SQL](http://www.windowsazure.com/es-es/manage/tutorials/sql-azure-management/). En esta guía, ejecutará los scripts en Management Studio para crear una versión local de la base de datos School.

1.  En Management Studio, conéctese a un servidor local. Haga clic con el botón secundario en **Databases**, haga clic en **New Database** y especifique *school*.

2.  Haga clic con el botón secundario en *school* y haga clic en **New Query**.

3.  Copie y ejecute el script de creación de esquema del tutorial.

``` {}
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

    --Definir la relación entre StudentGrade y Student.
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
```

A continuación, copie y ejecute el script de inserción de datos.

``` {}
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
    --Insertar datos en la tabla OnsiteCourse.
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
    VALUES (2021, 6, 2,5);
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
    VALUES (4041, 11, 2,5);
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
    VALUES (4022, 15, 2,5);
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
    VALUES (4061, 22, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 23, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 23, 1,5);
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
    VALUES (1045, 28, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 28, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 29, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 30, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 30, 4);
    GO
```

Ahora dispone de una base de datos local que puede exportar a Azure. A continuación, ejecutará un asistente que crea un archivo .bacpac, lo carga en Azure y lo importa en Base de datos SQL.

### Implementación en Base de datos SQL

1.  En Management Studio, conéctese a una instancia de SQL Server local que cuente con una base de datos que desee migrar.

2.  Haga clic con el botón secundario en la base de datos School que acaba de crear, seleccione **Tasks** y haga clic en **Deploy Database to SQL Database**.

3.  En Deployment Settings, especifique un nombre para la base de datos, como *school*.

4.  Haga clic en **Connect**.

5.  En Server name, especifique un nombre de servidor de 10 caracteres seguido de .databases.windows.net.

6.  En Authentication, seleccione **SQL Server Authentication**.

7.  Especifique el nombre de inicio de sesión y la contraseña de administrador que aprovisionó al crear el servidor lógico de Base de datos SQL.

8.  Haga clic en **Options**.

9.  En Connection Properties, en Connect to database, escriba **master**.

10. Haga clic en **Connect**. Con este paso se completa la especificación de la conexión y se le redirige al asistente.

11. Haga clic en **Next** y en **Finish** para ejecutar el asistente.

### Comprobación de la implementación de la base de datos

1.  En Management Studio, conéctese a un servidor lógico. Si ya dispone de una conexión abierta, puede cerrarla y volver a abrir una nueva. La conexión existente muestra solo las bases de datos que se encuentran en ejecución en el momento en el que se realizó la conexión.

    Para ver las instrucciones sobre cómo conectarse a un servidor lógico, consulte [Conexión mediante Management Studio](#PreReq3) en este documento.

2.  Expanda la carpeta Databases. Debe ver la base de datos school en la lista.

3.  Haga clic con el botón secundario en la base de datos School y haga clic en **New Query**.

4.  Ejecute la siguiente consulta para comprobar que es posible obtener acceso a los datos.

``` {}
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
```

Incorporación de inicios de sesión y usuarios
---------------------------------------------

Después de implementar una base de datos, debe configurar los inicios de sesión y asignar permisos. En el siguiente paso, ejecutará dos scripts.

Con el primer script, se conectará con master y ejecutará un script para crear los inicios de sesión. Los inicios de sesión se utilizarán para admitir las operaciones de lectura y escritura y para delegar las tareas de las operaciones, como la capacidad de ejecutar las consultas del sistema sin permisos.

Los inicios de sesión que cree deben ser inicios de sesión de autenticación de SQL Server. Si tiene scripts ya preparados que utilicen identidades de usuario Windows o identidades de notificaciones, esos scripts no funcionarán en Base de datos SQL.

El segundo script asigna permisos de usuario de base de datos. Con este script, se conectará con una base de datos que ya esté cargada en Azure.

### Creación de inicios de sesión

1.  En Management Studio, conéctese con un servidor lógico en Azure, expanda la carpeta Bases de datos, haga clic con el botón secundario en **master** y seleccione **Nueva consulta**.

2.  Utilice las siguientes instrucciones Transact-SQL para crear inicios de sesión. Sustituya la contraseña por una contraseña válida. Seleccione cada una de forma individual y, a continuación, haga clic en **Ejecutar**. Repita la operación con los inicios de sesión restantes.

``` {}
    -- ejecútelo en master, ejecute cada línea por separado
    -- utilice este inicio de sesión para administrar otros inicios de sesión en este servidor
    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- utilice este inicio de sesión para crear o copiar una base de datos
    CREATE LOGIN sqlops WITH password='<ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
```

### Creación de usuarios de base de datos

1.  Expanda la carpeta Bases de datos, haga clic con el botón secundario en **school** y seleccione **Nueva consulta**.

2.  Utilice las siguientes instrucciones Transact-SQL para agregar usuarios de bases de datos. Sustituya la contraseña por una contraseña válida.

``` {}
    -- ejecútelo en una base de datos normal, ejecute cada línea por separado
    -- utilice este inicio de sesión para leer operaciones
    CREATE LOGIN sqlreader WITH password='<ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- utilice este inicio de sesión para escribir operaciones
    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- conceda permisos DMV en la base de datos school para "sqlops"
    GRANT VIEW DATABASE STATE to 'sqlops';
```

### Visualización y prueba de inicios de sesión

1.  En una ventana de nueva consulta, conéctese con **master** y ejecute la siguiente instrucción:

         SELECT * from sys.sql_logins;

2.  En Management Studio, haga clic con el botón secundario en la base de datos **school** y seleccione **Nueva consulta**.

3.  En el menú Consulta, seleccione **Conexión** y, a continuación, haga clic en **Cambiar conexión**.

4.  Inicie sesión como *sqlreader*.

5.  Copie e intente ejecutar la siguiente instrucción. Debería recibir un error que informe de que el objeto no existe.

         INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
         VALUES (1061, 30, 9);

6.  Abra una segunda ventana de consulta y cambie el contexto de conexión a *sqlwriter*. La misma consulta debería ejecutarse ahora correctamente.

Ya ha creado y probado varios inicios de sesión. Para obtener más información, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/ee336235.aspx) y [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica](http://msdn.microsoft.com/es-es/library/windowsazure/ff394114.aspx).

Supervisión de servidores lógicos e instancias de bases de datos
----------------------------------------------------------------

Las herramientas y técnicas de supervisión a las que podría estar acostumbrado a usar en un servidor local, como la auditoría de inicios de sesión, la ejecución de seguimientos y el uso de contadores de rendimiento, no están disponibles para la base de datos SQL. En Azure, puede usar vistas de administración de datos (DMV) para supervisar la capacidad de datos, los problemas de consulta y las conexiones actuales.

Para obtener más información, consulte [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica](http://msdn.microsoft.com/es-es/library/windowsazure/ff394114.aspx).

Escalación de una solución de base de datos SQL
-----------------------------------------------

En Azure, la escalabilidad de la base de datos es sinónimo de escalado horizontal, donde la carga de trabajo se redistribuye entre los distintos servidores de mercancía en un centro de datos. El escalado horizontal es una estrategia para solucionar problemas relacionados con el rendimiento o la capacidad de los datos. Una base de datos de gran tamaño en una trayectoria de crecimiento considerable requerirá finalmente una estrategia de escalado horizontal, tanto si algunos o muchos usuarios obtienen acceso a ella.

La forma en la que mejor se consigue el escalado horizontal en Azure es mediante la federación. La federación de Base de datos SQL se basa en el particionamiento horizontal, en donde una o más tablas se dividen por fila y se fragmentan entre los distintos miembros de la federación.

La federación no es la única respuesta a cada problema de escalabilidad. A veces, las características de los requisitos de aplicación o de los datos apuntan a enfoques más sencillos. La siguiente lista muestra las posibles soluciones en orden de complejidad.

**Aumento del tamaño de la base de datos**

Las bases de datos se crean con un tamaño fijo sujeto a un máximo impuesto por cada edición. Para la Web Edition, puede aumentar la base de datos hasta un máximo de 5 gigabytes. En la Business Edition, el tamaño máximo de la base de datos es de 150 gigabytes. La forma más lógica de aumentar la capacidad de datos es cambiar la edición y el tamaño máximo:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**Uso de varias bases de datos y asignación de usuarios**

En determinados escenarios, puede crear copias de una base de datos y, a continuación, asignar inicios de sesión y usuarios entre cada base de datos. Antes de que la federación fuese una opción, este era un enfoque común para la redistribución de una carga de trabajo. Este enfoque es viable para las bases de datos que usa a corto plazo y que, a continuación, fusiona en una base de datos principal que mantiene en local, y para soluciones que proporcionan datos de solo lectura.

**Uso de federaciones**

Las federaciones en Base de datos SQL se usan para conseguir un rendimiento y una escalabilidad superiores. Una o más tablas en una base de datos se dividen por fila y se fragmentan entre varias bases de datos (miembros de la federación). A este tipo de partición horizontal se le conoce a veces como "particionamiento". Los escenarios principales en los que es útil son aquellos en los que es necesario conseguir una escalación o un rendimiento determinados, o para administrar la capacidad.

Las federaciones se admiten en la Business Edition. Para obtener más información, consulte [Federaciones en Base de datos SQL de Azure (anteriormente SQL Azure)](http://msdn.microsoft.com/es-es/library/windowsazure/hh597452.aspx) y [Tutorial de Federaciones de bases de datos SQL: DBA](http://msdn.microsoft.com/es-es/library/windowsazure/hh778416.aspx).

**Consideración de otras formas de almacenamiento**

Recuerde que Azure es compatible con varias formas de almacenamiento de datos, incluido el almacenamiento de tablas y el almacenamiento de blobs. Si no precisa características relacionales, el almacenamiento de tablas o blobs puede resultar más económico.

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos de la administración de Base de datos SQL, siga estos vínculos para obtener más información acerca de cómo realizar tareas administrativas más complejas.

-   Consulte [Base de datos SQL](http://msdn.microsoft.com/es-es/library/windowsazure/gg619386) en MSDN.
-   Visite la [wiki de TechNet de Base de datos SQL](http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx) (en inglés).

