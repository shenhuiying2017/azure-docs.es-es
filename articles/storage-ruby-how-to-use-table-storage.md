<properties 
	pageTitle="Uso del almacenamiento de tablas (Ruby) | Microsoft Azure" 
	description="Aprenda a usar el servicio de almacenamiento de tablas en Azure. Los ejemplos de código están escritos usando la API Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>





# Uso del servicio Tabla desde Ruby

Esta guía le indicará cómo actuar en situaciones habituales usando el servicio Tabla de Azure. Los ejemplos se han escrito usando la API Ruby. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla**. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes](#next-steps) .

## Tabla de contenido

* [¿Qué es el servicio Tabla?](#what-is)
* [Conceptos](#concepts)
* [Creación de una cuenta de almacenamiento de Azure](#create-a-windows-azure-storage-account)
* [Creación de una aplicación de Ruby](#create-a-ruby-application)
* [Configuración de la aplicación para acceder al almacenamiento](#configure-your-application-to-access-storage)
* [Configuración de una conexión de almacenamiento de Azure](#setup-a-windows-azure-storage-connection)
* [Establecimiento una tabla](#how-to-create-a-table)
* [Establecimiento una entidad a una tabla](#how-to-add-an-entity-to-a-table)
* [Adición de una entidad](#how-to-update-an-entity)
* [Uso de grupos de entidades](#how-to-work-with-groups-of-entities)
* [Uso de una entidad](#how-to-query-for-an-entity)
* [Uso de un conjunto de entidades](#how-to-query-a-set-of-entities)
* [Adición de un subconjunto de propiedades de las entidades](#how-to-query-a-subset-of-entity-properties)
* [Adición de una entidad](#how-to-delete-an-entity)
* [Establecimiento de una tabla](#how-to-delete-a-table)
* [Pasos siguientes](#next-steps)

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a id="create-a-windows-azure-storage-account"></a>Creación de una cuenta de almacenamiento de Azure

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Para obtener instrucciones, 
consulte [Creación de una aplicación de Ruby en Azure](/es-es/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Configuración de la aplicación para acceder al almacenamiento

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Ruby azure, 
que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba **gem install azure** en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Configuración de una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY** para obtener la información necesaria para conectarse a la cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de la cuenta antes de usar **Azure::TableService** con el siguiente código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

Para obtener estos valores:

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).

2. Vaya a la cuenta de almacenamiento que desea utilizar.

3. Haga clic en **ADMINISTRAR CLAVES** en la parte inferior del panel de navegación.

4. En el cuadro de diálogo emergente, verá el nombre de cuenta de almacenamiento, la clave de acceso principal y la clave de acceso secundaria. Para la clave de acceso, puede elegir la principal o la secundaria.

## <a id="how-to-create-a-table"></a>Creación de una tabla

El objeto **Azure::TableService** le permite trabajar con tablas y entidades. Para crear una tabla, use el método **create\_table()**. En el siguiente ejemplo se crea una tabla o se imprime el error, si hay alguno.

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## <a id="how-to-add-an-entity-to-a-table"></a>Incorporación de una entidad a una tabla

Para agregar una entidad, primero cree un objeto hash que defina las propiedades de la entidad. Tenga en cuenta que para cada entidad debe especificar valores en **PartitionKey** y **RowKey**. Estos son los identificadores exclusivos de sus entidades y son valores que se pueden consultar más rápidamente que las demás propiedades. El servicio de almacenamiento de Azure usa **PartitionKey** para distribuir automáticamente las entidades de la tabla entre varios nodos de almacenamiento. Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. **RowKey** es el identificador exclusivo de la entidad de la partición a la que pertenece. 

	entity = { "content" => "test entity", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## <a id="how-to-update-an-entity"></a>Procedimiento: de una entidad

Hay varios métodos para actualizar una entidad existente:

* **update\_entity():** Actualiza una entidad existente reemplazándola..
* **merge\_entity():** Actualiza una entidad existente combinando los nuevos valores de propiedad en la entidad existente.
* **insert\_or\_merge\_entity():** Actualiza una entidad existente reemplazándola. Si no hay entidades, se insertará una nueva:
* **insert\_or\_replace\_entity():** Actualiza una entidad existente combinando los nuevos valores de propiedad en la entidad existente. Si no hay entidades, se insertará una nueva.

En el ejemplo siguiente se muestra cómo actualizar una entidad mediante **update\_entity()**:

	entity = { "content" => "test entity with updated content", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Con **update\_entity()** y **merge\_entity()**, si la entidad que se está actualizando no existe, se producirá un error en la operación de actualización. Por lo tanto, si desea almacenar una entidad independientemente de si ya existe, debe usar **insert\_or\_replace\_entity()** o **insert\_or\_merge\_entity()**.

## <a id="how-to-work-with-groups-of-entities"></a>Procedimiento: grupos de entidades

A veces resulta útil enviar varias operaciones juntas en un lote a fin de garantizar el procesamiento atómico por parte del servidor. Para ello, primero debe crear un objeto **Batch** y, a continuación, usar el método **execute\_batch()** en **TableService**. El siguiente ejemplo muestra el envío de dos entidades con RowKey 2 y 3 en un lote. Tenga en cuenta que solo funciona para entidades con el mismo valor de PartitionKey.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable", 
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## <a id="how-to-query-for-an-entity"></a>Procedimiento: una entidad

Para consultar la entidad de una tabla, use el método **get\_entity()**, pasando el nombre de tabla, **PartitionKey** y **RowKey**.

	result = azure_table_service.get_entity("testtable", "test-partition-key", 
	  "1")

## <a id="how-to-query-a-set-of-entities"></a>Procedimiento: un conjunto de entidades

Para realizar una consulta en un conjunto de entidades de una tabla, cree un objeto hash de consulta y use el método **query\_entities()**. El siguiente ejemplo muestra la obtención de todas las entidades con el mismo valor de **PartitionKey**:

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

**Tenga en cuenta** que si el resultado establecido es demasiado largo para que lo devuelva una única consulta, se devolverá un token de continuación que puede usar para recuperar las páginas siguientes.

## <a id="how-to-query-a-subset-of-entity-properties"></a>Procedimiento: de un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada "proyección", reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Utilice la cláusula select y pase los nombres de las propiedades que quiera que lleguen al cliente.

	query = { :filter => "PartitionKey eq 'test-partition-key'", 
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## <a id="how-to-delete-an-entity"></a>Procedimiento: de una entidad

Para eliminar una entidad, use el método **delete\_entity()**. Tiene que pasar el nombre de la tabla que contiene la entidad, la PartitionKey y la RowKey de la entidad.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a id="how-to-delete-a-table"></a>Procedimiento: de una tabla

Para eliminar una tabla, use el método **delete\_table()** y pase el nombre de la tabla que desee eliminar.

		azure_table_service.delete_table("testtable")

## <a id="next-steps"></a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

* Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx)
* Visite el [blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* Visite el repositorio de [SDK de Azure para Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub.
<!--HONumber=42-->
