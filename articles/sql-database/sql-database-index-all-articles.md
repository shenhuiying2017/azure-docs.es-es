---
title: Todos los temas del servicio Base de datos SQL | Microsoft Docs
description: Tabla de todos los temas del servicio Base de datos SQL que se encuentra en http://azure.microsoft.com/documentation/articles/, con títulos y descripciones.
services: sql-database
documentationcenter: ''
author: MightyPen
manager: jhubbard
editor: MightyPen

ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: genemi

---
# <a name="all-topics-for-azure-sql-database-service"></a>Todos los temas del servicio Base de datos SQL de Azure
En este documento se muestran todos los temas relacionados directamente con el servicio **Base de datos SQL** de Azure. Puede buscar en esta página web las palabras clave mediante **Ctrl+F**, para encontrar los temas de interés.

## <a name="new"></a>Nuevo
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 1 |[Daxko y CSI utilizan Azure para acelerar su ciclo de desarrollo, aumentar su rendimiento y mejorar sus servicios de atención al cliente](sql-database-implementation-daxko.md) |Sepa cómo Daxko y CSI utilizan SQL Database para acelerar su ciclo de desarrollo, aumentar su rendimiento y mejorar sus servicios de atención al cliente. |
| 2 |[Azure brinda a GEP alcance global y mayor eficacia](sql-database-implementation-gep.md) |Sepa cómo GEP utiliza SQL Database para llegar a más clientes internacionales y lograr una mayor eficacia. |
| 3 |[Con Azure, SnelStart ha expandido rápidamente sus servicios empresariales con una cifra de 1000 nuevas bases de datos SQL de Azure cada mes](sql-database-implementation-snelstart.md) |Sepa cómo SnelStart usa SQL Database para expandir rápidamente sus servicios empresariales con una cifra de 1000 nuevas bases de datos SQL de Azure cada mes. |
| 4 |[Umbraco utiliza Azure SQL Database para aprovisionar y escalar rápidamente servicios para miles de inquilinos en la nube](sql-database-implementation-umbraco.md) |Sepa cómo Umbraco utiliza SQL Database para aprovisionar y escalar rápidamente servicios para miles de inquilinos en la nube. |
| 5 |[Administración de Azure SQL Database con PowerShell](sql-database-manage-powershell.md) |Administración de Base de datos SQL de Azure con PowerShell. |
| 6 |[Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL](sql-database-ssms-mfa-authentication.md) |Use Multi-Factor Authentication con SSMS para Base de datos SQL y Almacenamiento de datos SQL. |
| 7 |[Descripción de las unidades de transacción de bases de datos (DTU) y de las unidades de transacción de bases de datos elásticas (eDTU)](sql-database-what-is-a-dtu.md) |Comprender lo que es una unidad de transacción de Azure SQL Database. |

## <a name="updated-articles,-sql-database"></a>Artículos actualizados, SQL Database
En esta sección se enumeran los artículos que se han actualizado recientemente. Concretamente, aquellos en los que la actualización fuera grande o considerable. Para cada artículo actualizado, se muestra un fragmento del texto agregado. Los artículos se actualizaron dentro de este intervalo de fechas: del **22-08-2016** al **05-10-2016**.

| &nbsp; | Artículo | Texto actualizado, fragmento | Se actualiza cuando |
| ---:|:--- |:--- |:--- |
| 8 |[Administración de Base de datos SQL de Azure con PowerShell](sql-database-command-line-tools.md) |Cree un grupo de recursos para nuestra instancia de SQL Database y los recursos relacionados de Azure con el cmdlet New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). * $resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation * Para más información, vea  Uso de Azure PowerShell con Azure Resource Manager (../powershell-azure-resource-manager.md). Para un script ejemplo, consulte Creación de un script de PowerShell de SQL Database (sql-database-get-started-powershell.md create-a-sql-database-powershell-script). ** Creación de un servidor de SQL Database** Cree un servidor de SQL Database con  New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet. Reemplace *server1* por el nombre del servidor. Los nombres de servidores deben ser único en todos los servidores de Azure SQL Database. Recibirá un mensaje de error si ya existe el nombre del servidor. Este comando puede tardar varios minutos en completarse. ¿Cuál es el resultado? |2016-09-14 |
| 9 |[Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) |SQL database source (the existing database to copy)  --  $sourceDbName = "db1"  $sourceDbServerName = "server1"  $sourceDbResourceGroupName = "rg1"  SQL database copy (the new db to be created)  --  $copyDbName = "db1_copy"  $copyDbServerName = "server2"  $copyDbResourceGroupName = "rg2"  Copy a database to the same server  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName  Copy a database to a different server  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName  Copy a database into an elastic database pool  --  $poolName = "pool1"  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyReso |2016-09-08 |
| 10 |[Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md) |Console.WriteLine("Server firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);  Console.WriteLine("Elastic pool...");  ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  Console.WriteLine("Database: " + dbr.Database.Id);  Console.WriteLine("Press any key to continue...");  Console.ReadKey();  }  static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa |2016-09-14 |
| 11 |[PowerShell script for identifying databases suitable for an elastic database pool (Script de PowerShell para identificar bases de datos adecuadas para un grupo de bases de datos elásticas)](sql-database-elastic-pool-database-assessment-powershell.md) |** Para los candidatos de grupo de bases de datos elásticas, se excluyen las maestras y las bases de datos que ya están en un grupo. Puede agregar más bases de datos a la lista excluida siguiente según sea necesario** $ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.') 0  -ResourceGroupName $ResourceGroupName  /  Where-Object {$*.DatabaseName -notin ("master") -and $*.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $*.CurrentServiceObjectiveName -notin ("ElasticPool")} $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword" $destinationTableName = "resource_stats_output" ** Create a table in output database for metrics collection** $sql = " IF  NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U')) BEGIN Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg* |2016-09-29 |
| 12 |[Tutorial de SQL Database: creación de una SQL Database en cuestión de minutos con datos de ejemplo y Azure Portal](sql-database-get-started.md) |! Nueva SQL Database 1 (./media/sql-database-get-started/sql-database-new-database-1.png) 3. Haga clic en **Base de datos SQL** para abrir la hoja Base de datos SQL. El contenido de esta hoja varía según el número de suscripciones y los objetos existentes (por ejemplo, los servidores existentes).  ! Nueva SQL Database 2 (./media/sql-database-get-started/sql-database-new-database-2.png) 4. En el cuadro de texto **Nombre de la base de datos** , escriba un nombre para la primera base de datos; por ejemplo, "mi-base-de-datos". Una marca de verificación verde indica que ha proporcionado un nombre válido.  ! Nueva SQL Database 3 (./media/sql-database-get-started/sql-database-new-database-3.png) 5. Si tiene varias suscripciones, seleccione una. 6. En **Grupo de recursos**, haga clic en **Crear nuevo** y proporcione un nombre para el primer grupo de recursos - por ejemplo; por ejemplo, "mi-grupo-de-recursos". Una marca de verificación verde indica que ha proporcionado un nombre válido.  ! Nueva SQL Database 4 (./media/sql-database-get-started/sql-database-new-database-4.png) 7. En **Seleccionar origen* |2016-09-08 |
| 13 |[Prueba de Base de datos SQL: Use C# para crear una Base de datos SQL con la biblioteca de Base de datos SQL para .NET](sql-database-get-started-csharp.md) |** Creación de una entidad de servicio para obtener acceso a recursos** El siguiente script de PowerShell crea la aplicación de Active Directory (AD) y la entidad de servicio que se necesitan para autenticar la aplicación de C. En la salida del script, se encuentran los valores que se necesitan para el anterior ejemplo de C. Para ver información detallada, consulte Uso de Azure PowerShell para crear una entidad de servicio para obtener acceso a recursos  (../resource-group-authenticate-service-principal.md).  Inicie sesión en Azure.  Add-AzureRmAccount  Si tiene varias suscripciones, quite la marca de comentario y establezca la suscripción con la que quiera trabajar.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"  Set-AzureRmContext -SubscriptionId $subscriptionId  Proporcione estos valores para la nueva aplicación AAD.  $appName es el nombre para mostrar de la aplicación. Debe ser exclusivo en el directorio.  $uri no tiene que ser un URI real.  $secret es una contraseña que crea.  $appName = "{app-name}"  $uri = "http://{app-name}"  $secret = "{app-password}"  Crear una aplicación AAD  $azureAdApplication = New-AzureRmADApp |2016-09-23 |
| 14 |[Administración de Bases de datos SQL de Azure con el Portal de Azure](sql-database-manage-portal.md) |Para ver o actualizar la configuración de la base de datos, haga clic en la opción que desee en la hoja de SQL Database: ! Configuración de SQL Database (./media/sql-database-manage-portal/settings.png) ** ¿Cómo se puede encontrar el nombre completo de servidor de las SQL Database?** Para ver el nombre de servidor de las bases de datos, haga clic en **Introducción** en la hoja **SQL Database** y anote el nombre del servidor: ! Configuración de SQL Database (. / media/sql-database-manage-portal/server-name.png) ** ¿Cómo se administran las reglas de firewall para controlar el acceso a mi SQL Server y Database?** Para ver, crear, o actualizar las reglas de firewall, haga clic en **Establecer firewall de servidor** en la hoja **SQL Database**. Para más información, consulte Configuración de un firewall de nivel de servidor en Azure SQL Database mediante Azure Portal (sql-database-configure-firewall-settings.md). ! reglas de firewall (. / media/sql-database-manage-portal/sql-database-firewall.png) ** ¿Cómo se cambia el nivel de rendimiento o de servicio de mi SQL Database?** Para actualizar el nivel de rendimiento o de servicio de una SQL Database, haga clic en **Plan de tarifa (s |2016-09-20 |

## <a name="get-started"></a>Primeros pasos
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 15 |[Creación de aplicaciones multiempresa con Base de datos SQL de Azure con aislamiento y eficiencia](sql-database-build-multi-tenant-apps.md) |Obtenga información sobre cómo crear aplicaciones multiinquilino con Base de datos SQL de Azure |
| 16 |[Conexión a Base de datos SQL con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md) |Aprenda a conectarse a Base de datos SQL en Azure con SQL Server Management Studio (SSMS). Después, ejecute una consulta de ejemplo con Transact-SQL (T-SQL). |
| 17 |[Conexión a Base de datos SQL mediante .NET (C#)](sql-database-develop-dotnet-simple.md) |Use el código de ejemplo de este inicio rápido para crear una aplicación moderna con C# con el respaldado de una base de datos relacional eficaz en la nube con la base de datos SQL de Azure. |
| 18 |[Creación de un nuevo grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-create-portal.md) |Cómo agregar un grupo de bases de datos elásticas escalables a la configuración de la Base de datos SQL para una administración y un uso compartido de los recursos más sencillos entre varias bases de datos. |
| 19 |[Exploración de tutoriales de Base de datos SQL de Azure](sql-database-explore-tutorials.md) |Información sobre las funcionalidades y características de Base de datos SQL |
| 20 | |[Tutorial de SQL Database: creación de una SQL Database en cuestión de minutos con datos de ejemplo y Azure Portal](sql-database-get-started.md) |
| 21 |[Azure SQL Database Secures and Protects (Seguridad y protección de Base de datos SQL de Azure)](sql-database-helps-secures-and-protects.md) |Obtenga más información acerca de cómo Base de datos SQL le puede ayudar y proteger |
| 22 |[Azure SQL Database aprende y se adapta](sql-database-learn-and-adapt.md) |Obtenga información sobre cómo Base de datos SQL aprende y se adapta |
| 23 |[Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) |Obtenga información acerca de qué opción de SQL Server en la nube se ajusta a su aplicación: Base de datos (PaaS) SQL de Azure o SQL Server en la nube en Máquinas virtuales de Azure. |
| 24 |[Escalas de Base de datos SQL de Azure sobre la marcha](sql-database-scale-on-the-fly.md) |Obtención de información sobre las escalas de Base de datos SQL sobre la marcha |
| 25 |[Exploración de tutoriales de soluciones de Base de datos SQL de Azure](sql-database-solution-quick-starts.md) |Conozca las soluciones de Base de datos SQL de Azure. |
| 26 |[Base de datos SQL de Azure funciona en su entorno](sql-database-works-in-your-environment.md) |Obtenga más información acerca de cómo Base de datos SQL le puede ayudar, asegurar y proteger |

## <a name="build-an-app"></a>Compilación de una aplicación
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 27 |[Acciones para solucionar problemas, diagnosticar y evitar errores de conexión y errores transitorios en Base de datos SQL](sql-database-connectivity-issues.md) |Aprenda a solucionar problemas, diagnosticar y evitar un error de conexión de SQL o un error transitorio en la Base de datos SQL de Azure. |
| 28 |[Conexión a una base de datos SQL con Visual Studio](sql-database-connect-query.md) |Escribir un programa en C# para realizar consultas y conectarse a Base de datos SQL. Información acerca de direcciones IP, cadenas de conexión, inicio de sesión seguro y Visual Studio gratuito. |
| 29 |[Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md) |Las conexiones de cliente de ADO.NET a Base de datos SQL de Azure V12 omiten al proxy e interactúan directamente con la base de datos. Los puertos que no sean 1433 se convierten en puertos importantes. |
| 30 |[Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas.](sql-database-develop-error-messages.md) |Obtenga información acerca de los códigos de error de SQL de las aplicaciones cliente de la Base de datos SQL, como los errores de conexión de base de datos más comunes, los problemas de copia de la base de datos y los errores generales. |
| 31 |[Conexión a la base de datos SQL mediante PHP en Windows](sql-database-develop-php-simple.md) |Presenta un programa PHP de ejemplo que se conecta a la base de datos SQL de Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesarios que necesita el cliente. |
| 32 |[Prueba de Base de datos SQL: Use C# para crear una Base de datos SQL con la biblioteca de Base de datos SQL para .NET](sql-database-get-started-csharp.md) |Pruebe la Base de datos SQL para desarrollar aplicaciones SQL y C# y crear una Base de datos SQL de Azure con C# mediante la biblioteca de Base de datos SQL para. NET. |
| 33 |[Introducción a las características de JSON en Base de datos SQL de Azure](sql-database-json-features.md) |Base de datos SQL de Azure permite analizar datos, consultarlos y darles formato en notación de objetos JavaScript (JSON). |
| 34 |[Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure](sql-database-troubleshoot-common-connection-issues.md) |Pasos para identificar y resolver errores de conexión comunes para la base de datos SQL de Azure. |
| 35 |[Error "La base de datos en el servidor no está disponible en estos momentos" al conectar con la base de datos SQL](sql-database-troubleshoot-connection.md) |Solución de problemas del error "La base de datos en el servidor no está disponible en estos momentos", al conectar con la base de datos SQL. |

## <a name="develop"></a>Desarrollo
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 36 |[Obtención de los valores necesarios para autenticar una aplicación para obtener acceso a SQL Database desde el código](sql-database-client-id-keys.md) |Cree una entidad de servicio para tener acceso a SQL Database desde el código. |
| 37 |[Conexión a la base de datos SQL mediante Java con JDBC en Windows](sql-database-develop-java-simple.md) |Este tema muestra un ejemplo de código Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa JDBC y se ejecuta en un equipo cliente de Windows. |
| 38 |[Conexión a Base de datos SQL mediante Node.js](sql-database-develop-nodejs-simple.md) |Este tema muestra un ejemplo de código Node.js que puede usar para conectarse a la base de datos SQL de Azure. |
| 39 |[Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md) |Aprenda sobre las bibliotecas de conectividad disponibles y los procedimientos recomendados para las aplicaciones que se conectan a Base de datos SQL. |
| 40 |[Conexión a Base de datos SQL mediante Python](sql-database-develop-python-simple.md) |Este tema muestra un ejemplo de código Pyton que puede usar para conectarse a la base de datos SQL de Azure. |
| 41 |[Conexión a Base de datos SQL mediante Ruby](sql-database-develop-ruby-simple.md) |Muestra de código Ruby que puede ejecutar en Windows para conectarse a Base de datos SQL de Azure. |
| 42 |[Introducción a las tablas temporales de Base de datos SQL de Azure](sql-database-temporal-tables.md) |Obtenga información sobre cómo empezar a usar las tablas temporales de Base de datos SQL de Azure |

## <a name="performance-and-scale"></a>Rendimiento y escala
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 43 |[Asesor de Base de datos SQL](sql-database-advisor.md) |El Asesor de Base de datos SQL de Azure ofrece recomendaciones para las bases de datos SQL existentes que pueden mejorar el rendimiento actual de las consultas. |
| 44 |[SQL Database Advisor con Azure Portal](sql-database-advisor-portal.md) |Puede utilizar el Asesor de Base de datos SQL de Azure en el Portal de Azure para revisar e implementar las recomendaciones para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual. |
| 45 |[Información general sobre la prueba comparativa Base de datos SQL de Azure](sql-database-benchmark-overview.md) |Este tema describe la prueba comparativa Base de datos SQL de Azure Benchmark, que se usa para la medición del rendimiento de la Base de datos SQL de Azure. |
| 46 |[¿Cuándo se debe utilizar un grupo de bases de datos elásticas?](sql-database-elastic-pool-guidance.md) |Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas. Este documento ofrece orientación para ayudarle a evaluar la idoneidad de usar un grupo de bases de datos elásticas para un grupo de base de datos. |
| 47 |[Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) |Explicación básica de la característica de herramientas de base de datos elástica de Base de datos SQL de Azure, incluida una aplicación de ejemplo de ejecución sencilla. |
| 48 |[Preguntas más frecuentes sobre la Base de datos SQL](sql-database-faq.md) |Respuestas a las preguntas más comunes que los clientes preguntan sobre las bases de datos en la nube y Base de datos SQL de Azure, el sistema de administración de bases de datos relacionales (RDBMS) de Microsoft y bases de datos como un servicio en la nube. |
| 49 |[Introducción a In-Memory (vista previa) en Base de datos SQL](sql-database-in-memory.md) |Las tecnologías In-Memory de SQL mejoran notablemente el rendimiento de las cargas de trabajo de transacciones y de análisis. Aprenda a sacar partido de estas tecnologías. |
| 50 |[Uso de In-Memory OLTP (vista previa) para mejorar el rendimiento de la aplicación de Base de datos SQL de Azure](sql-database-in-memory-oltp-migration.md) |Adopción de In-Memory OLTP para mejorar el rendimiento transaccional en una Base de datos SQL ya existente. |
| 51 |[Supervisión del almacenamiento OLTP en memoria](sql-database-in-memory-oltp-monitoring.md) |Estimar y supervisar el uso y la capacidad del almacenamiento en memoria de XTP; resolver el error de capacidad 41823 |
| 52 |[Funcionamiento del almacén de consultas de Base de datos SQL de Azure](sql-database-operate-query-store.md) |Aprenda a utilizar el Almacén de consultas de Base de datos SQL de Azure. |
| 53 |[SQL Database Performance Insight (Información de rendimiento de Base de datos SQL)](sql-database-performance.md) |La Base de datos SQL de Azure ofrece herramientas de rendimiento para ayudarle a identificar áreas que pueden mejorar el rendimiento actual de las consultas. |
| 54 |[Azure SQL Database y rendimiento de bases de datos únicas](sql-database-performance-guidance.md) |Este artículo puede ayudarle a determinar qué nivel de servicio elegir para la aplicación. También se recomiendan formas de optimizar la aplicación para obtener el máximo partido de Azure SQL Database. |
| 55 |[Query Performance Insight de Base de datos SQL de Azure](sql-database-query-performance.md) |La supervisión del rendimiento de las consultas identifica las consultas que más CPU consumen en una base de datos SQL de Azure. |
| 56 |[Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md) |El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos. Cambio del nivel de precios de una base de datos SQL de Azure. |
| 57 |[Supervisión de rendimiento de bases de datos con la Base de datos SQL de Azure](sql-database-single-database-monitor.md) |Conozca las opciones para la supervisión de la base de datos con herramientas de Azure y vistas de administración dinámica. |
| 58 |[Sugerencias para la optimización del rendimiento de Base de datos SQL](sql-database-troubleshoot-performance.md) |Sugerencias para la optimización del rendimiento de Base de datos SQL de Azure a través de la evaluación y la mejora. |
| 59 |[Uso del procesamiento por lotes para mejorar el rendimiento de las aplicaciones de Base de datos SQL](sql-database-use-batching-to-improve-performance.md) |El tema proporciona evidencia de que el procesamiento por lotes de las operaciones de base de datos mejora en gran medida la velocidad y la escalabilidad de las aplicaciones de Base de datos SQL de Azure. Aunque estas técnicas de procesamiento por lotes funcionan para cualquier base de datos de SQL Server, el artículo se centra en Azure. |

## <a name="tools-for-scaling-out"></a>Herramientas de escalado horizontal
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 60 |[Modelos de diseño para las aplicaciones SaaS multiinquilino y Base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) |En este artículo se explican los requisitos y modelos de arquitectura de datos comunes de las aplicaciones de base de datos multiinquilino que se ejecutan en un entorno de nube que debe tener en cuenta y las distintas ventajas e inconvenientes de estos modelos. También se explica cómo ayuda Base de datos SQL de Azure con sus grupos elásticos y herramientas elásticas a afrontar estos requisitos de forma garantizada. |
| 61 |[Migrate existing databases to scale-out (Migrar bases de datos existentes de escalado horizontal)](sql-database-elastic-convert-to-use-elastic-tools.md) |Conversión de bases de datos particionadas para usar herramientas para bases de datos elásticas creando un administrador de mapas de particiones |
| 62 |[Creación de bases de datos escalables en la nube](sql-database-elastic-database-client-library.md) |Crear aplicaciones de bases de datos de .NET escalables con la Biblioteca de cliente de Base de datos elástica |
| 63 |[Creación de bases de datos escalables en la nube](sql-database-elastic-database-perf-counters.md) |Clase ShardMapManager y contadores de rendimiento de enrutamiento dependiente de los datos |
| 64 |[Incorporación de una partición con herramientas de bases de datos elásticas](sql-database-elastic-scale-add-a-shard.md) |Establece cómo usar las API de escala elástica para agregar particiones nuevas a un conjunto de particiones. |
| 65 |[Implemente un servicio de división y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |División y combinación con las herramientas de Base de datos elástica |
| 66 |[Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) |Cómo usar la clase ShardMapManager en aplicaciones .NET para el enrutamiento dependiente de los datos, una característica de bases de datos elásticas para Base de datos SQL de Azure |
| 67 |[Preguntas frecuentes de herramientas de Base de datos elástica](sql-database-elastic-scale-faq.md) |Preguntas más frecuentes sobre el Escalado elástico de Base de datos SQL de Azure. |
| 68 |[Glosario de las herramientas de bases de datos elásticas](sql-database-elastic-scale-glossary.md) |Explicación de los términos usados en las herramientas de bases de datos elásticas |
| 69 |[Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md) |Los desarrolladores de Software como servicio (SaaS) pueden crear bases de datos elásticas y escalables con facilidad en la nube mediante estas herramientas |
| 70 |[Credenciales usadas para acceder a la biblioteca de cliente de bases de datos elásticas](sql-database-elastic-scale-manage-credentials.md) |Cómo configurar el nivel correcto de las credenciales (de administrador a solo lectura) de las aplicaciones de bases de datos elásticas. |
| 71 |[Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md) |Ejecute consultas a través de particiones con la biblioteca de cliente de bases de datos elásticas. |
| 72 |[Moving data between scaled-out cloud databases (Mover datos entre bases de datos en la nube escaladas horizontalmente)](sql-database-elastic-scale-overview-split-and-merge.md) |Explica cómo manipular las particiones y mover los datos a través de un servicio autohospedado mediante las API de bases de datos elásticas. |
| 73 |[Escalado horizontal de las bases de datos mediante Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) |Cómo usar ShardMapManager, la biblioteca de cliente de bases de datos elásticas |
| 74 |[Configuración de seguridad de división y combinación](sql-database-elastic-scale-split-merge-security-configuration.md) |Configuración de certificados x409 para cifrado |
| 75 |[Actualización de una aplicación para usar la biblioteca de cliente de base de datos elástica más reciente](sql-database-elastic-scale-upgrade-client-library.md) |Actualización de aplicaciones y bibliotecas mediante NuGet |
| 76 |[Biblioteca de cliente de base de datos elástica con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |Uso de la biblioteca de cliente de Base de datos elástica y Entity Framework para la codificación de bases de datos |
| 77 |[Uso de la biblioteca de cliente de bases de datos elásticas con Dapper](sql-database-elastic-scale-working-with-dapper.md) |Uso de la biblioteca de cliente de bases de datos elásticas con Dapper. |
| 78 |[Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md) |Aprenda a usar herramientas de bases de datos elásticas junto con la seguridad de nivel de fila para crear una aplicación con un nivel de datos altamente escalable en Base de datos de SQL de Azure que admite particiones de varios inquilinos. |

## <a name="elastic-jobs"></a>Trabajos elásticos
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 79 |[Creación y administración de Bases de datos SQL de Azure escaladas horizontalmente con trabajos elásticos (versión preliminar)](sql-database-elastic-jobs-create-and-manage.md) |Siga los pasos necesarios de los procesos de creación y administración de un trabajo de base de datos elástica. |
| 80 |[Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md) |usar trabajos de base de datos elástica |
| 81 |[Administración de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-jobs-overview.md) |Muestra el servicio de trabajo de base de datos elástica |
| 82 |[Creación y administración de un grupo de bases de datos SQL elásticas mediante PowerShell](sql-database-elastic-jobs-powershell.md) |PowerShell usada para administrar grupos de Base de datos SQL de Azure |
| 83 |[Información general sobre la instalación de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md) |Pasos de instalación de la característica de trabajo elástico. |
| 84 |[Desinstalación de componentes de Trabajos de base de datos elástica.](sql-database-elastic-jobs-uninstall.md) |Desinstalación de componentes de Trabajos de base de datos elástica |

## <a name="elastic-pools"></a>Grupos elásticos
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 85 |[¿Qué es un grupo elástico de Azure?](sql-database-elastic-pool.md) |Administre cientos o miles de bases de datos mediante un grupo. Un precio para un conjunto de unidades de rendimiento se puede distribuir por el grupo. Agregue o quite bases de datos a voluntad. |
| 86 |[Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md) |Use técnicas de desarrollo de bases de datos de C# para crear un grupo de bases de datos elásticas escalable en la Base de datos SQL de Azure, para así poder compartir recursos entre muchas bases de datos. |
| 87 |[Creación de un nuevo grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md) |Aprenda a usar PowerShell para escalar horizontalmente los recursos de la Base de datos SQL de Azure mediante la creación de un grupo de bases de datos elásticas escalable para administrar varias bases de datos. |
| 88 |[PowerShell script for identifying databases suitable for an elastic database pool (Script de PowerShell para identificar bases de datos adecuadas para un grupo de bases de datos elásticas)](sql-database-elastic-pool-database-assessment-powershell.md) |Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas. Este documento ofrece un script de Powershell para ayudarle a evaluar la idoneidad de usar un grupo de bases de datos elásticas para un grupo de base de datos. |
| 89 |[Administración y cambio de tamaño de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-manage-csharp.md) |Use técnicas de desarrollo de bases de datos de C# para administrar un grupo de bases de datos elásticas de Base de datos SQL de Azure. |
| 90 |[Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-manage-portal.md) |Obtenga más información sobre cómo usar la inteligencia integrada de la Base de datos SQL y el Portal de Azure con el objetivo de administrar y supervisar un grupo de bases de datos elásticas escalables, así como identificar su tamaño más adecuado, con el objetivo de optimizar el rendimiento de las bases de datos y administrar los costos. |
| 91 |[Supervisión, administración y ajuste de tamaño de un grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-manage-powershell.md) |Obtenga más información sobre cómo usar PowerShell para administrar un grupo de bases de datos elásticas. |
| 92 |[Supervisión y administración de un grupo de bases de datos elásticas con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) |Use T-SQL para crear una Base de datos SQL de Azure en un grupo elástico. O bien, use T-SQL para mover la base de datos dentro y fuera de los grupos. |
| 93 |[Información de precios y facturación de grupos de bases de datos elásticas](sql-database-elastic-pool-price.md) |Información sobre precios de grupos de bases de datos elásticas. |

## <a name="elastic-query"></a>Consulta elástica
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 94 |[Informes de bases de datos escaladas horizontalmente en la nube (versión preliminar)](sql-database-elastic-query-getting-started.md) |cómo utilizar consultas de bases de datos cruzadas |
| 95 |[Introducción a las consultas entre bases de datos (particiones verticales) (versión preliminar)](sql-database-elastic-query-getting-started-vertical.md) |cómo usar la consulta de base de datos elástica con bases de datos con particiones verticales |
| 96 |[Informes de bases de datos escaladas horizontalmente en la nube (vista previa)](sql-database-elastic-query-horizontal-partitioning.md) |Configuración de las consultas elásticas en particiones horizontales |
| 97 |[Información general sobre la consulta de bases de datos elásticas de Base de datos SQL de Azure (vista previa)](sql-database-elastic-query-overview.md) |Información general de la característica de consultas elásticas |
| 98 |[Consulta de bases de datos elásticas para consultas entre bases de datos (particionamiento vertical)](sql-database-elastic-query-vertical-partitioning.md) |cómo configurar consultas entre bases de datos en particiones verticales |

## <a name="elastic-transaction"></a>Transacción elástica
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 99 |[Introducción sobre las transacciones de base de datos elástica con Base de datos SQL de Azure](sql-database-elastic-transactions-overview.md) |Introducción sobre las transacciones de base de datos elástica con Base de datos SQL de Azure |

## <a name="backup-and-recovery"></a>Copia de seguridad y recuperación
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 100 |[Copias de seguridad de SQL Database](sql-database-automated-backups.md) |Obtenga más información sobre las copias de seguridad de base de datos integradas de SQL Database, que permiten revertir una base de datos de Azure SQL Database a un momento dado previo o copiarla en una nueva base de datos de una región geográfica (hasta 35 días). |
| 101 |[Introducción a la continuidad empresarial con Base de datos SQL de Azure](sql-database-business-continuity.md) |Obtenga información acerca de cómo la Base de datos SQL de Azure permite la continuidad del negocio en la nube y la recuperación de la base de datos, y ayuda a que las aplicaciones críticas de la nube se sigan ejecutando. |
| 102 |[Restauración de una única tabla a partir de una copia de seguridad de Base de datos SQL de Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) |Obtenga información sobre cómo restaurar una única tabla de la copia de seguridad de Base de datos SQL de Azure. |
| 103 |[Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) |Obtenga información acerca de cómo diseñar soluciones de recuperación ante desastres en la nube para planificar la continuidad del negocio mediante la replicación geográfica de la copia de seguridad de datos de la aplicación con la Base de datos SQL de Azure. |
| 104 |[Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md) |Obtenga información acerca de cómo recuperar una base de datos tras un error o una interrupción de un centro de datos regional con las funcionalidades de replicación geográfica activa y restauración geográfica de Base de datos SQL de Azure. |
| 105 |[Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md) |Obtenga instrucciones e información sobre prácticas recomendadas acerca del uso de la base de datos SQL de Azure para la realización de tareas de obtención de detalles de la recuperación ante desastres. Dichas tareas le ayudarán a mantener la capacidad de recuperación ante errores y fallos de las aplicaciones de negocio críticas. |
| 106 |[Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) |Aprenda a diseñar la solución de nube para recuperación ante desastres eligiendo el modelo correcto de conmutación por error. |
| 107 |[Uso de la clase RecoveryManager para solucionar problemas de mapas de particiones](sql-database-elastic-database-recovery-manager.md) |Uso de la clase RecoveryManager para solucionar problemas con los mapas de particiones |
| 108 |[Importación de un archivo BACPAC para crear una Azure SQL Database](sql-database-import.md) |Cree una base de datos SQL de Azure importando un archivo BACPAC ya existente. |
| 109 |[Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure](sql-database-point-in-time-restore-portal.md) |Restauración de una base de datos SQL de Azure a un momento dado anterior. |
| 110 |[Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell](sql-database-point-in-time-restore-powershell.md) |Restauración de una base de datos SQL de Azure a un momento dado anterior |
| 111 |[Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md) |Restauración a un momento dado, base de datos de Microsoft Azure SQL, restaurar base de datos, recuperar base de datos, Portal de Azure clásico, Portal de Azure clásico |
| 112 |[Recuperación de una Base de datos SQL de Azure mediante copias de seguridad automatizadas](sql-database-recovery-using-backups.md) |Obtenga información acerca de la restauración a un momento dado, que le permite revertir una Base de datos SQL de Azure a un momento dado anterior (hasta 35 días). |
| 113 |[Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure](sql-database-restore-deleted-database-portal.md) |Restauración de una base de datos SQL de Azure eliminada (Portal de Azure). |
| 114 |[Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell](sql-database-restore-deleted-database-powershell.md) |Restauración de una Base de datos SQL de Azure eliminada (PowerShell) |
| 115 |[Restaurar una base de datos a un momento anterior en el tiempo, restaurar una base de datos eliminada o recuperarse de una interrupción del centro de datos](sql-database-troubleshoot-backup-and-restore.md) |Aprenda a recuperar una base de datos de nube de errores e interrupciones mediante copias de seguridad y réplicas de Base de datos SQL de Azure. |

## <a name="migrate"></a>Migrar
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 116 |[Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos,exportación de archivo BACPAC, sqlpackage |
| 117 |[Exportación de una base de datos SQL Server a un archivo BACPAC mediante SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos, exportación de archivo BACPAC, asistente para exportación de aplicaciones de capa de datos |
| 118 |[Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, importación de archivo BACPAC, sqlpackage |
| 119 |[Importar desde BACPAC a la Base de datos SQL con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) |Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración |
| 120 |[Migrar la base de datos de SQL Server a Base de datos SQL con el Asistente para implementar base de datos en Base de datos de Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, Asistente para bases de datos de Microsoft Azure |
| 121 |[Migración de una base de datos SQL Server a Base de datos SQL de Azure mediante la replicación transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, replicación transaccional |
| 122 |[Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, compatibilidad de Base de datos SQL, SqlPackage |
| 123 |[Uso de SQL Server Management Studio para determinar la compatibilidad con bases de datos SQL Server antes de realizar la migración a Base de datos SQL de Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad de Base de datos SQL, Asistente para exportación de aplicaciones de capa de datos |
| 124 |[Uso del Asistente para migración de SQL Azure para corregir problemas de compatibilidad con bases de datos SQL Server antes de realizar la migración a Base de datos SQL de Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure |
| 125 |[Migración de una base de datos SQL Server a Base de datos SQL de Azure mediante SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure, SSDT |
| 126 |[Solución de problemas de compatibilidad de bases de datos de SQL Server mediante SQL Server Management Studio antes de la migración a la Base de datos SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) |Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure |
| 127 |[Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell](sql-database-export-powershell.md) |Archivo de una base de datos SQL de Azure en un archivo BACPAC mediante PowerShell |
| 128 |[Importación de un archivo BACPAC para crear una Azure SQL Database mediante PowerShell](sql-database-import-powershell.md) |Importación de un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell |
| 129 |[Carga de datos desde CSV en Almacenamiento de datos SQL de Azure (archivos planos)](sql-database-load-from-csv-with-bcp.md) |Para un tamaño de datos pequeño, utiliza bcp para importar datos en Base de datos SQL de Azure. |
| 130 |[Movimiento de bases de datos de un servidor a otro, de una suscripción a otra, y dentro y fuera de Azure](sql-database-troubleshoot-moving-data.md) |Pasos rápidos para copiar, mover y migrar datos y bases de datos en Base de datos SQL de Azure. |

## <a name="move-data-in-and-out"></a>Introducción y extracción de datos
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 131 |[Migración de una base de datos de SQL Server a una Base de datos SQL en la nube](sql-database-cloud-migrate.md) |Obtenga más información acerca de la migración de una base de datos de SQL Server local a una Base de datos SQL de Azure en la nube. Antes de migrar una base de datos, use herramientas de migración de bases de datos para determinar la compatibilidad. |
| 132 |[Copiar una base de datos SQL de Azure](sql-database-copy.md) |Creación de una copia de una base de datos SQL de Azure |
| 133 |[Archivar una base de datos de SQL Azure en un archivo BACPAC mediante el Portal de Azure](sql-database-export.md) |Archivo de una Azure SQL Database en un archivo BACPAC mediante Azure Portal |

## <a name="security"></a>Seguridad
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 134 |[Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md) |Aprenda a conectarse a una Base de datos SQL mediante autenticación de Azure Active Directory |
| 135 |[Always Encrypted: protección de los datos confidenciales en Base de datos SQL y almacenamiento de las claves de cifrado en el almacén de certificados de Windows](sql-database-always-encrypted.md) |Proteger datos confidenciales en la base de datos SQL en cuestión de minutos. |
| 136 |[Always Encrypted: protección de datos confidenciales en Base de datos SQL y almacenamiento de las claves de cifrado en Almacén de claves de Azure](sql-database-always-encrypted-azure-key-vault.md) |Proteger datos confidenciales en la base de datos SQL en cuestión de minutos. |
| 137 |[SQL Database: compatibilidad con clientes de nivel inferior y cambios de punto de conexión IP para auditoría](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) |Más información sobre Compatibilidad de clientes de nivel inferior de Base de datos SQL y cambios de punto de conexión IP para auditoría. |
| 138 |[Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante PowerShell](sql-database-configure-firewall-settings-powershell.md) |Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure. |
| 139 |[Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante la API de REST](sql-database-configure-firewall-settings-rest.md) |Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure. |
| 140 |[Configuración de reglas de firewall de nivel de servidor en Base de datos SQL de Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md) |Aprenda a configurar el firewall para direcciones IP que obtengan acceso a bases de datos SQL de Azure. |
| 141 |[Introducción al enmascaramiento de datos dinámicos de bases de datos SQL (Portal de Azure)](sql-database-dynamic-data-masking-get-started.md) |Cómo empezar a usar el enmascaramiento de datos dinámicos de Base de datos SQL en el Portal de Azure |
| 142 |[Introducción al enmascaramiento de datos dinámicos de Base de datos SQL (Portal de Azure clásico)](sql-database-dynamic-data-masking-get-started-portal.md) |Cómo empezar a usar el enmascaramiento de datos dinámicos de Base de datos SQL en el Portal de Azure clásico |
| 143 |[Configuración de reglas de firewall de Azure SQL Database \- información general](sql-database-firewall-configure.md) |Obtenga información acerca de cómo configurar un firewall de base de datos SQL mediante las reglas de firewall de nivel de servidor y de nivel de base de datos, para administrar el acceso. |
| 144 |[SQL Database tutorial: Create SQL database user accounts to access and manage a database (Tutorial de Base de datos SQL: Creación de cuentas de base de datos SQL para acceder a una base de datos y administrarla)](sql-database-get-started-security.md) |Aprenda a crear cuentas de usuario para acceder a una base de datos y administrarla. |
| 145 |[Autorización y autenticación de Base de datos SQL: concesión de acceso](sql-database-manage-logins.md) |Obtenga información acerca de la administración de seguridad de la Base de datos SQL, específicamente el modo de administrar la seguridad del inicio de sesión y el acceso a la base de datos con la cuenta de entidad de seguridad a nivel de servidor. |
| 146 |[Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure](sql-database-security-guidelines.md) |Obtenga más información acerca de las instrucciones y limitaciones de la Base de datos SQL de Microsoft Azure relacionadas con la seguridad. |
| 147 |[Introducción a Detección de amenazas de Base de datos SQL](sql-database-threat-detection-get-started.md) |Cómo empezar a usar Detección de amenazas de Base de datos SQL en el Portal de Azure |
| 148 |[Realización de tareas administrativas comunes como el restablecimiento de la contraseña de administrador en Base de datos SQL de Azure](sql-database-troubleshoot-permissions.md) |Describe como realizar tareas administrativas comunes en Base de datos SQL de Azure. Por ejemplo, restablecer la contraseña de administrador, conceder y revocar el acceso. |

## <a name="manage-your-database"></a>Administración de la base de datos
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 149 |[Introducción a la auditoría de bases de datos SQL](sql-database-auditing-get-started.md) |Introducción a la auditoría de bases de datos SQL |
| 150 |[Configuración de un firewall de nivel de servidor en Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md) |Descubra cómo configurar el firewall para direcciones IP que accedan al servidor SQL de Azure. |
| 151 |[Copia de una instancia de Base de datos SQL de Azure mediante el Portal de Azure](sql-database-copy-portal.md) |Creación de una copia de una base de datos SQL de Azure |
| 152 |[Administración de bases de datos SQL de Azure mediante Automatización de Azure](sql-database-manage-automation.md) |Obtenga información acerca de cómo puede usarse el servicio Automatización de Azure para administrar bases de datos SQL de Azure a escala. |
| 153 |[Información general: herramientas de administración para Base de datos SQL](sql-database-manage-overview.md) |Compara las herramientas y opciones para administrar Base de datos SQL de Azure |
| 154 |[Supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md) |Obtenga información sobre cómo detectar y diagnosticar problemas comunes de rendimiento con vistas de administración dinámica para supervisar Base de datos SQL de Microsoft Azure. |
| 155 |[Protección de bases de datos SQL](sql-database-security.md) |Obtenga información acerca de la seguridad de la Base de datos SQL de Azure y SQL Server, incluidas las diferencias entre la nube y SQL Server local en cuanto a la autenticación, autorización, seguridad de conexión, cifrado y cumplimiento normativo. |

## <a name="extended-events"></a>Eventos extendidos
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 156 |[Código de destino del archivo de evento para eventos extendidos en Base de datos SQL](sql-database-xevent-code-event-file.md) |Brinda PowerShell y Transact-SQL para un ejemplo de código de dos fases que muestra el destino de archivo de evento en un evento extendido en Base de datos SQL de Azure. Almacenamiento de Azure es una parte necesaria en este escenario. |
| 157 |[Código de destino de búfer en anillo para eventos extendidos en Base de datos SQL](sql-database-xevent-code-ring-buffer.md) |Proporciona un ejemplo de código de Transact-SQL más fácil y rápido mediante el uso del destino de Búfer de anillo, en Base de datos SQL de Azure. |
| 158 |[Eventos extendidos en Base de datos SQL](sql-database-xevent-db-diff-from-svr.md) |Describe los eventos extendidos (XEvents) en Base de datos SQL de Azure y cómo las sesiones de eventos difieren ligeramente de las sesiones de eventos en Microsoft SQL Server. |

## <a name="geo-replication"></a>Replicación geográfica
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 159 |[Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-failover-portal.md) |Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con el Portal de Azure |
| 160 |[Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) |Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell |
| 161 |[Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) |Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con Transact-SQL |
| 162 |[Información general: Replicación geográfica activa para Base de datos SQL de Azure](sql-database-geo-replication-overview.md) |La replicación geográfica activa permite configurar cuatro réplicas de la base de datos en cualquiera de los centros de datos de Azure. |
| 163 |[Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md) |Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure |
| 164 |[Configuración de la replicación geográfica para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-powershell.md) |Configuración de la replicación geográfica activa para Base de datos SQL de Azure con PowerShell |
| 165 |[Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) |En este tema se explican las consideraciones de seguridad que hay que tener en cuenta para administrar la seguridad después de restaurar una base de datos o de conmutarla por error. |
| 166 |[Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md) |Configuración de la replicación geográfica para Base de datos SQL de Azure con Transact-SQL |
| 167 |[Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure](sql-database-geo-restore-portal.md) |Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica (Portal de Azure) |
| 168 |[Restauración geográfica de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante PowerShell](sql-database-geo-restore-powershell.md) |Restauración de una Base de datos SQL de Azure en un nuevo servidor a partir de una copia de seguridad con redundancia geográfica |

## <a name="upgrade"></a>Actualizar
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 169 |[Rendimiento mejorado de consultas con el nivel de compatibilidad 130 en Base de datos SQL de Azure](sql-database-compatibility-level-query-performance-130.md) |Pasos y herramientas para determinar qué nivel de compatibilidad es más adecuado para su base de datos en Base de datos SQL de Azure o Microsoft SQL Server |
| 170 |[Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de Base de datos SQL](sql-database-manage-application-rolling-upgrade.md) |Aprenda a usar la replicación geográfica en Base de datos SQL para admitir las actualizaciones en línea de la aplicación en la nube. |
| 171 |[Actualización a la base de datos SQL V12 de Azure con el Portal de Azure](sql-database-upgrade-server-portal.md) |Explicación acerca de cómo actualizar a la Base de datos SQL V12 de Azure, incluido cómo actualizar las bases de datos de tipo Web y Business y cómo actualizar un servidor V11 migrando sus bases de datos directamente a un grupo de bases de datos elásticas mediante el Portal de Azure. |
| 172 |[Actualización a la Base de datos SQL V12 de Azure mediante PowerShell](sql-database-upgrade-server-powershell.md) |Explicación acerca de cómo actualizar a la Base de datos SQL V12 de Azure, incluyendo cómo actualizar las bases de datos de tipo Web y Business y cómo actualizar un servidor V11 migrando sus bases de datos directamente a un grupo de bases de datos elásticas mediante PowerShell. |
| 173 |[Planeación y preparación para realizar la actualización a Base de datos SQL V12](sql-database-v12-plan-prepare-upgrade.md) |Describe los preparativos y las limitaciones que conlleva la actualización a la versión V12 de Base de datos SQL de Azure. |
| 174 |[Novedades de Base de datos SQL V12](sql-database-v12-whats-new.md) |Describe cuáles son las ventajas que van a obtener los sistemas empresariales que están utilizando Base de datos SQL de Azure en la nube al actualizarse a la versión V12 ahora. |
| 175 |[Preguntas frecuentes relacionadas con la retirada de las versiones Web y Business](sql-database-web-business-sunset-faq.md) |Obtenga información sobre cuándo se retirarán las bases de datos SQL de Azure Web y Business y sobre las características y funciones de los nuevos niveles de servicio. |

## <a name="tools"></a>Herramientas
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 176 |[Administración de Base de datos SQL de Azure con PowerShell](sql-database-command-line-tools.md) |Administración de Base de datos SQL de Azure con PowerShell. |
| 177 |[Tutorial de Base de datos SQL: conexión de Excel a una Base de datos SQL de Azure y creación de un informe](sql-database-connect-excel.md) |Más información acerca de cómo conectar Microsoft Excel a Base de datos SQL de Azure en la nube. Importación de datos en Excel para la generación de informes y la exploración de datos. |
| 178 |[Creación de una nueva Base de datos SQL y realización de tareas comunes de configuración de base de datos con los cmdlets de PowerShell](sql-database-get-started-powershell.md) |Aprenda a crear una Base de datos SQL con PowerShell. Las tareas de configuración comunes de la base de datos pueden administrarse mediante los cmdlets de PowerShell. |
| 179 |[Introducción a SQL Data Sync de Azure (vista previa)](sql-database-get-started-sql-data-sync.md) |Este tutorial le ayuda a comenzar con SQL Data Sync de Azure (vista previa). |
| 180 |[Administración de Base de datos SQL de Azure con el uso de SQL Server Management Studio](sql-database-manage-azure-ssms.md) |Obtenga información acerca de cómo usar SQL Server Management Studio para administrar bases de datos y servidores de bases de datos SQL. |
| 181 |[Administración de Bases de datos SQL de Azure con el Portal de Azure](sql-database-manage-portal.md) |Aprenda a usar el Portal de Azure para administrar una base de datos relacional en la nube mediante el Portal de Azure. |
| 182 |[Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL con PowerShell](sql-database-scale-up-powershell.md) |El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos mediante PowerShell. Cambio del nivel de precios de una base de datos SQL de Azure mediante PowerShell. |
| 183 |[Utilización de SQL Server Management Studio en Azure RemoteApp para conectarse a una base de datos SQL](sql-database-ssms-remoteapp.md) |Use este tutorial para aprender a utilizar SQL Server Management Studio en Azure RemoteApp y optimizar la seguridad y el rendimiento al conectarse a la Base de datos SQL |

## <a name="reference"></a>Referencia
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 184 |[Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md) |Muestra el número de versión mínimo para cada controlador que pueden usar los programas cliente para conectarse a Base de datos SQL de Azure o a Microsoft SQL Server. Se proporciona un vínculo con información acerca de los controladores publicados por la comunidad y no por Microsoft. |
| 185 |[Límites de recursos de Base de datos SQL](sql-database-resource-limits.md) |En esta página se describen algunos límites de recursos comunes para Base de datos SQL de Azure. |
| 186 |[Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md) |Instrucciones de Transact-SQL que no son totalmente compatibles con la Base de datos SQL de Azure |

## <a name="miscellaneous"></a>Varios
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 187 |[Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) |Crear copia de una base de datos SQL de Azure con PowerShell |
| 188 |[Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md) |Crear una copia de una base de datos SQL de Azure con Transact-SQL |
| 189 |[Instrucciones y limitaciones generales de Base de datos SQL de Azure](sql-database-general-limitations.md) |En esta página se describen algunas limitaciones generales para Base de datos SQL de Azure, así como las áreas de interoperatividad y compatibilidad. |
| 190 |[Recomendaciones sobre el nivel de precios de Base de datos SQL](sql-database-service-tier-advisor.md) |Si se cambian los niveles de precios en el Portal de Azure, se recomienda el nivel más apropiado para ejecutar la carga de trabajo de una Base de datos SQL de Azure existente. Los niveles de precios describen el nivel de servicio y el nivel de rendimiento de una base de datos SQL. |

&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Extras
<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Artículos relacionados de otros servicios de Azure
* [Back up your Azure App Services app in Azure (Hacer copia de seguridad de su aplicación de Servicios de aplicaciones de Azure en Azure)](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Herramientas de documentación
* [Updates announced for Azure SQL Database (Actualizaciones anunciadas en Base de datos SQL de Azure)](http://azure.microsoft.com/updates/?service=sql-database)
* [Search all the documentation types of Microsoft Azure, with filters (Buscar todos los tipos de documentación de Microsoft Azure, con filtros)](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Gráficos de la ruta de aprendizaje
* [Learning Path graphics for all Microsoft Azure services (Gráficos de la ruta de aprendizaje para todos los servicios de Microsoft Azure)](http://azure.microsoft.com/documentation/learning-paths/)
* [Learning Path: Learn Azure SQL Database (Ruta de aprendizaje: aprender sobre Base de datos SQL de Azure)](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)
* [Learning Path: SQL Database elastic scale (Ruta de aprendizaje: escala elástica de Base de datos SQL)](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)

<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->





<!--HONumber=Oct16_HO2-->


