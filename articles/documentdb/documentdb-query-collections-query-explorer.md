<properties 
	pageTitle="Creación, edición y ejecución de consultas SQL con respecto a una colección de DocumentDB usando el Explorador de consultas | Microsoft Azure" 
	description="Aprenda sobre el Explorador de consultas de DocumentDB, una herramienta del Portal de Azure que permite crear, editar y ejecutar consultas SQL en una colección de DocumentDB." 
	services="documentdb" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article"
	ms.date="12/01/2015" 
	ms.author="anhoh"/>

# Creación, edición y ejecución de consultas SQL con respecto a una colección de DocumentDB usando el Explorador de consultas #

En este artículo se proporciona información general sobre el Explorador de consultas de [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), una herramienta del Portal de Microsoft Azure que permite crear, editar y ejecutar consultas en una colección de DocumentDB.

Después de completar este tutorial, podrá responder a las preguntas siguientes:

-	¿Cómo puedo crear, editar y ejecutar consultas fácilmente en una colección de DocumentDB mediante un explorador web?
-	¿Cómo puedo navegar fácilmente por las páginas de resultados de la consultas de DocumentDB mediante un explorador web?
-	¿Cómo puedo solucionar los errores de sintaxis mediante una consulta de DocumentDB? 

##<a id="Launch"></a>Explorador de consultas: inicio y navegación##

El Explorador de consulta se puede iniciar desde cualquier hoja, colección, base de datos o cuenta de DocumentDB.
  
1. En la parte superior de la hoja de la base de datos o la cuenta de DocumentDB, haga simplemente clic en el comando **Explorador de consultas**.

	![Captura de pantalla del comando del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)

2. Además, casi al final de cada hoja están las **Herramientas de desarrollo**, que contiene el mosaico del **Explorador de consultas**.
	
	![Captura de pantalla de la parte del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryexplorerpart.png)

2. Solo tiene que hacer clic en el icono para iniciar el Explorador de consultas.

	Las listas desplegables **Base de datos** y **Colección** se rellenan previamente según el contexto en el que se ejecute el Explorador de consultas. Por ejemplo, si efectúa el inicio desde una hoja de base de datos, la base de datos actual se rellena previamente. Si el inicio se realiza desde una hoja de colección, la colección actual será la que se rellene previamente.

	![Captura de pantalla del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

##<a id="Create"></a>Creación, edición y ejecución de consultas con el Explorador de consultas##

El Explorador de consultas permite crear, editar y ejecutar consultas fácilmente en una colección de DocumentDB. Incluye palabras clave básicas y valores destacados para mejorar la experiencia de creación de consultas.

- Cuando se abre por primera vez el Explorador de consultas, se proporciona una consulta predeterminada de SELECT * FROM c. Puede aceptar la consulta predeterminada o crear la suya propia; a continuación, haga clic en el botón **Ejecutar consulta** para ver los resultados. El Explorador de consultas es compatible con el lenguaje de consultas SQL de DocumentDB, tal y como se describe en [DocumentDB de consulta](documentdb-sql-query.md).

	![Captura de pantalla de los resultados del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryresults1.png)

- También puede especificar varias consultas, resaltar la que desee ejecutar y, a continuación, hacer clic en el botón **Ejecutar consulta** para ver los resultados.

	![Captura de pantalla de resaltar y ejecutar en el Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

- Puede cargar el contenido de un archivo existente con el comando **Cargar archivo** .

	![Captura de pantalla de la carga de archivo en el Explorador de consultas](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

- De forma predeterminada, el Explorador de consultas devuelve resultados en grupos de 100. Si la consulta genera más de 100 resultados, utilice los comandos **Página siguiente** y **Página anterior** para desplazarse por el conjunto de resultados.

	![Captura de pantalla de la compatibilidad con la paginación del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

- Las consultas con éxito proporcionan información como la siguiente: la carga de la solicitud, el conjunto de resultados que se muestra actualmente y si hay más resultados, a los que se puede acceder mediante el comando **Página siguiente**, tal y como se indicó anteriormente.

	![Captura de pantalla de información de consultas del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryinformation.png)

- Del mismo modo, si una consulta finaliza con errores, el Explorador de consultas muestra una lista de errores que puede ser útil a la hora de solucionar los problemas.

	![Captura de pantalla de errores de consulta del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryerror.png)

##<a name="NextSteps"></a>Pasos siguientes

- Para obtener más información sobre DocumentDB, haga clic [aquí](http://azure.com/docdb).
- Para obtener más información acerca de la gramática de SQL compatible con DocumentDB en el Explorador de consultas, haga clic [aquí](documentdb-sql-query.md).
 

<!---HONumber=AcomDC_1203_2015-->