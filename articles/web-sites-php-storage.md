<properties 
	pageTitle="Creación de una aplicación web PHP en el Servicio de aplicaciones de Azure mediante el almacenamiento de Azure" 
	description="En este tutorial se explica cómo crear una aplicación web PHP en el Servicio de aplicaciones de Azure y usar el servicio de almacenamiento de tablas de Azure en el back-end." 
	services="app-service\web, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tomfitz"/>

# Creación de una aplicación web PHP en el Servicio de aplicaciones de Azure mediante el almacenamiento de Azure

En este tutorial se explica cómo crear una aplicación web PHP en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y usar el servicio de almacenamiento de tablas de Azure en el back-end. En este tutorial se asume que tiene [PHP][install-php] y un servidor web instalados en el equipo. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo, incluidos Windows, Mac y Linux. Una vez completada esta guía, dispondrá de una aplicación web PHP que se ejecuta en Azure y, además, podrá tener acceso al servicio de almacenamiento de tablas.
 
Aprenderá a:

* Instalar las bibliotecas de clientes de Azure e incluirlas en la aplicación.
* Usar las bibliotecas de clientes para crear tablas y para crear, consultar y eliminar entidades de tabla.
* Crear una cuenta de almacenamiento de Azure y configurar la aplicación para usarla.
* Crear una aplicación web de Azure e implementarla mediante Git.
 
Compilará una aplicación web Tasklist sencilla en PHP. A continuación se muestra una captura de pantalla de la aplicación completada:

![Aplicación web PHP de Azure][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web inicial de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Instalación de las bibliotecas de clientes de Azure

Para instalar las bibliotecas de clientes PHP para Azure mediante el compositor, siga estos pasos:

1. [Instalación de Git][install-git]

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

* Cree un archivo denominado **init.php**.

* En primer lugar, incluya el script del cargador automático:

		require_once 'vendor\autoload.php'; 
	
* Incluya los espacios de nombres que va a usar.

	Para crear un cliente de cualquier servicio de Azure necesario para usar la clase **ServicesBuilder**:

		use WindowsAzure\Common\ServicesBuilder;

	Para capturar las excepciones generadas por cualquier llamada API, necesita la clase **ServiceException**:

		use WindowsAzure\Common\ServiceException;
	
* Para crear una instancia del cliente de servicio, también necesitará una cadena de conexión válida. El formato de las cadenas de conexión del servicio de tabla es:

	Para obtener acceso a un servicio en directo:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Para obtener acceso al emulador de almacenamiento:
	
		UseDevelopmentStorage=true

* Use el método de la factoría `ServicesBuilder::createTableService` para crear una instancia de un contenedor en torno a las llamadas del servicio Tabla.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` contiene un método para cada llamada de REST disponible en las tablas de Azure.


## Creación de una tabla

Para poder almacenar datos, primero tiene que crear un contenedor para ellos (la tabla). 

* Cree un archivo denominado **createtable.php**.

* En primer lugar, incluya el script de inicialización que ya ha creado. Incluirá este script en cada archivo que obtenga acceso a Azure:

		<?php
		require_once "init.php";

* A continuación, realice una llamada a *createTable* para lo que es necesario transferir el nombre de la tabla. Al igual que sucede con otros almacenes de tablas NoSQL, no se necesita ningún esquema para las tablas de Azure.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Los mensajes y los códigos de error se pueden encontrar aquí: [http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]


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

* Para consultar las tablas de Azure para **todas las entidades** almacenadas en la tabla  *tasks*, debe llamar al método  *queryEntities* y pasar solo el nombre de la tabla. En la sección **Actualización de una entidad** que encontrará más adelante también se explicará cómo pasar una consulta filtrada de una entidad específica.

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

* Una vez que obtenga una `Entity`, el modelo para leer los datos es `Entity->getPropertyValue('[name]')`:

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

* El primer paso para insertar una entidad es crear una instancia de un objeto  `Entity` y configurar las propiedades:
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* A continuación, puede pasar la entidad "$entity" que acaba de crear al método  `insertEntity`:

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

La aplicación de lista de tareas tiene la capacidad de marcar un elemento como completado y también de desmarcarlo. La página principal transfiere los valores *RowKey* and *PartitionKey* de una entidad y el estado del destino (marcado==1, desmarcado==0).

* Cree un archivo denominado **markitem.php** y agregue la parte de la inicialización:

		<?php		
		require_once "init.php";
		

* El primer paso para actualizar una entidad es capturarla de la tabla:
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Como puede observar, el filtro de consulta transferido presenta la forma "Key eq 'Value'". Puede encontrar la descripción completa de la sintaxis de la consulta [aquí][msdn-table-query-syntax].

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

La eliminación de un elemento se realiza con una única llamada a  `deleteItem`. Los valores transferidos son **PartitionKey** y **RowKey**, que en conjunto constituyen la clave principal de la entidad. Cree un archivo denominado **deleteitem.php** e inserte el código siguiente:

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Creación de una cuenta de almacenamiento de Azure

Para que la aplicación pueda almacenar datos en la nube, primero necesita crear una cuenta de almacenamiento en Azure y, a continuación, pasar la información de autenticación adecuada a la clase  *Configuration*.

1. Inicie sesión en el [Portal de Azure][management-portal].

2. Haga clic en el icono **Nuevo** situado en la parte inferior izquierda del portal y haga clic en **Datos + Almacenamiento** > **Almacenamiento**. Asigne un nombre único a la cuenta de almacenamiento y cree un [grupo de recursos] nuevo(azure-preview-portal-using-resource-groups.md) para dicha cuenta.

	![Creación de una cuenta de almacenamiento nueva][storage-quick-create]
	
	Una vez creada la cuenta de almacenamiento, el botón **Notificaciones** emitirá el mensaje **CORRECTO** en color verde y la hoja de la cuenta de almacenamiento se abrirá para mostrar que pertenece al nuevo grupo de recursos que ha creado.

5. Haga clic en la parte **Configuración** de la hoja de la cuenta de almacenamiento. Anote el nombre de la cuenta y la clave principal.

	![Selección de Administrar claves][storage-access-keys]

7. Abra **init.php** y reemplace "[YOUR_STORAGE_ACCOUNT_NAME]" y "[YOUR_STORAGE_ACCOUNT_KEY]" por el nombre de cuenta y la clave que anotó en el último paso. Guarde el archivo.

## Creación de una aplicación web de Azure y configuración de la publicación Git

Siga estos pasos para crear una aplicación web de Azure:

1. Inicie sesión en el [Portal de Azure][management-portal].

2. Crear una aplicación web vacía siguiendo las instrucciones que se incluyen en [Creación de una aplicación web con el Portal de Azure](web-sites-create-deploy.md#createawebsiteportal). Asegúrese de crear un nuevo [plan del Servicio de aplicaciones](azure-web-sites-web-hosting-plans-in-depth-overview) y seleccione el grupo de recursos que creó anteriormente para la cuenta de almacenamiento.

	Una vez creada la aplicación web, el botón **Notificaciones** emitirá el mensaje **CORRECTO** en color verde y la hoja de la aplicación web se abrirá para mostrar que pertenece al nuevo grupo de recursos que ha creado.

6. En la hoja de la aplicación web, haga clic en **Configurar la implementación continua**y elija **Repositorio de Git local**. Haga clic en **Aceptar**.

	![Configuración de la publicación Git][setup-git-publishing]

7. Para poder implementar el repositorio de Git local en Azure, también debe configurar las credenciales de implementación. En la hoja de la aplicación web, haga clic en **Toda la configuración** > **Credenciales de implementación** para configurar las credenciales. Cuando termine, haga clic en **Guardar**.

	![Creación de credenciales de publicación][credentials]

	Se tardará unos segundos en configurar el repositorio.

8. Una vez que el repositorio de Git esté listo, debe insertar los cambios en él. Encontrará la dirección URL del repositorio haciendo clic en la misma parte de implementación de la hoja de la aplicación web. 

	![Instrucciones de implementación de GIT devueltas después de crear un repositorio para la aplicación web.][git-instructions]

	Tenga en cuenta las instrucciones, ya que se usarán en la próxima sección para publicar la aplicación.

##Publicación de la aplicación

Para publicar la aplicación con Git, siga los pasos que se describen a continuación.

1. Abra la carpeta **vendor/microsoft/windowsazure** ubicada en la raíz de la aplicación y elimine los siguientes archivos y carpetas:
	* .git
	* .gitattributes
	* .gitignore
			
	Cuando el administrador de paquetes del compositor descarga las bibliotecas de clientes de Azure y sus dependencias, clona el repositorio de GitHub en el que residen. En el paso siguiente, la aplicación se implementará mediante Git, para lo que se creará un repositorio fuera de la carpeta raíz de la aplicación. Git ignorará el subrepositorio en el que residen las bibliotecas de clientes, a menos que se eliminen los archivos específicos del repositorio.

2. Abra GitBash (o un terminal, si Git está en su  `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

3. Vaya a **http://[dominio de la aplicación web]/createtable.php** para crear la tabla para la aplicación.
4. Vaya a **http://[dominio de la aplicación web]/index.php** para empezar a usar la aplicación.

Una vez publicada la aplicación, podrá comenzar a realizar cambios en esta y a usar Git para publicarlos. 

##Publicación de cambios de la aplicación

Para publicar los cambios de la aplicación, siga estos pasos:

1. Realice los cambios en la aplicación localmente.
2. Abra GitBash (o un terminal, si Git está en su  `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

3. Diríjase a **http://[[dominio del sitio web]]/index.php** para ver los cambios. 

## Lo que ha cambiado
* Para obtener una guía para cambiar de sitios web al servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía para cambiar del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-php]: http://www.php.net/manual/en/install.php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx

[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://portal.azure.com

[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png

[git-instructions]: ./media/web-sites-php-storage/git-instructions.png


<!--HONumber=52-->