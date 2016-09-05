<properties
	pageTitle="Todos los temas del servicio Base de datos SQL | Microsoft Azure"
	description="Tabla de todos los temas del servicio Base de datos SQL que se encuentra en http://azure.microsoft.com/documentation/articles/, con títulos y descripciones."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="genemi"/>


# Todos los temas del servicio Base de datos SQL de Azure

En este documento se muestran todos los temas relacionados directamente con el servicio **Base de datos SQL** de Azure. Puede buscar en esta página web las palabras clave mediante **Ctrl+F**, para encontrar los temas de interés.




## Nuevo

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 1 | [Introducción a las características de JSON en Base de datos SQL de Azure](sql-database-json-features.md) | Base de datos SQL de Azure permite redistribuir datos, consultarlos y darles formato en notación de objetos JavaScript (JSON). |
| 2 | [Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL](sql-database-ssms-mfa-authentication.md) | Use Multi-Factor Authentication con SSMS para Base de datos SQL y Almacenamiento de datos SQL. |


## Artículos actualizados

En esta sección se enumeran los artículos que se han actualizado recientemente. Concretamente, aquellos en los que la actualización fuera grande o considerable. Para cada artículo actualizado, se muestra un fragmento del texto agregado. Los artículos se actualizaron dentro de este intervalo de fechas: del **26-07-2016** al **21-08-2016**.

| &nbsp; | Artículo | Texto actualizado, fragmento |
| --: | :-- | :-- |
| 3 | [Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell](sql-database-export-powershell.md) | $subscriptionId = "YOUR AZURE SUBSCRIPTION ID" Login-AzureRmAccount Set-AzureRmContext -SubscriptionId $subscriptionId Database to export $DatabaseName = "DATABASE-NAME" $ResourceGroupName = "RESOURCE-GROUP-NAME" $ServerName = "SERVER-NAME" $serverAdmin = "ADMIN-NAME" $serverPassword = "ADMIN-PASSWORD" $securePassword = ConvertTo-SecureString ΓÇôString $serverPassword ΓÇôAsPlainText -Force $creds = New-Object ΓÇôTypeName System.Management.Automation.PSCredential ΓÇôArgumentList $serverAdmin, $securePassword Generate a unique filename for the BACPAC $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac" Storage account info for the BACPAC $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/" $BacpacUri = $BaseStorageUri + $bacpacFilename $StorageKeytype = "StorageAccessKey" $StorageKey = "YOUR STORAGE KEY" $exportRequest = New-AzureRmSqlDatabaseExpo |
| 4 | [Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio](sql-database-service-tiers.md) | **Selección de un nivel de servicio** Para decidir sobre un nivel de servicio, empiece por determinar si la base de datos será una base de datos independiente o formará parte de un grupo elástico. **Selección de un nivel de servicio para una base de datos independiente** Para decidir sobre un nivel de servicio para una base de datos independiente, empiece por determinar las funciones de base de datos que necesita para elegir la edición d Base de datos SQL: /Tamaño de base de datos de tamaño (5 GB máximo para Básico, 250 GB máximo para Estándar y de 500 GB a 1 TB máximo para Premium: según el nivel de rendimiento)/ Periodo de retención de copia de seguridad de base de datos (7 días para Basic, 35 para Estándar y 35 días para Premium) Cuando haya determinado la edición de Base de datos SQL, estará preparado para determinar el nivel de rendimiento de la base de datos (el número de DTU). Este valor se puede estimar y, posteriormente, escalar o reducir verticalmente (sql-database-scale-up.md) en función de su experiencia real. También puede utilizar la (http://dtucalculator.azurewebsites.net/) para estimar el número aproximado de DTU que se necesitan. ** C |





## Primeros pasos

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 5 | [Creación de aplicaciones multiempresa con Base de datos SQL de Azure con aislamiento y eficiencia](sql-database-build-multi-tenant-apps.md) | Obtenga información sobre cómo crear aplicaciones multiinquilino con Base de datos SQL de Azure |
| 6 | [Conexión a Base de datos SQL con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md) | Aprenda a conectarse a Base de datos SQL en Azure con SQL Server Management Studio (SSMS). Después, ejecute una consulta de ejemplo con Transact-SQL (T-SQL). |
| 7 | [Conexión a Base de datos SQL mediante .NET (C#)](sql-database-develop-dotnet-simple.md) | Use el código de ejemplo de este inicio rápido para crear una aplicación moderna con C# con el respaldado de una base de datos relacional eficaz en la nube con la base de datos SQL de Azure. |
| 8 | [Creación de un nuevo grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-create-portal.md) | Cómo agregar un grupo de bases de datos elásticas escalables a la configuración de la Base de datos SQL para una administración y un uso compartido de los recursos más sencillos entre varias bases de datos. |
| 9 | [Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md) | Información sobre las funcionalidades y características de Base de datos SQL |
| 10 | [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md) | Obtenga información sobre cómo configurar un servidor lógico de Base de datos SQL, una regla de firewall del servidor, una Base de datos SQL y datos de ejemplo, así cómo conectarse con herramientas de cliente, configurar usuarios y una regla de firewall de base de datos. |
| 11 | [Azure SQL Database Secures and Protects (Seguridad y protección de Base de datos SQL de Azure)](sql-database-helps-secures-and-protects.md) | Obtenga más información acerca de cómo Base de datos SQL le puede ayudar y proteger |
| 12 | [Base de datos SQL de Azure aprende y se adapta](sql-database-learn-and-adapt.md) | Obtenga información sobre cómo Base de datos SQL aprende y se adapta |
| 13 | [Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Obtenga información acerca de qué opción de SQL Server en la nube se ajusta a su aplicación: Base de datos (PaaS) SQL de Azure o SQL Server en la nube en Máquinas virtuales de Azure. |
| 14 | [Escalas de Base de datos SQL de Azure sobre la marcha](sql-database-scale-on-the-fly.md) | Obtención de información sobre las escalas de Base de datos SQL sobre la marcha |
| 15 | [Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md) | Conozca las soluciones de Base de datos SQL de Azure. |
| 16 | [Base de datos SQL de Azure funciona en su entorno](sql-database-works-in-your-environment.md) | Obtenga más información acerca de cómo Base de datos SQL le puede ayudar, asegurar y proteger |



## Compilación de una aplicación

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 17 | [Acciones para solucionar problemas, diagnosticar y evitar errores de conexión y errores transitorios en Base de datos SQL](sql-database-connectivity-issues.md) | Aprenda a solucionar problemas, diagnosticar y evitar un error de conexión de SQL o un error transitorio en la Base de datos SQL de Azure. |
| 18 | [Conexión a una base de datos SQL con Visual Studio](sql-database-connect-query.md) | Escribir un programa en C# para realizar consultas y conectarse a Base de datos SQL. Información acerca de direcciones IP, cadenas de conexión, inicio de sesión seguro y Visual Studio gratuito. |
| 19 | [Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md) | Las conexiones de cliente de ADO.NET a Base de datos SQL de Azure V12 omiten al proxy e interactúan directamente con la base de datos. Los puertos que no sean 1433 se convierten en puertos importantes. |
| 20 | | [Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas.](sql-database-develop-error-messages.md) | Obtenga información acerca de los códigos de error de SQL de las aplicaciones cliente de la Base de datos SQL, como los errores de conexión de base de datos más comunes, los problemas de copia de la base de datos y los errores generales. |
| 21 | [Conexión a la base de datos SQL mediante PHP en Windows](sql-database-develop-php-simple.md) | Presenta un programa PHP de ejemplo que se conecta a la base de datos SQL de Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesarios que necesita el cliente. |
| 22 | [Prueba de Base de datos SQL: Use C# para crear una Base de datos SQL con la biblioteca de Base de datos SQL para .NET](sql-database-get-started-csharp.md) | Pruebe la Base de datos SQL para desarrollar aplicaciones SQL y C# y crear una Base de datos SQL de Azure con C# mediante la biblioteca de Base de datos SQL para. NET. |
| 23 | [Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure](sql-database-troubleshoot-common-connection-issues.md) | Pasos para identificar y resolver errores de conexión comunes para la base de datos SQL de Azure. |
| 24 | [Error "La base de datos en el servidor no está disponible en estos momentos" al conectar con la base de datos SQL](sql-database-troubleshoot-connection.md) | Solución de problemas del error "La base de datos en el servidor no está disponible en estos momentos", al conectar con la base de datos SQL. |



## Desarrollo

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 25 | [Obtención del identificador de cliente y la clave para conectarse a Base de datos SQL desde el código](sql-database-client-id-keys.md) | Obtenga el identificador de cliente y la clave para acceder a Base de datos SQL desde el código. |
| 26 | [Conexión a la base de datos SQL mediante Java con JDBC en Windows](sql-database-develop-java-simple.md) | Este tema muestra un ejemplo de código Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa JDBC y se ejecuta en un equipo cliente de Windows. |
| 27 | [Conexión a Base de datos SQL mediante Node.js](sql-database-develop-nodejs-simple.md) | Este tema muestra un ejemplo de código Node.js que puede usar para conectarse a la base de datos SQL de Azure. |
| 28 | [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md) | Aprenda sobre las bibliotecas de conectividad disponibles y los procedimientos recomendados para las aplicaciones que se conectan a Base de datos SQL. |
| 29 | [Conexión a Base de datos SQL mediante Python](sql-database-develop-python-simple.md) | Este tema muestra un ejemplo de código Pyton que puede usar para conectarse a la base de datos SQL de Azure. |
| 30 | [Conexión a Base de datos SQL mediante Ruby](sql-database-develop-ruby-simple.md) | Muestra de código Ruby que puede ejecutar en Windows para conectarse a Base de datos SQL de Azure. |
| 31 | [Introducción a las tablas temporales de Base de datos SQL de Azure](sql-database-temporal-tables.md) | Obtenga información sobre cómo empezar a usar las tablas temporales de Base de datos SQL de Azure |



## Rendimiento y escala

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 32 | [Asesor de Base de datos SQL](sql-database-advisor.md) | El Asesor de Base de datos SQL de Azure ofrece recomendaciones para las bases de datos SQL existentes que pueden mejorar el rendimiento actual de las consultas. |
| 33 | [Asesor de Base de datos SQL](sql-database-advisor-portal.md) | Puede utilizar el Asesor de Base de datos SQL de Azure en el Portal de Azure para revisar e implementar las recomendaciones para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual. |
| 34 | [Información general sobre la prueba comparativa Base de datos SQL de Azure](sql-database-benchmark-overview.md) | Este tema describe la prueba comparativa Base de datos SQL de Azure Benchmark, que se usa para la medición del rendimiento de la Base de datos SQL de Azure. |
| 35 | [¿Cuándo se debe utilizar un grupo de bases de datos elásticas?](sql-database-elastic-pool-guidance.md) | Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas. Este documento ofrece orientación para ayudarle a evaluar la idoneidad de usar un grupo de bases de datos elásticas para un grupo de base de datos. |
| 36 | [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) | Explicación básica de la característica de herramientas de base de datos elástica de Base de datos SQL de Azure, incluida una aplicación de ejemplo de ejecución sencilla. |
| 37 | [Preguntas más frecuentes sobre la Base de datos SQL](sql-database-faq.md) | Respuestas a las preguntas más comunes que los clientes preguntan sobre las bases de datos en la nube y Base de datos SQL de Azure, el sistema de administración de bases de datos relacionales (RDBMS) de Microsoft y bases de datos como un servicio en la nube. |
| 38 | [Introducción a In-Memory (vista previa) en Base de datos SQL](sql-database-in-memory.md) | Las tecnologías In-Memory de SQL mejoran notablemente el rendimiento de las cargas de trabajo de transacciones y de análisis. Aprenda a sacar partido de estas tecnologías. |
| 39 | [Uso de In-Memory OLTP (vista previa) para mejorar el rendimiento de la aplicación de Base de datos SQL de Azure](sql-database-in-memory-oltp-migration.md) | Adopción de In-Memory OLTP para mejorar el rendimiento transaccional en una Base de datos SQL ya existente. |
| 40 | [Supervisión del almacenamiento OLTP en memoria](sql-database-in-memory-oltp-monitoring.md) | Estimar y supervisar el uso y la capacidad del almacenamiento en memoria de XTP; resolver el error de capacidad 41823 |
| 41 | [Funcionamiento del almacén de consultas de Base de datos SQL de Azure](sql-database-operate-query-store.md) | Aprenda a utilizar el Almacén de consultas de Base de datos SQL de Azure. |
| 42 | [SQL Database Performance Insight (Información de rendimiento de Base de datos SQL)](sql-database-performance.md) | La Base de datos SQL de Azure ofrece herramientas de rendimiento para ayudarle a identificar áreas que pueden mejorar el rendimiento actual de las consultas. |
| 43 | [Guía de rendimiento de Base de datos SQL de Azure](sql-database-performance-guidance.md) | Este tema proporciona instrucciones para ayudarle a determinar qué nivel de servicio es el adecuado para su aplicación, así como recomendaciones para ajustarla y obtener el máximo partido de Base de datos SQL de Azure. |
| 44 | [Query Performance Insight de Base de datos SQL de Azure](sql-database-query-performance.md) | La supervisión del rendimiento de las consultas identifica las consultas que más CPU consumen en una base de datos SQL de Azure. |
| 45 | [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md) | El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos. Cambio del nivel de precios de una base de datos SQL de Azure. |
| 46 | [Supervisión de rendimiento de bases de datos con la Base de datos SQL de Azure](sql-database-single-database-monitor.md) | Conozca las opciones para la supervisión de la base de datos con herramientas de Azure y vistas de administración dinámica. |
| 47 | [Sugerencias para la optimización del rendimiento de Base de datos SQL](sql-database-troubleshoot-performance.md) | Sugerencias para la optimización del rendimiento de Base de datos SQL de Azure a través de la evaluación y la mejora. |
| 48 | [Uso del procesamiento por lotes para mejorar el rendimiento de las aplicaciones de Base de datos SQL](sql-database-use-batching-to-improve-performance.md) | El tema proporciona evidencia de que el procesamiento por lotes de las operaciones de base de datos mejora en gran medida la velocidad y la escalabilidad de las aplicaciones de Base de datos SQL de Azure. Aunque estas técnicas de procesamiento por lotes funcionan para cualquier base de datos de SQL Server, el artículo se centra en Azure. |



## Herramientas de escalado horizontal

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 49 | [Modelos de diseño para las aplicaciones SaaS multiinquilino y Base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | En este artículo se explican los requisitos y modelos de arquitectura de datos comunes de las aplicaciones de base de datos multiinquilino que se ejecutan en un entorno de nube que debe tener en cuenta y las distintas ventajas e inconvenientes de estos modelos. También se explica cómo ayuda Base de datos SQL de Azure con sus grupos de bases de datos elásticas y herramientas elásticas a afrontar estos requisitos de forma garantizada. |
| 50 | [Migrate existing databases to scale-out (Migrar bases de datos existentes de escalado horizontal)](sql-database-elastic-convert-to-use-elastic-tools.md) | Conversión de bases de datos particionadas para usar herramientas para bases de datos elásticas creando un administrador de mapas de particiones |
| 51 | [Creación de bases de datos escalables en la nube](sql-database-elastic-database-client-library.md) | Crear aplicaciones de bases de datos de .NET escalables con la Biblioteca de cliente de Base de datos elástica |
| 52 | [Creación de bases de datos escalables en la nube](sql-database-elastic-database-perf-counters.md) | Clase ShardMapManager y contadores de rendimiento de enrutamiento dependiente de los datos |
| 53 | [Incorporación de una partición con herramientas de bases de datos elásticas](sql-database-elastic-scale-add-a-shard.md) | Establece cómo usar las API de escala elástica para agregar particiones nuevas a un conjunto de particiones. |
| 54 | [Implemente un servicio de división y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | División y combinación con las herramientas de Base de datos elástica |
| 55 | [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) | Cómo usar la clase ShardMapManager en aplicaciones .NET para el enrutamiento dependiente de los datos, una característica de bases de datos elásticas para Base de datos SQL de Azure |
| 56 | [Preguntas frecuentes de herramientas de Base de datos elástica](sql-database-elastic-scale-faq.md) | Preguntas más frecuentes sobre el Escalado elástico de Base de datos SQL de Azure. |
| 57 | [Glosario de las herramientas de bases de datos elásticas](sql-database-elastic-scale-glossary.md) | Explicación de los términos usados en las herramientas de bases de datos elásticas |
| 58 | [Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md) | Los desarrolladores de Software como servicio (SaaS) pueden crear bases de datos elásticas y escalables con facilidad en la nube mediante estas herramientas |
| 59 | [Credenciales usadas para acceder a la biblioteca de cliente de bases de datos elásticas](sql-database-elastic-scale-manage-credentials.md) | Cómo configurar el nivel correcto de las credenciales (de administrador a solo lectura) de las aplicaciones de bases de datos elásticas. |
| 60 | [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md) | Ejecute consultas a través de particiones con la biblioteca de cliente de bases de datos elásticas. |
| 61 | [Moving data between scaled-out cloud databases (Mover datos entre bases de datos en la nube escaladas horizontalmente)](sql-database-elastic-scale-overview-split-and-merge.md) | Explica cómo manipular las particiones y mover los datos a través de un servicio autohospedado mediante las API de bases de datos elásticas. |
| 62 | [Escalado horizontal de las bases de datos mediante Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) | Cómo usar ShardMapManager, la biblioteca de cliente de bases de datos elásticas |
| 63 | [Configuración de seguridad de división y combinación](sql-database-elastic-scale-split-merge-security-configuration.md) | Configuración de certificados x409 para cifrado |
| 64 | [Actualización de una aplicación para usar la biblioteca de cliente de base de datos elástica más reciente](sql-database-elastic-scale-upgrade-client-library.md) | Actualización de aplicaciones y bibliotecas mediante NuGet |
| 65 | [Biblioteca de cliente de base de datos elástica con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Uso de la biblioteca de cliente de Base de datos elástica y Entity Framework para la codificación de bases de datos |
| 66 | [Uso de la biblioteca de cliente de bases de datos elásticas con Dapper](sql-database-elastic-scale-working-with-dapper.md) | Uso de la biblioteca de cliente de bases de datos elásticas con Dapper. |
| 67 | [Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Aprenda a usar herramientas de bases de datos elásticas junto con la seguridad de nivel de fila para crear una aplicación con un nivel de datos altamente escalable en Base de datos de SQL de Azure que admite particiones de varios inquilinos. |



## Trabajos elásticos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 68 | [Creación y administración de Bases de datos SQL de Azure escaladas horizontalmente con trabajos elásticos (versión preliminar)](sql-database-elastic-jobs-create-and-manage.md) | Siga los pasos necesarios de los procesos de creación y administración de un trabajo de base de datos elástica. |
| 69 | [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md) | usar trabajos de base de datos elástica |
| 70 | [Administración de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-jobs-overview.md) | Muestra el servicio de trabajo de base de datos elástica |
| 71 | [Creación y administración de un grupo de bases de datos SQL elásticas mediante PowerShell](sql-database-elastic-jobs-powershell.md) | PowerShell usada para administrar grupos de Base de datos SQL de Azure |
| 72 | [Información general sobre la instalación de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md) | Pasos de instalación de la característica de trabajo elástico. |
| 73 | [Desinstalación de componentes de Trabajos de base de datos elástica.](sql-database-elastic-jobs-uninstall.md) | Desinstalación de componentes de Trabajos de base de datos elástica |



## Grupos elásticos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 74 | [¿Qué es un grupo de bases de datos elásticas de Azure?](sql-database-elastic-pool.md) | Administre cientos o miles de bases de datos mediante un grupo. Un precio para un conjunto de unidades de rendimiento se puede distribuir por el grupo. Agregue o quite bases de datos a voluntad. |
| 75 | [Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md) | Use técnicas de desarrollo de bases de datos de C# para crear un grupo de bases de datos elásticas escalable en la Base de datos SQL de Azure, para así poder compartir recursos entre muchas bases de datos. |
| 76 | [Creación de un nuevo grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md) | Aprenda a usar PowerShell para escalar horizontalmente los recursos de la Base de datos SQL de Azure mediante la creación de un grupo de bases de datos elásticas escalable para administrar varias bases de datos. |
| 77 | [Desarrollo de bases de datos de C#: creación y configuración de un grupo de bases de datos elásticas para Base de datos SQL](sql-database-elastic-pool-csharp.md) | Use técnicas de desarrollo de bases de datos de C# para crear un grupo de bases de datos elásticas de la Base de datos SQL de Azure, para así poder compartir recursos entre una gran cantidad de bases de datos. |
| 78 | [PowerShell script for identifying databases suitable for an elastic database pool (Script de PowerShell para identificar bases de datos adecuadas para un grupo de bases de datos elásticas)](sql-database-elastic-pool-database-assessment-powershell.md) | Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas. Este documento ofrece un script de Powershell para ayudarle a evaluar la idoneidad de usar un grupo de bases de datos elásticas para un grupo de base de datos. |
| 79 | [Administración y cambio de tamaño de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-manage-csharp.md) | Use técnicas de desarrollo de bases de datos de C# para administrar un grupo de bases de datos elásticas de Base de datos SQL de Azure. |
| 80 | [Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-manage-portal.md) | Obtenga más información sobre cómo usar la inteligencia integrada de la Base de datos SQL y el Portal de Azure con el objetivo de administrar y supervisar un grupo de bases de datos elásticas escalables, así como identificar su tamaño más adecuado, con el objetivo de optimizar el rendimiento de las bases de datos y administrar los costos. |
| 81 | [Supervisión, administración y ajuste de tamaño de un grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-manage-powershell.md) | Obtenga más información sobre cómo usar PowerShell para administrar un grupo de bases de datos elásticas. |
| 82 | [Supervisión y administración de un grupo de bases de datos elásticas con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Use T-SQL para crear una Base de datos SQL de Azure en un grupo elástico. O bien, use T-SQL para mover la base de datos dentro y fuera de los grupos. |
| 83 | [Información de precios y facturación de grupos de bases de datos elásticas](sql-database-elastic-pool-price.md) | Información sobre precios de grupos de bases de datos elásticas. |



## Consulta elástica

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 84 | [Informes de bases de datos escaladas horizontalmente en la nube (versión preliminar)](sql-database-elastic-query-getting-started.md) | cómo utilizar consultas de bases de datos cruzadas |
| 85 | [Introducción a las consultas entre bases de datos (particiones verticales) (versión preliminar)](sql-database-elastic-query-getting-started-vertical.md) | cómo usar la consulta de base de datos elástica con bases de datos con particiones verticales |
| 86 | [Informes de bases de datos escaladas horizontalmente en la nube (vista previa)](sql-database-elastic-query-horizontal-partitioning.md) | Configuración de las consultas elásticas en particiones horizontales |
| 87 | [Información general sobre la consulta de bases de datos elásticas de Base de datos SQL de Azure (vista previa)](sql-database-elastic-query-overview.md) | Información general de la característica de consultas elásticas |
| 88 | [Consulta de bases de datos elásticas para consultas entre bases de datos (particionamiento vertical)](sql-database-elastic-query-vertical-partitioning.md) | cómo configurar consultas entre bases de datos en particiones verticales |



## Transacción elástica

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 89 | [Introducción sobre las transacciones de base de datos elástica con Base de datos SQL de Azure](sql-database-elastic-transactions-overview.md) | Introducción sobre las transacciones de base de datos elástica con Base de datos SQL de Azure |



## Copia de seguridad y recuperación

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 90 | [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md) | Obtenga más información sobre las copias de seguridad integradas de Base de datos SQL que permiten revertir una Base de datos SQL de Azure a un momento dado previo o copiarla en una nueva base de datos en una región geográfica (hasta 35 días). |
| 91 | [Introducción a la continuidad empresarial con Base de datos SQL de Azure](sql-database-business-continuity.md) | Obtenga información acerca de cómo la Base de datos SQL de Azure permite la continuidad del negocio en la nube y la recuperación de la base de datos, y ayuda a que las aplicaciones críticas de la nube se sigan ejecutando. |
| 92 | [Restauración de una única tabla a partir de una copia de seguridad de Base de datos SQL de Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Obtenga información sobre cómo restaurar una única tabla de la copia de seguridad de Base de datos SQL de Azure. |
| 93 | [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Obtenga información acerca de cómo diseñar soluciones de recuperación ante desastres en la nube para planificar la continuidad del negocio mediante la replicación geográfica de la copia de seguridad de datos de la aplicación con la Base de datos SQL de Azure. |
| 94 | [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md) | Obtenga información acerca de cómo recuperar una base de datos tras un error o una interrupción de un centro de datos regional con las funcionalidades de replicación geográfica activa y restauración geográfica de Base de datos SQL de Azure. |
| 95 | [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md) | Obtenga instrucciones e información sobre prácticas recomendadas acerca del uso de la base de datos SQL de Azure para la realización de tareas de obtención de detalles de la recuperación ante desastres. Dichas tareas le ayudarán a mantener la capacidad de recuperación ante errores y fallos de las aplicaciones de negocio críticas. |
| 96 | [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Aprenda a diseñar la solución de nube para recuperación ante desastres eligiendo el modelo correcto de conmutación por error. |
| 97 | [Uso de la clase RecoveryManager para solucionar problemas de mapas de particiones](sql-database-elastic-database-recovery-manager.md) | Uso de la clase RecoveryManager para solucionar problemas con los mapas de particiones |
| 98 | [Importar un archivo BACPAC para crear una base de datos SQL de Azure](sql-database-import.md) | Crear una base de datos SQL de Azure importando un archivo BACPAC ya existente. |
| 99 | [Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure](sql-database-point-in-time-restore-portal.md) | Restauración de una base de datos SQL de Azure a un momento dado anterior. |
| 100 | [Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell](sql-database-point-in-time-restore-powershell.md) | Restauración de una base de datos SQL de Azure a un momento dado anterior |
| 101 | [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md) | Restauración a un momento dado, base de datos de Microsoft Azure SQL, restaurar base de datos, recuperar base de datos, Portal de Azure clásico, Portal de Azure clásico |
| 102 | [Recuperación de una Base de datos SQL de Azure mediante copias de seguridad automatizadas](sql-database-recovery-using-backups.md) | Obtenga información acerca de la restauración a un momento dado, que le permite revertir una Base de datos SQL de Azure a un momento dado anterior (hasta 35 días). |
| 103 | [Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure](sql-database-restore-deleted-database-portal.md) | Restauración de una base de datos SQL de Azure eliminada (Portal de Azure). |
| 104 | [Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell](sql-database-restore-deleted-database-powershell.md) | Restauración de una Base de datos SQL de Azure eliminada (PowerShell) |
| 105 | [Restaurar una base de datos a un momento anterior en el tiempo, restaurar una base de datos eliminada o recuperarse de una interrupción del centro de datos](sql-database-troubleshoot-backup-and-restore.md) | Aprenda a recuperar una base de datos de nube de errores e interrupciones mediante copias de seguridad y réplicas de Base de datos SQL de Azure. |



## Migrar

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 106 | [Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos,exportación de archivo BACPAC, sqlpackage |
| 107 | [Exportación de una base de datos SQL Server a un archivo BACPAC mediante SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos, exportación de archivo BACPAC, asistente para exportación de aplicaciones de capa de datos |
| 108 | [Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, importación de archivo BACPAC, sqlpackage |
| 109 | [Importar desde BACPAC a la Base de datos SQL con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración |
| 110 | [Migrar la base de datos de SQL Server a Base de datos SQL con el Asistente para implementar base de datos en Base de datos de Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, Asistente para bases de datos de Microsoft Azure |
| 111 | [Migración de una base de datos SQL Server a Base de datos SQL de Azure mediante la replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, replicación transaccional |
| 112 | [Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, compatibilidad de Base de datos SQL, SqlPackage |
| 113 | [Uso de SQL Server Management Studio para determinar la compatibilidad con bases de datos SQL Server antes de realizar la migración a Base de datos SQL de Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad de Base de datos SQL, Asistente para exportación de aplicaciones de capa de datos |
| 114 | [Uso del Asistente para migración de SQL Azure para corregir problemas de compatibilidad con bases de datos SQL Server antes de realizar la migración a Base de datos SQL de Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure |
| 115 | [Migración de una base de datos SQL Server a Base de datos SQL de Azure mediante SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure, SSDT |
| 116 | [Solución de problemas de compatibilidad de bases de datos de SQL Server mediante SQL Server Management Studio antes de la migración a la Base de datos SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure |
| 117 | [Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell](sql-database-export-powershell.md) | Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell |
| 118 | [Importar un archivo BACPAC para crear una nueva base de datos de SQL Azure con PowerShell](sql-database-import-powershell.md) | Importar un archivo BACPAC para crear una nueva base de datos de SQL Azure con PowerShell |
| 119 | [Carga de datos desde CSV en Almacenamiento de datos SQL de Azure (archivos planos)](sql-database-load-from-csv-with-bcp.md) | Para un tamaño de datos pequeño, utiliza bcp para importar datos en Base de datos SQL de Azure. |
| 120 | [Movimiento de bases de datos de un servidor a otro, de una suscripción a otra, y dentro y fuera de Azure](sql-database-troubleshoot-moving-data.md) | Pasos rápidos para copiar, mover y migrar datos y bases de datos en Base de datos SQL de Azure. |



## Introducción y extracción de datos

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 121 | [Migración de una base de datos de SQL Server a una Base de datos SQL en la nube](sql-database-cloud-migrate.md) | Obtenga más información acerca de la migración de una base de datos de SQL Server local a una Base de datos SQL de Azure en la nube. Antes de migrar una base de datos, use herramientas de migración de bases de datos para determinar la compatibilidad. |
| 122 | [Copiar una base de datos SQL de Azure](sql-database-copy.md) | Creación de una copia de una base de datos SQL de Azure |
| 123 | [Archivar una base de datos de SQL Azure en un archivo BACPAC mediante el Portal de Azure](sql-database-export.md) | Archivar una base de datos de SQL Azure en un archivo BACPAC mediante el Portal de Azure |



## Seguridad

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 124 | [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md) | Aprenda a conectarse a una Base de datos SQL mediante autenticación de Azure Active Directory |
| 125 | [Always Encrypted: protección de los datos confidenciales en Base de datos SQL y almacenamiento de las claves de cifrado en el almacén de certificados de Windows](sql-database-always-encrypted.md) | Proteger datos confidenciales en la base de datos SQL en cuestión de minutos. |
| 126 | [Always Encrypted: protección de datos confidenciales en Base de datos SQL y almacenamiento de las claves de cifrado en Almacén de claves de Azure](sql-database-always-encrypted-azure-key-vault.md) | Proteger datos confidenciales en la base de datos SQL en cuestión de minutos. |
| 127 | [Base de datos SQL: compatibilidad con clientes de nivel inferior y cambios de punto de conexión IP para auditoría](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Más información sobre Compatibilidad de clientes de nivel inferior de Base de datos SQL y cambios de punto de conexión IP para auditoría. |
| 128 | [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell](sql-database-configure-firewall-settings-powershell.md) | Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure. |
| 129 | [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST](sql-database-configure-firewall-settings-rest.md) | Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure. |
| 130 | [Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md) | Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure. |
| 131 | [Introducción al enmascaramiento de datos dinámicos de bases de datos SQL (Portal de Azure)](sql-database-dynamic-data-masking-get-started.md) | Cómo empezar a usar el enmascaramiento de datos dinámicos de Base de datos SQL en el Portal de Azure |
| 132 | [Introducción al enmascaramiento de datos dinámicos de Base de datos SQL (Portal de Azure clásico)](sql-database-dynamic-data-masking-get-started-portal.md) | Cómo empezar a usar el enmascaramiento de datos dinámicos de Base de datos SQL en el Portal de Azure clásico |
| 133 | [Configuración de reglas de firewall de Base de datos SQL de Azure: información general](sql-database-firewall-configure.md) | Obtenga información acerca de cómo configurar un firewall de base de datos SQL mediante las reglas de firewall de nivel de servidor y de nivel de base de datos, para administrar el acceso. |
| 134 | [SQL Database tutorial: Create SQL database user accounts to access and manage a database (Tutorial de Base de datos SQL: Creación de cuentas de base de datos SQL para acceder a una base de datos y administrarla)](sql-database-get-started-security.md) | Aprenda a crear cuentas de usuario para acceder a una base de datos y administrarla. |
| 135 | [Autorización y autenticación de Base de datos SQL: concesión de acceso](sql-database-manage-logins.md) | Obtenga información acerca de la administración de seguridad de la Base de datos SQL, específicamente el modo de administrar la seguridad del inicio de sesión y el acceso a la base de datos con la cuenta de entidad de seguridad a nivel de servidor. |
| 136 | [Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure](sql-database-security-guidelines.md) | Obtenga más información acerca de las instrucciones y limitaciones de la Base de datos SQL de Microsoft Azure relacionadas con la seguridad. |
| 137 | [Introducción a Detección de amenazas de Base de datos SQL](sql-database-threat-detection-get-started.md) | Cómo empezar a usar Detección de amenazas de Base de datos SQL en el Portal de Azure |
| 138 | [Realización de tareas administrativas comunes como el restablecimiento de la contraseña de administrador en Base de datos SQL de Azure](sql-database-troubleshoot-permissions.md) | Describe como realizar tareas administrativas comunes en Base de datos SQL de Azure. Por ejemplo, restablecer la contraseña de administrador, conceder y revocar el acceso. |



## Administración de la base de datos

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 139 | [Introducción a la auditoría de bases de datos SQL](sql-database-auditing-get-started.md) | Introducción a la auditoría de bases de datos SQL |
| 140 | [Configuración de un firewall de nivel de servidor en Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md) | Descubra cómo configurar el firewall para direcciones IP que accedan al servidor SQL de Azure. |
| 141 | [Copia de una instancia de Base de datos SQL de Azure mediante el Portal de Azure](sql-database-copy-portal.md) | Creación de una copia de una base de datos SQL de Azure |
| 142 | [Administración de bases de datos SQL de Azure mediante Automatización de Azure](sql-database-manage-automation.md) | Obtenga información acerca de cómo puede usarse el servicio Automatización de Azure para administrar bases de datos SQL de Azure a escala. |
| 143 | [Información general: herramientas de administración para Base de datos SQL](sql-database-manage-overview.md) | Compara las herramientas y opciones para administrar Base de datos SQL de Azure |
| 144 | [Supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md) | Obtenga información sobre cómo detectar y diagnosticar problemas comunes de rendimiento con vistas de administración dinámica para supervisar Base de datos SQL de Microsoft Azure. |
| 145 | [Protección de bases de datos SQL](sql-database-security.md) | Obtenga información acerca de la seguridad de la Base de datos SQL de Azure y SQL Server, incluidas las diferencias entre la nube y SQL Server local en cuanto a la autenticación, autorización, seguridad de conexión, cifrado y cumplimiento normativo. |



## Eventos extendidos

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 146 | [Código de destino del archivo de evento para eventos extendidos en Base de datos SQL](sql-database-xevent-code-event-file.md) | Brinda PowerShell y Transact-SQL para un ejemplo de código de dos fases que muestra el destino de archivo de evento en un evento extendido en Base de datos SQL de Azure. Almacenamiento de Azure es una parte necesaria en este escenario. |
| 147 | [Código de destino de búfer en anillo para eventos extendidos en Base de datos SQL](sql-database-xevent-code-ring-buffer.md) | Proporciona un ejemplo de código de Transact-SQL más fácil y rápido mediante el uso del destino de Búfer de anillo, en Base de datos SQL de Azure. |
| 148 | [Eventos extendidos en Base de datos SQL](sql-database-xevent-db-diff-from-svr.md) | Describe los eventos extendidos (XEvents) en Base de datos SQL de Azure y cómo las sesiones de eventos difieren ligeramente de las sesiones de eventos en Microsoft SQL Server. |



## Replicación geográfica

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 149 | [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-failover-portal.md) | Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure |
| 150 | [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) | Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell |
| 151 | [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL |
| 152 | [Información general: Replicación geográfica activa para Base de datos SQL de Azure](sql-database-geo-replication-overview.md) | La replicación geográfica activa permite configurar cuatro réplicas de la base de datos en cualquiera de los centros de datos de Azure. |
| 153 | [Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md) | Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure |
| 154 | [Configuración de la replicación geográfica para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-powershell.md) | Configuración de la replicación geográfica activa para Base de datos SQL de Azure con PowerShell |
| 155 | [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) | En este tema se explican las consideraciones de seguridad que hay que tener en cuenta para administrar la seguridad después de restaurar una base de datos o de conmutarla por error. |
| 156 | [Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL |
| 157 | [Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure](sql-database-geo-restore-portal.md) | Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica (Portal de Azure) |
| 158 | [Restauración geográfica de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante PowerShell](sql-database-geo-restore-powershell.md) | Restauración de una Base de datos SQL de Azure en un nuevo servidor a partir de una copia de seguridad con redundancia geográfica |



## Actualizar

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 159 | [Rendimiento mejorado de consultas con el nivel de compatibilidad 130 en Base de datos SQL de Azure](sql-database-compatibility-level-query-performance-130.md) | Pasos y herramientas para determinar qué nivel de compatibilidad es más adecuado para su base de datos en Base de datos SQL de Azure o Microsoft SQL Server |
| 160 | [Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de Base de datos SQL](sql-database-manage-application-rolling-upgrade.md) | Aprenda a usar la replicación geográfica en Base de datos SQL para admitir las actualizaciones en línea de la aplicación en la nube. |
| 161 | [Actualización a la base de datos SQL V12 de Azure con el Portal de Azure](sql-database-upgrade-server-portal.md) | Explicación acerca de cómo actualizar a la Base de datos SQL V12 de Azure, incluido cómo actualizar las bases de datos de tipo Web y Business y cómo actualizar un servidor V11 migrando sus bases de datos directamente a un grupo de bases de datos elásticas mediante el Portal de Azure. |
| 162 | [Actualización a la Base de datos SQL V12 de Azure mediante PowerShell](sql-database-upgrade-server-powershell.md) | Explicación acerca de cómo actualizar a la Base de datos SQL V12 de Azure, incluyendo cómo actualizar las bases de datos de tipo Web y Business y cómo actualizar un servidor V11 migrando sus bases de datos directamente a un grupo de bases de datos elásticas mediante PowerShell. |
| 163 | [Planeación y preparación para realizar la actualización a Base de datos SQL V12](sql-database-v12-plan-prepare-upgrade.md) | Describe los preparativos y las limitaciones que conlleva la actualización a la versión V12 de Base de datos SQL de Azure. |
| 164 | [Novedades de Base de datos SQL V12](sql-database-v12-whats-new.md) | Describe cuáles son las ventajas que van a obtener los sistemas empresariales que están utilizando Base de datos SQL de Azure en la nube al actualizarse a la versión V12 ahora. |
| 165 | [Preguntas frecuentes relacionadas con la retirada de las versiones Web y Business](sql-database-web-business-sunset-faq.md) | Obtenga información sobre cuándo se retirarán las bases de datos SQL de Azure Web y Business y sobre las características y funciones de los nuevos niveles de servicio. |



## Herramientas

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 166 | [Administración de Base de datos SQL de Azure con PowerShell](sql-database-command-line-tools.md) | Administración de Base de datos SQL de Azure con PowerShell. |
| 167 | [Tutorial de Base de datos SQL: conexión de Excel a una Base de datos SQL de Azure y creación de un informe](sql-database-connect-excel.md) | Más información acerca de cómo conectar Microsoft Excel a Base de datos SQL de Azure en la nube. Importación de datos en Excel para la generación de informes y la exploración de datos. |
| 168 | [Creación de una nueva Base de datos SQL y realización de tareas comunes de configuración de base de datos con los cmdlets de PowerShell](sql-database-get-started-powershell.md) | Aprenda a crear una Base de datos SQL con PowerShell. Las tareas de configuración comunes de la base de datos pueden administrarse mediante los cmdlets de PowerShell. |
| 169 | [Introducción a SQL Data Sync de Azure (vista previa)](sql-database-get-started-sql-data-sync.md) | Este tutorial le ayuda a comenzar con SQL Data Sync de Azure (vista previa). |
| 170 | [Administración de Base de datos SQL de Azure con el uso de SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Obtenga información acerca de cómo usar SQL Server Management Studio para administrar bases de datos y servidores de bases de datos SQL. |
| 171 | [Administración de Bases de datos SQL de Azure con el Portal de Azure](sql-database-manage-portal.md) | Aprenda a usar el Portal de Azure para administrar una base de datos relacional en la nube mediante el Portal de Azure. |
| 172 | [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL con PowerShell](sql-database-scale-up-powershell.md) | El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos mediante PowerShell. Cambio del nivel de precios de una base de datos SQL de Azure mediante PowerShell. |
| 173 | [Utilización de SQL Server Management Studio en Azure RemoteApp para conectarse a una base de datos SQL](sql-database-ssms-remoteapp.md) | Use este tutorial para aprender a utilizar SQL Server Management Studio en Azure RemoteApp y optimizar la seguridad y el rendimiento al conectarse a la Base de datos SQL |



## Referencia

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 174 | [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md) | Muestra el número de versión mínimo para cada controlador que pueden usar los programas cliente para conectarse a Base de datos SQL de Azure o a Microsoft SQL Server. Se proporciona un vínculo con información acerca de los controladores publicados por la comunidad y no por Microsoft. |
| 175 | [Límites de recursos de Base de datos SQL](sql-database-resource-limits.md) | En esta página se describen algunos límites de recursos comunes para Base de datos SQL de Azure. |
| 176 | [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md) | Instrucciones de Transact-SQL que no son totalmente compatibles con la Base de datos SQL de Azure |



## Varios

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 177 | [Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) | Crear copia de una base de datos SQL de Azure con PowerShell |
| 178 | [Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md) | Crear una copia de una base de datos SQL de Azure con Transact-SQL |
| 179 | [Instrucciones y limitaciones generales de Base de datos SQL de Azure](sql-database-general-limitations.md) | En esta página se describen algunas limitaciones generales para Base de datos SQL de Azure, así como las áreas de interoperatividad y compatibilidad. |
| 180 | [Recomendaciones sobre el nivel de precios de Base de datos SQL](sql-database-service-tier-advisor.md) | Si se cambian los niveles de precios en el Portal de Azure, se recomienda el nivel más apropiado para ejecutar la carga de trabajo de una Base de datos SQL de Azure existente. Los niveles de precios describen el nivel de servicio y el nivel de rendimiento de una base de datos SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## Extras

<a name="extra_related_articles"></a>

### Artículos relacionados de otros servicios de Azure

- [Back up your Azure App Services app in Azure (Hacer copia de seguridad de su aplicación de Servicios de aplicaciones de Azure en Azure)](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### Herramientas de documentación

- [Updates announced for Azure SQL Database (Actualizaciones anunciadas en Base de datos SQL de Azure)](http://azure.microsoft.com/updates/?service=sql-database)

- [Search all the documentation types of Microsoft Azure, with filters (Buscar todos los tipos de documentación de Microsoft Azure, con filtros)](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### Gráficos de la ruta de aprendizaje

- [Learning Path graphics for all Microsoft Azure services (Gráficos de la ruta de aprendizaje para todos los servicios de Microsoft Azure)](http://azure.microsoft.com/documentation/learning-paths/)

- [Learning Path: Learn Azure SQL Database (Ruta de aprendizaje: aprender sobre Base de datos SQL de Azure)](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Learning Path: SQL Database elastic scale (Ruta de aprendizaje: escala elástica de Base de datos SQL)](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->

<!---HONumber=AcomDC_0824_2016-->