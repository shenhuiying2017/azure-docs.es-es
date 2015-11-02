<properties
	pageTitle="Ver procedimientos almacenados, desencadenadores y funciones definidas por el usuario mediante el Explorador de scripts de DocumentDB | Microsoft Azure"
	description="Obtenga información sobre el Explorador de scripts de Microsoft Azure DocumentDB, una herramienta del portal de vista previa de Azure para ver los artefactos de programación del lado servidor de DocumentDB, incluidos procedimientos almacenados, desencadenadores y funciones definidas por el usuario."
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
	ms.topic="article" 
	ms.date="09/02/2015"
	ms.author="anhoh"/>

# Ver, editar y crear procedimientos almacenados, desencadenadores y funciones definidas por el usuario mediante el Explorador de scripts de DocumentDB

En este artículo se ofrece información general sobre el Explorador de scripts de [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), una herramienta del portal de vista previa de Azure que le permite ver los artefactos de programación del lado servidor de DocumentDB, incluidos procedimientos almacenados, desencadenadores y funciones definidas por el usuario. Obtenga más información sobre la programación del lado servidor de DocumentDB [aquí](documentdb-programming.md).

Después de completar este tutorial, podrá responder a las preguntas siguientes:

-	¿Cómo puedo ver fácilmente los procedimientos almacenado de DocumentDB mediante un explorador web?
-	¿Cómo puedo ver fácilmente los desencadenadores de DocumentDB mediante un explorador web?
-	¿Cómo puedo ver fácilmente las funciones definidas por el usuario de DocumentDB mediante un explorador web?

## Iniciar y navegar por el Explorador de scripts

El Explorador de scripts se puede iniciar desde cualquier hoja, colección, base de datos o cuenta de DocumentDB.

1. En la parte superior de la hoja de la base de datos o la cuenta de DocumentDB, haga simplemente clic en el comando **Explorador de scripts**.

	![Captura de pantalla del comando del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. De forma alternativa, cerca de la parte inferior de cada hoja aparece el modo **Herramientas de desarrollo** que contiene la parte del **Explorador de scripts**.

	![Captura de pantalla de la parte del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. Simplemente haga clic en el comando o en la parte para iniciar el Explorador de scripts.

	<p>Los cuadros de listas desplegables **Base de datos** y **Colección** se rellenan previamente según el contexto en el que se ejecute el Explorador de scripts. Por ejemplo, si efectúa el inicio desde una hoja de base de datos, la base de datos actual se rellena previamente. Si el inicio se realiza desde una hoja de colección, la colección actual será la que se rellene previamente.

	![Captura de pantalla del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3. Los cuadros de listas desplegables **Base de datos** y **Colección** pueden usarse para cambiar fácilmente la colección desde la que se ven los scripts en ese momento sin tener que cerrar y reiniciar el Explorador de scripts.

4. Asimismo, el Explorador de scripts también admite el filtrado del conjunto de scripts cargado actualmente por su propiedad de identificador. Solo tiene que escribir en el cuadro de filtro.

	![Captura de pantalla del Explorador de scripts con el filtro resaltado](./media/documentdb-view-scripts/scriptexplorerfilter.png)

	Los resultados de la lista del Explorador de scripts se filtran en función de los criterios proporcionados.

	![Captura de pantalla del Explorador de scripts con los resultados filtrados](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT]La funcionalidad de filtro del Explorador de scripts solo filtra desde el conjunto de documentos cargado ***actualmente*** y no actualiza la colección seleccionada actualmente.

5. Para actualizar la lista de scripts cargados en el Explorador de scripts, simplemente haga clic en el comando **Actualizar** de la parte superior de la hoja.

	![Captura de pantalla del comando actualizar del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Ver, editar, crear y eliminar procedimientos almacenados, desencadenadores y funciones definidas por el usuario mediante el Explorador de scripts

El Explorador de scripts le permite llevar a cabo con facilidad operaciones de CRUD en artefactos de programación del lado de servidor de DocumentDB.

- Para crear un script, solo tiene que hacer clic en el comando para crear correspondiente del Explorador de scripts, ofrecer un id., escribir el contenido del script y hacer clic en el comando **Guardar**.

	![Captura de pantalla de la opción de crear del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- Al crear un desencadenador, debe especificar también la operación del desencadenador y del tipo de desencadenador.

	![Captura de pantalla de la opción de crear desencadenador del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- Para ver un script, basta con hacer clic en el script que le interesa.

	![Captura de pantalla de la experiencia de vista de script del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Para editar un script, solo tiene que realizar los cambios que desee y hacer clic en el comando **Guardar**.

	![Captura de pantalla de la experiencia de vista de script del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- Para descartar los cambios pendientes a un script, solo tiene que hacer clic en el comando **Descartar**.

	![Captura de pantalla de la experiencia de descartar cambios del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- El Explorador de scripts también le permite ver fácilmente las propiedades del sistema del script cargado actualmente, si hace clic en el comando **Propiedades**.

	![Captura de pantalla de la vista de propiedades de script del Explorador de scripts](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE]La propiedad de marca de tiempo (\_ts) se representa internamente como tiempo de época, pero el Explorador de scripts muestra el valor en formato GMT en lenguaje natural.

- Para eliminar un script, selecciónelo en el Explorador de scripts y haga clic en el comando **Eliminar**.

	![Captura de pantalla del comando eliminar del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- Para confirmar la acción de eliminación, haga clic en **Sí** o para cancelar la acción de eliminación, haga clic en **No**.

	![Captura de pantalla del comando eliminar del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## Pasos siguientes

Para obtener más información sobre DocumentDB, haga clic [aquí](http://azure.com/docdb).
 

<!---HONumber=Oct15_HO4-->