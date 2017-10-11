---
title: "Detección, identificación y clasificación de datos personales en Microsoft Azure | Microsoft Docs"
description: "Obtenga información sobre cómo buscar, clasificar, detectar e identificar datos"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 6ccb064a9a76e7041d4f365b3997673dc9182e0b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Detección, identificación y clasificación de datos personales en Microsoft Azure

En este artículo se proporcionan indicaciones sobre cómo detectar, identificar y clasificar los datos personales en varios servicios, incluido Azure Data Catalog, Azure Active Directory, SQL Database, Power Query para clústeres de Hadoop en Azure HDInsight, Azure Information Protection, Azure Search y consultas SQL para Azure Cosmos DB.

## <a name="scenario-problem-statement-and-goal"></a>Escenario, declaración del problema y objetivo

Una empresa de deportes con sede en Estados Unidos recopila un conjunto de datos tanto personales como de otro tipo. Entre ellos, se incluyen datos de clientes y empleados. La empresa los mantiene en varias bases de datos y los almacena en varias ubicaciones diferentes en su entorno de Azure. Además de vender equipamiento deportivo, también organizan y administran el registro de importantes eventos deportivos en todo el mundo, incluida la Unión Europea. En algunos casos, los datos de clientes incluyen información médica.

Dado que la empresa organiza muchas vueltas ciclistas internacionales al año y tiene empleados repartidos por todo el mundo, dos de los conjuntos de datos son bastante grandes. La empresa también tiene aplicaciones creadas por desarrolladores que usan los clientes y empleados.

La empresa quiere solucionar los problemas siguientes:

- Para garantizar la seguridad y el acceso adecuados, los datos personales de clientes y empleados deben clasificarse y distinguirse de otros datos que la empresa recopila.
- El administrador de datos debe detectar fácilmente la ubicación de los datos personales de clientes en las diferentes áreas del entorno de Azure.
- Para contribuir a la seguridad, los datos personales de clientes y empleados que aparecen en documentos compartidos y comunicaciones de correo electrónico deben estar etiquetados.
- Los desarrolladores de aplicaciones de la empresa necesitan una forma fácil de buscar los datos personales de clientes y empleados en sus aplicaciones móviles y web.
- Los desarrolladores también necesitan consultar los datos personales en su base de datos de documentos.

### <a name="company-goals"></a>Objetivos de la empresa

- Todos los datos personales de clientes y empleados deben etiquetarse o anotarse en Azure Data Catalog para que se puedan localizar fácilmente. Lo ideal es que los datos personales de clientes y empleados se etiqueten y anoten por separado.
- Los datos personales de perfiles de usuario de clientes y empleados y la información de trabajo que residen en Azure Active Directory deben encontrarse fácilmente.
- Los datos personales que residen en varias bases de datos SQL deben consultarse con facilidad. 
- Algunos de los conjuntos de datos grandes de la empresa se administran mediante Azure HDInsight y se almacenan en Hadoop. Se deben importar en Excel para que se puedan consultar los datos personales.
- Los datos personales compartidos en documentos y las comunicaciones de correo electrónico se deben clasificar, etiquetar y mantener seguros con Azure Information Protection.
- Los desarrolladores de aplicaciones de la empresa tienen que poder detectar datos personales de clientes y empleados en las aplicaciones que han compilado, lo que pueden hacer con Azure Search.
- Los desarrolladores tienen que poder encontrar datos personales en su base de datos de documentos.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: detección de datos

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Puede buscar los perfiles de usuario de clientes y empleados y la información de trabajo de usuario que contienen datos personales en el entorno de [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) mediante [Azure Portal](https://portal.azure.com/).

Esto es especialmente útil si quiere buscar o cambiar los datos personales de un usuario específico. También puede agregar o cambiar el perfil de usuario y la información del trabajo. Tiene que iniciar sesión con una cuenta que sea administrador global en el directorio.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>¿Cómo busco o veo el perfil de usuario y la información del trabajo?

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![cómo busco el perfil de usuario y la información del trabajo](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. En la hoja **Usuarios y grupos**, seleccione **Usuarios**.

  ![Apertura de Usuarios y grupos](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. En la hoja **Usuarios y grupos - Usuarios**, seleccione un usuario de la lista y después, en la hoja del usuario seleccionado, seleccione **Perfil** para ver la información de perfil de usuario que puede contener datos personales.

  ![seleccionar usuario](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Si necesita agregar o cambiar información de perfil de usuario, puede hacerlo y, después, en la barra de comandos, seleccione **Guardar**.
6. En la hoja del usuario seleccionado, seleccione **Información laboral** para ver la información laboral del usuario que puede contener datos personales.

 ![ver la información laboral](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Si necesita agregar o cambiar información laboral del usuario, puede hacerlo y, después, en la barra de comandos, seleccione **Guardar**.

## <a name="azure-sql-database-data-discovery"></a>Azure SQL Database: detección de datos

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) es una base de datos en la nube que ayuda a los desarrolladores a crear y mantener aplicaciones. Para buscar datos personales en [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50), se pueden usar consultas SQL estándares. La consulta elástica de Azure SQL (versión preliminar) permite a los usuarios realizar consultas entre bases de datos.

En un tutorial detallado de [SQL Database](../sql-database/sql-database-technical-overview.md) se explican muchos aspectos del uso de una base de datos SQL, incluido cómo crear una y cómo ejecutar consultas de datos. Aquí tiene un resumen de la información disponible en el tutorial con vínculos a secciones específicas.

### <a name="how-do-i-build-a-sql-database"></a>¿Cómo se compila una base de datos SQL?

Existen tres formas de hacerlo:

- Se puede crear una instancia de Azure SQL Database en [Azure Portal](https://portal.azure.com/). En el tutorial, usará un conjunto específico de recursos de proceso y almacenamiento de un grupo de recursos y servidor lógico. Usará datos de ejemplo de una empresa ficticia denominada AdventureWorks. También creará una regla de firewall de nivel de servidor. Para obtener información sobre cómo hacerlo, consulte el tutorial [Creación de una instancia de Azure SQL Database en Azure Portal](../sql-database/sql-database-get-started-portal.md).

  ![Crear una instancia de Azure SQL Database](media/how-to-discover-classify-personal-data-azure/create-database.png)
- También se puede crear una base de datos SQL en la CLI de [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), una herramienta de línea de comandos basada en el explorador. La herramienta está disponible en Azure Portal y se puede ejecutar directamente desde ahí. En este tutorial, iniciará la herramienta, definirá las variables de script, creará un grupo de recursos y un servidor lógico, y configurará una regla de firewall del servidor. Después, creará una base de datos con datos de ejemplo. Para obtener información sobre cómo crear la base de datos de este modo, consulte el tutorial [Creación de una sola instancia de Azure SQL Database con la CLI de Azure](../sql-database/sql-database-get-started-cli.md).

  ![Tutorial de CLIT](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
La CLI de Azure la usan normalmente los desarrolladores y administradores de Linux. A algunos usuarios les resulta más fácil y más intuitiva que PowerShell, que es la tercera opción.

- Por último, puede crear una base de datos SQL con PowerShell, que es una herramienta de línea de comandos o script que se usa para crear y administrar Azure y otros recursos. En este tutorial, iniciará la herramienta, definirá las variables de script, creará un grupo de recursos y un servidor lógico, y configurará una regla de firewall del servidor. Después, creará una base de datos con datos de ejemplo.

Para realizar el tutorial, es necesaria la versión 4.0 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute Get-Module -ListAvailable AzureRM para buscar la versión. Si necesita instalarla o actualizarla, consulte Install Azure PowerShell module (Instalar el módulo de Azure PowerShell).

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Para obtener información sobre cómo crear la base de datos de este modo, consulte el tutorial [Creación de una sola instancia de Azure SQL Database con PowerShell](../sql-database/sql-database-get-started-powershell.md).

>[!Note]
Los administradores de Windows suelen usar PowerShell, pero algunos de ellos prefieren la CLI de Azure.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>¿Cómo busco datos personales en la base de datos SQL en Azure Portal?**

Puede usar la herramienta integrada del editor de consultas en Azure Portal para buscar datos personales. Inicie sesión en la herramienta con las credenciales y la contraseña de administrador de SQL Server y después escriba una consulta.

  ![buscar en sql mediante el portal](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

En el paso 5 del tutorial, se muestra una consulta de ejemplo en el panel del editor de consultas, pero no se centra en información personal o confidencial (también combina datos de dos tablas y crea alias para la columna de origen en el conjunto de datos que se devuelve). En la siguiente captura de pantalla, se muestra la consulta del paso 5, así como el panel de resultados que se devuelve:

  ![editor de consultas](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Si la base de datos se ha denominado MyTable, una consulta de ejemplo para obtener información personal podría incluir el nombre, el número del seguro social y el número de identificación, y tendría este aspecto:

“SELECT Name, SSN, ID number FROM MyTable”

Ejecute la consulta y después vea los resultados en el panel **Resultados**.

Para obtener más información sobre cómo consultar una base de datos SQL en Azure Portal, visite la sección [Consulta a SQL Database](../sql-database/sql-database-get-started-portal.md) del tutorial.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>¿Cómo se buscan datos entre varias bases de datos?

La consulta elástica de SQL (versión preliminar) le permite realizar consultas entre bases de datos y de varias bases de datos y devuelve un único resultado. En la [información general del tutorial](../sql-database/sql-database-elastic-query-overview.md), se incluye una descripción detallada de los escenarios y se explica la diferencia entre la creación de particiones de base de datos horizontal y vertical. La creación de partición horizontal se denomina "particionamiento".

  ![Particiones verticales](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![creación de partición horizontal](media/how-to-discover-classify-personal-data-azure/horizontal.png)

Para comenzar, visite la página [Información general sobre las consultas elásticas de Azure SQL Database (versión preliminar)](../sql-database/sql-database-elastic-query-overview.md).

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (para importar clústeres de Hadoop de Azure HDInsight): detección de datos para grandes conjuntos de datos

Hadoop es un servicio de almacenamiento y procesamiento de código abierto de Apache para grandes conjuntos de datos, que se analizan y almacenan en clústeres de Hadoop. [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) permite a los usuarios trabajar con clústeres de Hadoop en Azure. Power Query es un complemento de Excel que, entre otras cosas, ayuda a los usuarios a detectar datos de orígenes diferentes.

Los datos personales asociados con clústeres de Hadoop en Azure HDInsight se pueden importar en Excel con Power Query. Una vez que los datos están en Excel, puede usar una consulta para identificarlos.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>¿Cómo puedo usar Power Query de Excel para importar clústeres de Hadoop en Azure HDInsight en Excel?

Un tutorial de HDInsight le guiará a través de este proceso. Se explican los requisitos previos y se incluye un vínculo a un tutorial de [Introducción a Azure HDInsight](../hdinsight/hdinsight-hadoop-linux-tutorial-get-started.md). En las instrucciones, se tratan Excel 2016, así como 2013 y 2010 (los pasos son algo diferentes para las versiones anteriores de Excel). Si no tiene el complemento Power Query de Excel, en el tutorial se muestra cómo obtenerlo. Empezará el tutorial en Excel y deberá tener una cuenta de Azure Blob Storage asociada al clúster.

  ![Power Query en Excel](media/how-to-discover-classify-personal-data-azure/excel.png)

Para obtener información sobre cómo hacerlo, consulte el tutorial [Conexión de Excel a Hadoop con Power Query](../hdinsight/hdinsight-connect-excel-power-query.md).

Origen: [Conexión de Excel a Hadoop con Power Query](../hdinsight/hdinsight-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure Information Protection: clasificación de datos personales de documentos y correo electrónico

[Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) puede ayudar a los clientes de Azure a aplicar etiquetas para clasificar y proteger documentos y comunicaciones por correo electrónico compartidos de forma interna y externa. Algunos de estos elementos pueden contener información personal de clientes o empleados. Las reglas y condiciones se pueden definir automática o manualmente, ya sea por parte de los administradores o de los usuarios. Por ejemplo, si un usuario guarda un documento que incluye información de la tarjeta de crédito, debería ver una recomendación de etiqueta que ha configurado el administrador.

### <a name="how-do-i-try-it"></a>¿Cómo puedo probarlo?

Si quiere probar Azure Information Protection para ver si encaja en su organización, consulte el [Tutorial de inicio rápido](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). En él, se le guía a través de cinco pasos básicos (desde la instalación hasta la configuración de directivas para ver la clasificación, el etiquetado y el uso compartido en acción) y debería llevarle menos de media hora.

### <a name="how-do-i-deploy-it"></a>¿Cómo lo implemento?

Si quiere implementar Azure Information Protection en su organización, consulte el [Mapa de ruta de implementación de Azure Information Protection](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>¿Hay algo más que deba saber?

Para obtener información complementaria que le ayudará a pensar en cómo configurarlo, consulte la entrada de blog [Ready, set, protect!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
(Preparados, listos, ¡a proteger!). Además, compruebe los vínculos de más información que se enumeran a continuación para obtener información adicional sobre Azure Information Protection.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Azure Search: detección de datos para aplicaciones de desarrollador

[Azure Search](https://azure.microsoft.com/services/search/) es una solución de búsqueda en la nube para desarrolladores que proporciona una experiencia de búsqueda de datos completa para sus aplicaciones. Azure Search le permite buscar datos en índices definidos por el usuario, con orígenes de Azure Cosmos DB, Azure SQL Database, Azure Blob Storage, Azure Table Storage o datos JSON de clientes personalizados. También puede estructurar las consultas de Lucene con la API de REST de Azure Search para buscar tipos de datos personales o los datos personales de usuarios específicos. Entre las características, se incluyen la búsqueda de texto completo, la sintaxis de consulta simple y la sintaxis de consulta de Lucene. 

## <a name="how-do-i-use-sql-to-query-data"></a>¿Cómo puedo usar SQL para consultar datos?

Para comenzar con los conceptos básicos, consulte el tutorial [Azure Cosmos DB: ¿cómo realizar consultas mediante SQL?](../cosmos-db/tutorial-query-documentdb.md) En el tutorial, se proporciona un documento de ejemplo y dos consultas SQL de ejemplo y los resultados.

Para obtener instrucciones más detalladas sobre cómo crear consultas SQL, visite [Consultas SQL para la API de DocumentDB de Azure Cosmos DB](../cosmos-db/documentdb-sql-query.md).

Si no está familiarizado con Azure Cosmos DB y quiere obtener información sobre cómo crear una base de datos, agregar una colección y agregar datos, visite el tutorial de inicio rápido [Azure Cosmos DB: Compilar una aplicación web de API DocumentDB](../cosmos-db/create-documentdb-dotnet.md). Si quiere hacer esto en un lenguaje que no sea .NET (como Java o Python), elija el lenguaje que quiera cuando esté en el sitio.

## <a name="next-steps"></a>Pasos siguientes

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[¿Qué es SQL Database?](../sql-database/sql-database-technical-overview.md)

[SQL Database Query Editor available in Azure portal (Editor de consultas de SQL Database disponible en Azure Portal)] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[¿Qué es Azure Information Protection?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[¿Qué es Azure Rights Management?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure Information Protection: Ready, set, protect!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/) (Azure Information Protection: preparados, listos, ¡a proteger!)
