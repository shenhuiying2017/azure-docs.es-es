<properties 
	pageTitle="Sitio web PHP con almacenamiento de tablas - Tutorial de Azure" 
	description="En este tutorial se explica cómo crear un sitio web PHP y usar el servicio de almacenamiento de tablas de Azure en el back-end." 
	services="web-sites, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="tomfitz"/>

#Creación de un sitio web PHP con Almacenamiento de Azure

En este tutorial se explica cómo crear un sitio web PHP y usar el servicio de almacenamiento de tablas de Azure en el back-end. En este tutorial se asume que tiene [PHP][install-php] y un servidor web instalados en el equipo. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo, incluidos Windows, Mac y Linux. Una vez completada esta guía, dispondrá de un sitio web PHP que se ejecuta en Azure y, además, podrá tener acceso al servicio de almacenamiento de tablas.
 
Aprenderá a:

* Instalar las bibliotecas de clientes de Azure e incluirlas en la aplicación.
* Usar las bibliotecas de clientes para crear tablas y para crear, consultar y eliminar entidades de tabla.
* Crear una cuenta de almacenamiento de Azure y configurar la aplicación para usarla.
* Crear un sitio web de Azure e implementarlo mediante Git.
 
Compilará una aplicación web Tasklist sencilla en PHP. A continuación se muestra una captura de pantalla de la aplicación completada:

![Azure PHP web site][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Instalación de las bibliotecas de clientes de Azure

Para instalar las bibliotecas de clientes PHP para Azure mediante el compositor, siga estos pasos:

1. [Instalar Git][install-git]

	> [AZURE.NOTE]
	> En Windows, también tendrá que agregar el archivo ejecutable Git a la variable de entorno PATH.

2. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.

4. Abra un símbolo del sistema y ejecute esto en la raíz del proyecto

		php composer.phar install

##Introducción a las bibliotecas de clientes

Hay que seguir cuatro pasos básicos para poder realizar una llamada a la API de Azure al usar las bibliotecas. Creará un script de inicialización que ejecutará estos pasos.

* Cree un archivo llamado **init.php**.

* En primer lugar, incluya el script del cargador automático:

		require_once 'vendor\autoload.php'; 
	
* Incluya los espacios de nombres que va a usar.

	Para crear un cliente de cualquier servicio de Azure necesario para utilizar la clase **ServicesBuilder**:

		use WindowsAzure\Common\ServicesBuilder;

	Para capturar las excepciones generadas por cualquier llamada API necesita la clase **ServiceException**:

		use WindowsAzure\Common\ServiceException;
	
* Para crear una instancia del cliente de servicio, también necesitará una cadena de conexión válida. El formato de las cadenas de conexión del servicio de tabla es:

	Para obtener acceso a un servicio en directo:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Para obtener acceso al emulador de almacenamiento:
	
		UseDevelopmentStorage=true

* Use el método Factory Method  `ServicesBuilder::createTableService` para crear una instancia de un contenedor en torno a las llamadas del servicio de tabla.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` contains a method for every REST call available on Azure Tables.


## Creación de una tabla

Para poder almacenar datos, primero tiene que crear un contenedor para ellos (la tabla). 

* Cree un archivo denominado **createtable.php**.

* En primer lugar, incluya el script de inicialización que ya ha creado. Incluirá este script en cada archivo que obtenga acceso a Azure:

		<?php
		require_once "init.php";

* A continuación, realice una llamada a *createTable* pasando el nombre de la tabla. Al igual que sucede con otros almacenes de tablas NoSQL, no se necesita ningún esquema para las tablas de Azure.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Los mensajes y los códigos de error se pueden encontrar aquí: [http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx][msdn-errors]


##Consulta de una tabla

En la página principal de la aplicación Tasklist deben aparecer todas las tareas existentes y, además, se debe permitir la inserción de tareas nuevas.

* Cree un archivo denominado **index.php** e inserte el siguiente código HTML y PHP, que formarán el encabezado de la página:
	
		<html>
		<head>
			<title>Index</title>
			<style type="text/css">
			    body { background-color: #fff; border-top: solid 10px #000;
			        color: #333; font-size: .85em; margin: 20; padding: 20;
			        font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			    }
			    h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			    h1 { font-size: 2em; }
			    h2 { font-size: 1.75em; }
			    h3 { font-size: 1.2em; }
			    table { margin-top: 0.75em; }
			    th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			    td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
			</style>
		</head>
		<body>
		<h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
		<?php		
		require_once "init.php";

* Para consultar las tablas de Azure de **todas las entidades** almacenadas en la tabla *tasks*, llame al método *queryEntities* y pásele solo el nombre de la tabla. En la sección**Actualización de una entidad** que encontrará más adelante también se explicará cómo pasar una consulta filtrada de una entidad específica.

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* Para iterar las entidades en el conjunto de resultados:

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* Una vez obtenida una  `Entity`, el modelo para leer los datos es  `Entity->getPropertyValue('[name]')`:

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Name</td>
					<td>Category</td>
					<td>Date</td>
					<td>Mark Complete?</td>
					<td>Delete?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>No items on list.</h3>";
		?>

* Last, you must insert the form that feeds data into the task insertion script and complete the HTML:

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Item Name: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Category: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Date: </td>
						<td><input name="date" type="textbox"/></td>
					</tr>
				</table>
				<input type="submit" value="Add item"/>
			</form>
		</body>
		</html>

## Inserción de entidades en una tabla

La aplicación ahora puede leer todos los elementos almacenados en la tabla. Como en principio no habrá ninguna, vamos a agregar una función que escriba datos en la base de datos.

* Cree un archivo denominado **additem.php**.

* Agregue lo siguiente al archivo:

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* El primer paso para insertar una entidad es crear una instancia de un objeto  `Entity` y configurar sus propiedades:
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* A continuación, puede pasar la entidad `$entity` que acaba de crear al método  `insertEntity`:

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Por último, para que la página vuelva a la página principal después de insertar la entidad:

		header('Location: index.php');		
		?>
	
## Actualización de una entidad

La aplicación de lista de tareas tiene la capacidad de marcar un elemento como completado y también de desmarcarlo. La página principal que se pasa en las *RowKey* y *PartitionKey* de una entidad y el estado de destino (marked==1, unmarked==0).

* Cree un archivo llamado **markitem.php** y agregue el elemento de inicialización:

		<?php		
		require_once "init.php";
		

* El primer paso para actualizar una entidad es capturarla de la tabla:
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Como puede observar, el filtro de consulta transferido presenta la forma `Key eq 'Value'`. Puede encontrar la descripción completa de la sintaxis de la consulta [aquí][msdn-table-query-syntax].

* A continuación, puede cambiar cualquier propiedad:

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* Y el método  `updateEntity` realiza la actualización:

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Para que la página vuelva a la página principal después de insertar la entidad:

		header('Location: index.php');		
		?>


## Eliminación de una entidad

La eliminación de un elemento se realiza con una única llamada a  `deleteItem`. Los valores transferidos son **PartitionKey** y **RowKey**, que en conjunto constituyen la clave principal de la entidad. Cree un archivo llamado **deleteitem.php** e inserte el código siguiente:

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Creación de una cuenta de almacenamiento de Azure

Para que la aplicación pueda almacenar datos en la nube, primero necesita crear una cuenta de almacenamiento en Azure y, a continuación, pasar la información de autenticación adecuada a la clase *Configuration*.

1. Inicie sesión en el [Portal de administración de Azure][management-portal].

2. Haga clic en el icono **+ Nuevo** en la parte inferior izquierda del portal.

	![Create New Azure web site][new-website]

3. Haga clic en **Servicios de datos**, **Almacenamiento** y, a continuación, haga clic en**Creación rápida**.

	![Custom Create a new web site][storage-quick-create]
	
	Escriba un valor de**URL** y seleccione el centro de datos de su sitio web en la lista desplegable **REGIÓN**. Haga clic en el botón **Crear cuenta de almacenamiento** en la parte inferior del cuadro de diálogo.

	![Fill in web site details][storage-quick-create-details]

	Una vez creada la cuenta de almacenamiento, se mostrará el texto **La creación de la cuenta de almacenamiento "[NOMBRE]" se ha completado correctamente**.

4. Asegúrese de que la pestaña**Almacenamiento** esté seleccionada y, a continuación, seleccione en la lista la cuenta de almacenamiento que acaba de crear.

5. Haga clic en **Administrar claves de acceso** en la barra de aplicaciones de la parte inferior.

	![Select Manage Keys][storage-manage-keys]

6. Anote el nombre de la cuenta de almacenamiento que ha creado y también la clave principal.

	![Select Manage Keys][storage-access-keys]

7. Abra **init.php** y reemplace `[YOUR_STORAGE_ACCOUNT_NAME]` y `[YOUR_STORAGE_ACCOUNT_KEY]` por la clave y el nombre de cuenta que ha anotado en el paso anterior. Guarde el archivo.


## Creación de un sitio web de Azure y configuración de la publicación Git

Siga estos pasos para crear un sitio web de Azure:

1. Inicie sesión en el [Portal de administración de Azure][management-portal].
2. Haga clic en el icono **+ Nuevo** en la parte inferior izquierda del portal.

	![Create New Azure Web Site][new-website]

3. Haga clic en **Proceso**, **Sitio web** y **Creación rápida**.

	![Custom Create a new web site][website-quick-create]
	
	Escriba un valor de**URL** y seleccione el centro de datos de su sitio web en la lista desplegable **REGIÓN**. Haga clic en el botón **Crear nuevo sitio web** en la parte inferior del cuadro de diálogo.

	![Fill in web site details][website-quick-create-details]

	Una vez creado el sitio web, se mostrará el texto**La creación del sitio web "[NOMBRE DEL SITIO]" se ha realizado correctamente**. Ahora puede habilitarse la publicación Git.

5. Haga clic en el nombre del sitio web que se muestra en la lista para abrir el panel**INICIO RÁPIDO**.

	![Open web site dashboard][go-to-dashboard]


6. En la parte inferior derecha de la página de inicio rápido, seleccione **Set up a deployment from source control**.

	![Set up Git publishing][setup-git-publishing]

6. Cuando se le pregunte "Where is your source code?" (¿Dónde está su código fuente?), seleccione **Local Git repository** y, a continuación, haga clic en la flecha.

	![where is your source code][where-is-code]

7. Para habilitar la publicación Git, debe proporcionar un nombre de usuario y una contraseña. Tome nota de ambos. (Si ya ha configurado un repositorio Git anteriormente, este paso se omitirá).

	![Create publishing credentials][credentials]

	Se tardará unos segundos en configurar el repositorio.

8. Una vez esté listo el repositorio Git, se le mostrarán las instrucciones sobre los comandos Git que debe utilizar para configurar un repositorio local y, a continuación, se insertarán los archivos en Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

	Tenga en cuenta las instrucciones, ya que se usarán en la próxima sección para publicar la aplicación.

##Publicación de la aplicación

Para publicar la aplicación con Git, siga los pasos que se describen a continuación.

1. Abra la carpeta **vendor/microsoft/windowsazure** ubicada en la raíz de la aplicación y elimine los siguientes archivos y carpetas:
	* .git
	* .gitattributes
	* .gitignore
			
	Cuando el administrador de paquetes del compositor descarga las bibliotecas de clientes de Azure y sus dependencias, clona el repositorio de GitHub en el que residen. En el paso siguiente, la aplicación se implementará mediante Git, para lo que se creará un repositorio fuera de la carpeta raíz de la aplicación. Git ignorará el subrepositorio en el que residen las bibliotecas de clientes, a menos que se eliminen los archivos específicos del repositorio.

2. Abra GitBash (o un terminal, si Git está en su `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos (**Nota:** estos son los mismos pasos que se indican al final de la sección **Creación de un sitio web de Azure y configuración de la publicación Git**):

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

3. Vaya a **http://[el dominio de su sitio web]/createtable.php** para crear la tabla de la aplicación.
4. Vaya a **http://[el dominio de su sitio web]/index.php** para comenzar a usar la aplicación.

Una vez publicada la aplicación, podrá comenzar a realizar cambios en esta y a usar Git para publicarlos. 

##Publicación de cambios de la aplicación

Para publicar los cambios de la aplicación, siga estos pasos:

1. Realice los cambios en la aplicación localmente.
2. Abra GitBash (o un terminal, si Git está en su  `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

3. Vaya a **http://[el dominio de su sitio web]/index.php** para ver los cambios. 

[install-php]: http://www.php.net/manual/en/install.php


[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar

[msdn-errors]: http://msdn.microsoft.com/es-es/library/windowsazure/dd179438.aspx



[msdn-table-query-syntax]: http://msdn.microsoft.com/es-es/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://manage.windowsazure.com
[new-website]: ./media/web-sites-php-storage/new_website.jpg

[website-quick-create]: ./media/web-sites-php-storage/createsite.png
[website-quick-create-details]: ./media/web-sites-php-storage/sitedetails.png
[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-quick-create-details]: ./media/web-sites-php-storage/provideurl.png
[storage-manage-keys]: ./media/web-sites-php-storage/accesskeys.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[go-to-dashboard]: ./media/web-sites-php-storage/selectsite.png
[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
[where-is-code]: ./media/web-sites-php-storage/where_is_code.png




<!--HONumber=42-->
