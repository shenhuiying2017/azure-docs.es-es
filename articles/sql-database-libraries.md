<properties
	pageTitle="Bibliotecas de conexiones para Base de datos SQL y SQL Server"
	description="Muestra el número de versión mínimo para cada controlador que pueden usar los programas cliente para conectarse a Base de datos SQL de Azure o a Microsoft SQL Server. Se proporciona un vínculo con información acerca de los controladores publicados por la comunidad y no por Microsoft."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/01/2015"
	ms.author="pehteh"/>


# Bibliotecas de conexiones para Base de datos SQL y SQL Server


En este tema se muestra el número de versión mínimo para cada biblioteca o controlador que los programas cliente pueden usar para conectarse a Base de datos SQL de Azure o a Microsoft SQL Server.


Este tema se divide en dos secciones:


- *Tabla de bibliotecas de controladores publicada por Microsoft*: cubre las bibliotecas que Microsoft ha publicado. Microsoft mantiene la información de esta sección.
- *Bibliotecas de terceros*: muestra las bibliotecas que publican y mantienen terceros en lugar de Microsoft. **Solo la comunidad pública de desarrolladores mantiene esta sección. Microsoft no mantendrá esta sección.**


## Tabla de bibliotecas de controladores publicadas por Microsoft


En la tabla siguiente se muestran las bibliotecas publicadas por Microsoft. La columna **Bibliotecas** proporciona vínculos que puede usar para descargar cada biblioteca. La columna **Versión** muestra la versión mínima recomendada para interactuar con Base de datos de SQL Azure y Microsoft SQL Server.


| Plataforma | Oper sys | Bibliotecas | Versión | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| .NET | Multiplataforma (. NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4.5 + | Proveedor de SQL Server para .NET Framework |
| PHP | Windows | [PHP para SQL Server](http://www.microsoft.com/es-es/download/details.aspx?id=20098) | 2.0+ | Controlador PHP para SQL Server |
| Java | Windows | [JDBC para SQL Server](https://www.microsoft.com/es-es/download/details.aspx?id=11774) | 2.0+ | Controlador JDBC de tipo 4 que proporciona conectividad de base de datos a través de la API de JDBC estándar |
| ODBC | Windows | [ODBC para SQL Server](http://www.microsoft.com/es-es/download/details.aspx?id=36434) | 11.0+ | Controlador ODBC de Microsoft para SQL Server |
| ODBC | Suse Linux | [ODBC para SQL Server](http://www.microsoft.com/es-es/download/details.aspx?id=34687) | 11.0+ | Controlador ODBC de Microsoft para SQL Server |
| ODBC | Redhat Linux | [ODBC para SQL Server](http://www.microsoft.com/es-es/download/details.aspx?id=34687) | 11.0+ | Controlador ODBC de Microsoft para SQL Server |


## Bibliotecas de terceros


> [AZURE.IMPORTANT]En la tabla siguiente se muestran las bibliotecas publicadas por terceros bajo los términos de licencia de terceros. El usuario es responsable de comprobar y cumplir con las licencias de terceros relevantes para poder usar estas bibliotecas. Asume el riesgo del uso de estas bibliotecas. Microsoft no otorga ninguna garantía, expresa o implícita, con respecto a la información proporcionada aquí y simplemente ha proporcionado la información por razones de conveniencia para los usuarios. Nada en este documento implica cualquier tipo de aprobación por Microsoft. <br/><br/>Depende de la comunidad pública de desarrolladores la actualización y el mantenimiento de la información que se encuentra en esta sección "Bibliotecas de terceros", mediante el uso del repositorio [azure-content](http://github.com/Azure/azure-content/) propiedad de **Azure** en GitHub.com. Microsoft anima a los desarrolladores a actualizar esta sección. *No* está previsto que el personal de Microsoft mantenga la información de esta sección, en parte porque otras personas son más expertas con las bibliotecas de terceros que nosotros. Gracias.


En la tabla siguiente se muestran las bibliotecas publicadas por terceros como de otras compañías o por la comunidad. Las bibliotecas publicadas por Microsoft están restringidas a la sección anterior de este tema.


| Plataforma | Bibliotecas |
| :-- | :-- |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql *(org)*](http://pymssql.org/) |
| Node.js | [Tedious *(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL *(github, patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js *(github com, tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js *(tjanczuk, github io)*](http://tjanczuk.github.io/edge/) |
| . | [FreeTDS *(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=58-->