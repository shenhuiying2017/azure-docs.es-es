<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="" solutions="" manager="" editor="" />

Uso del servicio Tabla desde Node.js
====================================

Esta guía le indicará cómo actuar en situaciones habituales usando el servicio Tabla de Azure. Los ejemplos se han escrito usando la API Node.js. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla**. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [żQué es el servicio Tabla?](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#create-account)
-   [Creación de una aplicación Node.js](#create-app)
-   [Configuración de su aplicación para obtener acceso al almacenamiento](#configure-access)
-   [Configuración de una conexión de almacenamiento de Azure](#setup-connection-string)
-   [Creación de una tabla](#create-table)
-   [Incorporación de una entidad a una tabla](#add-entity)
-   [Actualización de una entidad](#update-entity)
-   [Trabajo con grupos de entidades](#change-entities)
-   [Consulta de una entidad](#query-for-entity)
-   [Consulta de un conjunto de entidades](#query-set-entities)
-   [Consulta de un subconjunto de propiedades de las entidades](#query-entity-properties)
-   [Eliminación de una entidad](#delete-entity)
-   [Eliminación de una tabla](#delete-table)
-   [Pasos siguientes](#next-steps)

żQué es el servicio Tabla?
--------------------------

El servicio Tabla de Azure almacena grandes cantidades de datos estructurados. Este servicio acepta llamadas autenticadas de dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales. Entre los usos habituales de los servicios Tabla se incluyen los siguientes:

-   Almacenamiento de una gran cantidad de datos estructurados (muchos TB) que se escala automáticamente para satisfacer las demandas de rendimiento
-   Almacenamiento de conjuntos de datos que no requieren uniones complejas, claves externas o procedimientos almacenados y que pueden desnormalizarse para obtener un acceso rápido
-   Consulta rápida de datos como los perfiles de usuario mediante un índice agrupado

Puede usar el servicio Tabla para apara almacenar cientos de datos estructurados no relacionales y realizar consultas sobre ellos, y las tablas se escalarán a medida que aumente el volumen.

Conceptos
---------

El servicio Tabla contiene los siguientes componentes:

![Tabla1](./media/storage-nodejs-how-to-use-table-storage/table1.png)

-   **Formato de dirección URL:** El código se desvía a las tablas en una cuenta con este formato de dirección:

        http://storageaccount.table.core.windows.net/table  

    Puede desviar las tablas de Azure directamente mediante esta dirección con el protocolo OData. Para obtener más información, consulte [OData.org](http://www.odata.org/).

-   **Cuenta de almacenamiento:** Todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. El tamańo total del contenido del blob, la tabla y la cola de una cuenta de almacenamiento no puede superar los 100 TB.

-   **Tabla**: Una tabla es una colección ilimitada de entidades. Las tablas no exigen un esquema sobre entidades, lo que significa que una única tabla puede contener entidades que dispongan de diferentes conjuntos de propiedades. Una cuenta puede contener varias tablas.

-   **Entidad**: Una entidad es un conjunto de propiedades, similar a una fila de base de datos. Una entidad puede tener un tamańo de hasta 1 MB.

-   **Propiedades**: Una propiedad es un par nombre-valor. Cada entidad puede incluir hasta 252 propiedades para almacenar datos. Cada entidad dispone también de tres propiedades del sistema que especifican una clave de partición, una clave de fila y una marca de tiempo. Pueden realizarse consultas en las entidades con la misma partición de manera más rápida e insertarse o actualizarse en operaciones atómicas. Una clave de fila de la entidad es el identificador exclusivo en una partición.

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

Necesita una cuenta de almacenamiento de Azure para usar operaciones de almacenamiento. Siga estos pasos para crear una cuenta de almacenamiento. También puede crear una cuenta de almacenamiento [usando la API de REST](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx).

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  En la parte inferior del panel de navegación, haga clic en **+NEW**.

    ![+new](./media/storage-nodejs-how-to-use-table-storage/plus-new.png)

3.  Haga clic en **Cuenta de almacenamiento** y, a continuación, en **Quick Create**.

    ![Cuadro de diálogo de creación rápida](./media/storage-nodejs-how-to-use-table-storage/quick-storage.png)

4.  En URL, escriba el nombre de subdominio que vaya usar en el URI para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

5.  Seleccione un grupo de región/afinidad en el que ubicar el almacenamiento. Si va a usar el almacenamiento desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

6.  Haga clic en **Create Storage Account**.

Creación de una aplicación Node.js
----------------------------------

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un Sitio web Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Servicio en la nube Node.js]({localLink:2221} "Aplicación web con Express") (usando Windows PowerShell) o [Sitio web con WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configuración de su aplicación para obtener acceso al almacenamiento
--------------------------------------------------------------------

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Node.js azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure** en la ventana de comandos. Esto debería devolver la salida siguiente:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, busque el paquete **azure**, que contiene las bibliotecas necesarias para obtener acceso al almacenamiento.

### Importación del paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo **server.js** de la aplicación en la que pretenda usar el almacenamiento:

    var azure = require('azure');

Configuración de una conexión de almacenamiento de Azure
--------------------------------------------------------

El módulo azure leerá las variables de entorno AZURE\_STORAGE\_ACCOUNT y AZURE\_STORAGE\_ACCESS\_KEY para obtener la información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no configura estas variables de entorno, debe especificar la información de la cuenta al llamar a **TableService**.

Para ver un ejemplo de cómo configurar las variables de entorno en un archivo de configuración para un servicio de nube de Azure, consulte [Servicio de nube de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-site-with-storage/).

Creación de una tabla
---------------------

El código siguiente crea un objeto **TableService** que usa para crear una tabla. Agregue lo siguiente cerca de la parte superior de **server.js**.

    var tableService = azure.createTableService();

La llamada a **createTableIfNotExists** devolverá la tabla especificada si existe, o creará una nueva con el nombre especificado, si es que todavía no existe. El ejemplo siguiente crea una tabla llamada "mytable", si es que no existe todavía:

    tableService.createTableIfNotExists('mytable', function(error){
        if(!error){
            // La tabla ya existe o se ha creado.
        }
    });

### Filtros

Las operaciones opcionales de filtrado se pueden aplicar a las operaciones realizadas usando **TableService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

     function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

     function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **TableService** que usa **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableService = azure.createTableService().withFilter(retryOperations);

Incorporación de una entidad a una tabla
----------------------------------------

Para agregar una entidad, primero cree un objeto que defina las propiedades de su entidad y los tipos de datos. Tenga en cuenta que para cada entidad debe especificar valores en **PartitionKey** y **RowKey**. Estos son los identificadores exclusivos de sus entidades y son valores que se pueden consultar más rápidamente que las demás propiedades. El sistema usa **PartitionKey** para distribuir automáticamente las entidades de la tabla entre varios nodos de almacenamiento. Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. La **RowKey** es el identificador exclusivo de la entidad de la partición a la que pertenece. Para agregar una entidad a su tabla, pase el objeto de la entidad al método **insertEntity**.

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Take out the trash'
        , DueDate: new Date(2012, 6, 20)
    };
    tableService.insertEntity('mytable', task, function(error){
        if(!error){
            // La entidad se ha insertado.
        }
    });

Actualización de una entidad
----------------------------

Hay varios métodos para actualizar una entidad existente:

-   **updateEntity**: Actualiza una entidad que ya existe reemplazándola.

-   **mergeEntity**: Actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con la entidad existente.

-   **insertOrReplaceEntity**: Actualiza una entidad existente reemplazándola. Si no hay entidades, se insertará una nueva.

-   **insertOrMergeEntity**: Actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con los existentes. Si no hay entidades, se insertará una nueva.

El ejemplo siguiente demuestra cómo actualizar una entidad usando **updateEntity**:

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Wash Dishes'
    }
    tableService.updateEntity('mytable', task, function(error){
        if(!error){
            // La entidad se ha actualizado.
        }
    });

Con **updateEntity** y **mergeEntity**, si la entidad que se está actualizando no existe, se producirá un error en la operación de actualización. Por lo tanto, si desea almacenar una entidad independientemente de si ya existe, debe usar **insertOrReplaceEntity** o **insertOrMergeEntity**.

Trabajo con grupos de entidades
-------------------------------

A veces resulta útil enviar varias operaciones juntas en un lote a fin de garantizar el procesamiento atómico por parte del servidor. Para ello, debe usar el método **beginBatch** en **TableService** y, a continuación, llamar las series de operaciones como de costumbre. La diferencia es que las funciones de devolución de llamada de estos operadores indicarán que la operación se ha incluido en lotes, no que se ha enviado al servidor. Cuando quiera enviar el lote, llame a **commitBatch**. La devolución de llamada enviada a dicho método indicará si se ha enviado correctamente todo el lote. El ejemplo siguiente demuestra cómo enviar dos entidades en un lote:

    var tasks=[
        {
            PartitionKey : 'hometasks'
            , RowKey : '1'
            , Description : 'Take out the trash.'
            , DueDate: new Date(2012, 6, 20)
        }
        , {
            PartitionKey : 'hometasks'
            , RowKey : '2'
            , Description : 'Wash the dishes.'
            , DueDate: new Date(2012, 6, 20)
        }
    ]
    tableService.beginBatch();
    var async=require('async');

    async.forEach(tasks
        , function taskIterator(task, callback){
            tableService.insertEntity('mytable', task, function(error){
                if(!error){
                    // La entidad se ha insertado.
                    callback(null);
                } else {
                    callback(error);
                }
            });
        }
        , function(error){
            if(!error){
                // Todas las inserciones se han completado.
                tableService.commitBatch(function(error){
                    if(!error){
                        // El lote se ha confirmado correctamente.
                    }
                });
            }
        });

**Nota:**

Los ejemplos de arriba utilizan el módulo "async" para garantizar el correcto envío de las entidades antes de llamar a **commitBatch**.

Consulta de una entidad
-----------------------

Para consultar la entidad de una tabla, use el método **queryEntity**, pasando la **PartitionKey** y la **RowKey**.

    tableService.queryEntity('mytable'
        , 'hometasks'
        , '1'
        , function(error, entity){
            if(!error){
                // La entidad contiene la entidad devuelta.
            }
        });

Consulta de un conjunto de entidades
------------------------------------

A fin de consultar una tabla, use el objeto **TableQuery** para crear una expresión de consulta que use cláusulas como **select**, **from**, **where** (incluyendo cláusulas como **wherePartitionKey**, **whereRowKey**, **whereNextPartitionKey** y **whereNextRowKey**), **and**, **or** y **top**. A continuación, pase la expresión de consulta al método **queryEntities**. Puede usar los resultados de un bucle **for** dentro de una devolución de llamada.

Este ejemplo busca todas las tareas de Seattle en función de la **PartitionKey**.

    var query = azure.TableQuery
        .select()
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if(!error){
            // Las entidades contienen una matriz de entidades.
        }
    });

Consulta de un subconjunto de propiedades de las entidades
----------------------------------------------------------

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamańo. Utilice la cláusula **select** y pase los nombres de las propiedades que quiera que lleguen al cliente.

La consulta del código siguiente solo devuelve las **descripciones** de las entidades de la tabla; tenga en cuenta que, en la salida del programa, **DueDate** aparecerá como **undefined** porque el servidor no lo ha enviado.

**Nota:**

Tenga en cuenta que el fragmento siguiente solo funciona con el servicio de almacenamiento en la nube, el emulador de almacenamiento no admite la palabra clave **select**.

    var query = azure.TableQuery
        .select('Description')
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if(!error){
            // Las entidades contienen una matriz de entidades.
        }
    });

Eliminación de una entidad
--------------------------

Puede eliminar una entidad usando sus claves de partición y fila. En este ejemplo, el objeto **task1** contiene los valores **RowKey** y **PartitionKey** de la entidad que se va a eliminar. A continuación, el objeto pasa al método **deleteEntity**.

    tableService.deleteEntity('mytable'
        , {
            PartitionKey : 'hometasks'
            , RowKey : '1'
        }
        , function(error){
            if(!error){
                // La entidad se ha eliminado.
            }
        });

Eliminación de una tabla
------------------------

El código siguiente elimina una tabla de la cuenta de almacenamiento.

    tableService.deleteTable('mytable', function(error){
        if(!error){
            // La tabla se ha eliminado.
        }
    });

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) (en inglés).
-   Visite el repositorio del [SDK de Azure para Node.js](https://github.com/WindowsAzure/azure-sdk-for-node) (en inglés) en GitHub.

