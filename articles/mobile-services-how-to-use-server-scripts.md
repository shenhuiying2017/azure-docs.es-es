<properties pageTitle="Work with a JavaScript backend mobile service" metaKeywords="server scripts, mobile devices, Azure, scheduler" description="Provides examples on how to define, register, and use server scripts in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Uso de un servicio móvil back-end de JavaScript

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

Este artículo proporciona información detallada y ejemplos sobre cómo usar scripts del back-end de JavaScript en Servicios móviles de Azure. Este tema está dividido en las siguientes secciones:

-   [Introducción][]
-   [Operaciones de tabla][]
    -   [Direccionamiento del operaciones de tabla][]
    -   [Direccionamiento del la respuesta predeterminada][]
    -   [Direccionamiento del execute success][]
    -   [Direccionamiento del control de errores predeterminado][]
    -   [Direccionamiento del parámetros personalizados][]
    -   [Direccionamiento del usuarios de tablas][]
-   [API personalizada][]
    -   [Direccionamiento del una API personalizada][]
    -   [Direccionamiento del de métodos HTTP][]
    -   [Direccionamiento del y recepción de datos como XML][]
    -   [Direccionamiento del de usuarios y encabezados en una API personalizada][]
    -   [Direccionamiento del de varias rutas en una API personalizada][]
-   [Programador de trabajos][]
    -   [Direccionamiento del de scripts de trabajos programados][Programador de trabajos]
-   [Control de código fuente, código compartido y funciones auxiliares][]
    -   [Direccionamiento del de módulos Node.js][]
    -   [Direccionamiento del de funciones auxiliares][]
    -   [Direccionamiento del compartido de código mediante el control de código fuente][]
    -   [Direccionamiento del de configuración de aplicaciones][]
-   [Uso de la herramienta de la línea de comandos][]
-   [Uso de tablas][]
    -   [Direccionamiento del a tablas desde scripts][]
    -   [Direccionamiento del de inserciones en masa][]
    -   [Direccionamiento del de tipos JSON a tipos de base de datos][]
    -   [Uso de Transact-SQL para obtener acceso a las tablas][]
-   [Depuración y solución de problemas][]
    -   [Direccionamiento del del resultado en los registros de servicios móviles][]

## <a name="intro"></a>Introducción

En un servicio móvil de back-end de JavaScript, puede definir la lógica de negocios personalizada como código JavaScript, que se almacena y ejecuta en el servidor. Este código de script del servidor se asigna a una de las siguientes funciones de servidor:

-   [Inserción, lectura, actualización o eliminación de operaciones en una tabla determinada][Operaciones de tabla].
-   [Trabajos programados][Programador de trabajos].
-   [Métodos HTTP definidos en una API personalizada][API personalizada].

La firma de la función principal del script del servidor depende del contexto en el que se use el script. También puede definir el código de script común como módulos nodes.js que se comparten entre scripts. Para obtener más información, consulte [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

Para ver las descripciones de las funciones y los objetos de script del servidor individuales, consulte [Referencia de script de servidor de Servicios móviles][].

## <a name="table-scripts"></a>Operaciones de tabla

Un script de operación de tabla es un script del servidor que está registrado en una operación en una tabla: insertar, leer, actualizar o eliminar (*del*). El nombre del script debe coincidir con el tipo de operación para el que se ha registrado. Solo puede registrarse un script para una operación de tabla determinada. El script se ejecuta cada vez que una solicitud REST invoca la operación determinada, por ejemplo, cuando se recibe una solicitud POST para insertar un elemento en la tabla. Servicios móviles no conserva el estado entre ejecuciones de script. Puesto que se crea un nuevo contexto global cada vez que se ejecuta un script, las variables de estado que se definen en el script vuelven a inicializarse. Si desea almacenar el estado de una solicitud a otra, cree una tabla en el servicio móvil y, a continuación, lea y escriba el estado en la tabla. Para obtener más información, consulte [Inserción de tablas desde scripts][Direccionamiento del a tablas desde scripts].

Escriba scripts de operaciones de tabla si necesita forzar la lógica de negocios personalizada cuando se ejecute la operación. Por ejemplo, el siguiente script rechaza las operaciones de inserción si la longitud de la cadena del campo `text` es superior a 10 caracteres:

    function insert(item, user, request) {
        if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 
                'Text length must be less than 10 characters');
        } else {
            request.execute();
        }
    }

Una función de script de tabla siempre cuenta con tres argumentos.

-   El primer argumento varía según la operación de tabla.

    -   Para las inserciones y las actualizaciones, es un objeto **item**, que es una representación JSON de la fila afectada por la operación. Esto le permite obtener acceso a los valores de columna por nombre, por ejemplo, *item.Owner*, donde *Owner* es uno de los nombres en la representación JSON.
    -   Para la eliminación, es el identificador del registro que se va a eliminar.
    -   Para la lectura, es un objeto [query][] que especifica el conjunto de filas que se van a devolver.
-   El segundo argumento es siempre un objeto [user][] que representa al usuario que envió la solicitud.

-   El tercer argumento es siempre un objeto [request][] mediante el que puede controlar la ejecución de la operación solicitada y la respuesta enviada al cliente.

A continuación se muestran las firmas canónicas de funciones principales para las operaciones de tabla:

-   [Insert (función)][]: `function insert (item, user, request) { ... }`
-   [Update, función][]: `function update (item, user, request) { ... }`
-   [Delete (función)][]: `function del (id, user, request) { ... }`
-   [Read (función)][]: `function read (query, user, request) { ... }`

> [WACOM.NOTE]Debe usarse el nombre *del* para la función registrada en la operación de eliminación porque "delete" es una palabra clave reservada en JavaScript.

Cada script del servidor cuenta con una función principal y dispone de funciones auxiliares opcionales. A pesar de que es posible que se haya creado un script del servidor para una tabla específica, también puede hacer referencia a otras tablas en la misma base de datos. También puede definir funciones comunes como módulos que pueden compartirse entre scripts. Para obtener más información, consulte [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

### <a name="register-table-scripts"></a>Direccionamiento del scripts de tablas

Puede definir los scripts del servidor que están registrados en una operación de tabla de alguna de las siguientes formas:

-   En el [Portal de administración de Azure][]. Puede obtenerse acceso a los scripts para las operaciones de tabla en la pestaña **Scripts** para una tabla determinada. A continuación se muestra el código predeterminado registrado en el script de inserción para la tabla `TodoItem`. Puede reemplazar este código por su propia lógica de negocios personalizada.

    ![1][]

    Para obtener información al respecto, consulte [Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor][].

-   Mediante el control de código fuente. Cuando tenga el control de código fuente habilitado, simplemente cree un archivo con el nombre *`<table>``<operation>`*.js en la subcarpeta .\\service\\table del repositorio git, donde *`<table>`* es el nombre de la tabla y *`<operation>`* es la operación de tabla que se registra. Para obtener más información, consulte [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

-   Desde el símbolo del sistema mediante la herramienta de la línea de comandos de Azure. Para obtener más información, consulte [Uso de la herramienta de la línea de comandos][].

Un script de operación de tabla debe llamar a al menos a una de las siguientes funciones del objeto [request][] para asegurarse de que el cliente recibe una respuesta.

-   Función **execute**: La operación se ha completado como se ha solicitado y se devuelve la respuesta estándar.

-   Función **respond**: Se ha devuelto una respuesta personalizada.

<div class="dev-callout"><strong>Importante</strong>
<p>Es posible que la operaci&oacute;n no responda cuando un script cuente con una ruta de c&oacute;digo en la que no se invoque <b>execute</b> ni <b>respond</b>.</p></div>

El siguiente script llama a la función **execute** para completar la operación de datos solicitada por el cliente:

    function insert(item, user, request) { 
        request.execute(); 
    }

En este ejemplo, el elemento se inserta en la base de datos y se devuelve el código de estado apropiado al usuario.

Cuando se llama a la función **execute**, se utiliza el valor `item`, [query][] o `id` que se pasó como el primer argumento en la función de script para realizar la operación. Para una operación de inserción, actualización o consulta, puede modificar el objeto o la consulta antes de llamar a **execute**:

    function insert(item, user, request) { 
        item.scriptComment =
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 

    function update(item, user, request) { 
        item.scriptComment = 
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 

    function read(query, user, request) { 
        // Only return records for the current user         
        query.where({ userid: user.userId}); 
        request.execute(); 
    }

> [WACOM.NOTE]En un script de eliminación, cambiar el valor de las variables userId proporcionadas no afecta al registro que se elimina.

Para ver más ejemplos, consulte [Leer y escribir datos][], [Modificar la solicitud][] y [Validar datos][].

### <a name="override-response"></a>Direccionamiento del la respuesta predeterminada

También puede usar un script para implementar la lógica de validación que puede reemplazar el comportamiento de la respuesta predeterminada. Si se produce un error en la validación, llame a la función **respond** en lugar de a la función **execute** y escriba la respuesta en el cliente:

    function insert(item, user, request) {
        if (item.userId !== user.userId) {
            request.respond(statusCodes.FORBIDDEN, 
            'You may only insert records with your userId.');
        } else {
            request.execute();
        }
    }

En este ejemplo, se rechaza la solicitud cuando el elemento insertado no dispone de una propiedad `userId` que coincida con el `userId` del objeto [user][] proporcionado para el cliente autenticado. En este caso, no se produce una operación de base de datos (*insert*) y se devuelve una respuesta con el código de estado 403 HTTP y un mensaje de error personalizado al cliente. Para ver más ejemplos, consulte [Modificar la respuesta][].

### <a name="override-success"></a>Direccionamiento del execute success

De forma predeterminada en una operación de tabla, la función **execute** escribe respuestas automáticamente. Sin embargo, puede pasar dos parámetros opcionales para la función execute que reemplacen el comportamiento en success o en error.

Si pasa un controlador **success** cuando llama a execute, puede modificar los resultados de una consulta antes de escribirlos en la respuesta. En el ejemplo siguiente, se llama a `execute({ success: function(results) { ... })` para realizar un trabajo adicional después de que se lean los datos de la base de datos pero antes de que se escriba la respuesta:

    function read(query, user, request) {
        request.execute({
            success: function(results) {
                results.forEach(function(r) {
                    r.scriptComment = 
                    'this was added by a script after querying the database';
                });
                request.respond();
            }
        });
    }

Cuando proporcione un controlador **success** para la función **execute**, debe también llamar a la función **respond** como parte del controlador **success** para que el tiempo de ejecución sepa que el script se ha completado y que puede escribirse una respuesta. Cuando llame a **respond** sin pasar ningún argumento, Servicios móviles genera la respuesta predeterminada.

> [WACOM.NOTE]Puede llamar a **respond** sin argumentos para invocar la respuesta predeterminada solo después de haber realizado la llamada a la función **execute**.

### <a name="override-error"></a>Direccionamiento del control de errores predeterminado

Puede generarse un error en la función **execute** si se produce la pérdida de conectividad en la base de datos, hay un objeto no válido o existe una consulta incorrecta. Los scripts del servidor registran el error y escriben un resultado de error en la respuesta de forma predeterminada cuando se produce el error. Puesto que Servicios móviles ofrece un control de errores predeterminado, no tiene que administrar los errores que puedan producirse en el servicio.

Puede reemplazar el control de error predeterminado mediante la implementación de control explícito de errores si desea una acción de compensación determinada o cuando desee usar el objeto console global para escribir más información detallada en el registro. Puede realizar esto proporcionando un controlador **error** en la función **execute**:

    function update(item, user, request) { 
      request.execute({ 
        error: function(err) { 
          // Do some custom logging, then call respond. 
          request.respond(); 
        } 
      }); 
    }

Cuando proporcione un controlador error, Servicios móviles devolverá un resultado de error al cliente cuando se llama a **respond**.

También puede proporcionar un controlador **success** y **error** si lo desea.

### <a name="access-headers"></a>Direccionamiento del parámetros personalizados

Cuando envía una solicitud al servicio móvil, puede incluir parámetros personalizados en el URI de la solicitud para que los scripts de operaciones de tabla sepan cómo procesar una solicitud determinada. A continuación, modifique su script para inspeccionar el parámetro para determinar la ruta de procesamiento.

Por ejemplo, el siguiente URI para una solicitud POST indica al servicio que no se permita la inserción de un nuevo *TodoItem* que tenga el mismo valor de texto:

        https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

Puede obtenerse acceso a los parámetros de consulta predeterminados desde la propiedad **parameters** del objeto [request][]. Servicios móviles proporciona el objeto **request** para cualquier función registrada en una operación de tabla. El siguiente script del servidor para la operación de inserción comprueba el valor del parámetro `duplicateText` antes de que se ejecute la operación de inserción:

        function insert(item, user, request) {
            var todoItemTable = tables.getTable('TodoItem');
            // Check the supplied custom parameter to see if
            // we should allow duplicate text items to be inserted.        
            if (request.parameters.duplicateText === 'false') {
                // Find all existing items with the same text
                // and that are not marked 'complete'. 
                todoItemTable.where({
                    text: item.text,
                    complete: false
                }).read({
                    success: insertItemIfNotComplete
                });
            } else {
                request.execute();
            }

            function insertItemIfNotComplete(existingItems) {
                if (existingItems.length > 0) {
                    request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
                } else {
                    // Insert the item as normal. 
                    request.execute();
                }
            }
        }

Tenga en cuenta que en **insertItemIfNotComplete**, la función **execute** del objeto [request][] se invoca para insertar el elemento cuando no existe texto duplicado; de lo contrario, se invoca la función **respond** para avisar al cliente del duplicado.

Tenga en cuenta la sintaxis de la llamada a la función **success** en el código anterior:

                }).read({
                    success: insertItemIfNotComplete
                });

En JavaScript es una versión compacta del equivalente más largo:

        success: function(results) 
        { 
            insertItemIfNotComplete(results); 
        }

### <a name="work-with-users"></a>Direccionamiento del de usuarios

En Servicios móviles de Azure puede usar un proveedor de identidades para autenticar usuarios. Para obtener más información, consulte [Introducción a la autenticación][]. Cuando un usuario autenticado invoca una operación de tabla, Servicios móviles usa el objeto [user][] para proporcionar información sobre el usuario a la función de script registrada. La propiedad **userId** puede usarse para almacenar y recuperar información específica del usuario. En el siguiente ejemplo se establece la propiedad owner de un elemento basado en el userId de un usuario autenticado:

    function insert(item, user, request) {
        item.owner = user.userId;
        request.execute();
    }

En el siguiente ejemplo se agrega un filtro adicional a la consulta basado en el **userId** de un usuario autenticado. Este filtro restringe el resultado a solo los elementos que pertenecen al usuario actual:

    function read(query, user, request) {
        query.where({
            owner: user.userId
        });
        request.execute();
    }

## <a name="custom-api"></a>API personalizada

Una API personalizada es un extremo del servicio móvil al que se obtiene acceso mediante uno o varios métodos HTTP estándar: GET, POST, PUT, PATCH y DELETE. Puede definirse una exportación de función independiente para cada método HTTP proporcionado por la API personalizada, todo en un único archivo de script. El script registrado se invoca cuando se recibe una solicitud a la API personalizada con el método determinado. Para obtener más información, consulte [API personalizada][2].

Cuando el tiempo de ejecución de Servicios móviles llama a las funciones de la API personalizada, se proporcionan tanto el objeto [request][] como [response][]. Estos objetos ofrecen la funcionalidad de [express.js library][], que los scripts pueden aprovechar. La siguiente API personalizada llamada **hello** es un ejemplo muy sencillo que devuelve *Hello, world!* en respuesta a la solicitud POST:

        exports.post = function(request, response) {
            response.send(200, "{ message: 'Hello, world!' }");
        } 

La función **send** en el objeto [request][response] devuelve la respuesta deseada al cliente. Este código se invoca mediante el envío de una solicitud POST a la siguiente dirección URL:

        https://todolist.azure-mobile.net/api/hello  

El estado global se mantiene entre ejecuciones.

### <a name="define-custom-api"></a>Direccionamiento del una API personalizada

Puede definir los scripts del servidor que están registrados en métodos HTTP en un extremo de la API personalizada de alguna de las siguientes formas:

-   En el [Portal de administración de Azure][]. Los scripts de la API personalizada se crean y modifican en la pestaña **API**. El código del script del servidor se encuentra en la pestaña **Scripts** de una determinada API personalizada. A continuación se muestra el script que invoca la solicitud POST en el extremo de la API personalizada `CompleteAll`.

    ![2][3]

    Los métodos de permisos de acceso a la API personalizada se encuentran asignados en la pestaña Permissions. Para ver cómo se creó esta API personalizada, consulte [Llamada a una API personalizada desde el cliente][].

-   Mediante el control de código fuente. Cuando disponga de un control de código fuente habilitado, simplemente cree un archivo con el nombre *`<custom_api>`*.js en la subcarpeta .\\service\\api en el repositorio git, donde *`<custom_api>`* es el nombre de la API personalizada que se registra. El script contienen una función *exported* para cada método HTTP que ofrece la API personalizada. Los permisos se definen en un archivo .json complementario. Para obtener más información, consulte [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

-   Desde el símbolo del sistema mediante la herramienta de la línea de comandos de Azure. Para obtener más información, consulte [Uso de la herramienta de la línea de comandos][].

### <a name="handle-methods"></a>Direccionamiento del de métodos HTTP

Una API personalizada puede administrar uno o más métodos HTTP: GET, POST, PUT, PATCH y DELETE. Se define una función exportada para cada método HTTP que administra la API personalizada. Un único archivo de código de la API personalizada puede exportar una o todas las funciones siguientes:

        exports.get = function(request, response) { ... };
        exports.post = function(request, response) { ... };
        exports.patch = function(request, response) { ... };
        exports.put = function(request, response) { ... };
        exports.delete = function(request, response) { ... };

No se puede llamar al extremo de la API personalizada mediante un método HTTP que no se haya implementado en el script del servidor y se devuelve una respuesta de error 405 (Método no permitido). Pueden asignarse niveles de permisos independientes a cada método de HTTP compatible.

### <a name="api-return-xml"></a>Direccionamiento del y recepción de datos como XML

Cuando los clientes almacenan y recuperan datos, Servicios móviles usa la notación de objetos JavaScript (JSON) para representar datos en el cuerpo del mensaje. Sin embargo, existen escenarios en los que es posible que desee usar una carga XML. Por ejemplo, las aplicaciones de Tienda Windows disponen de una funcionalidad de notificaciones periódicas integrada que requiere que el servicio emita XML. Para obtener más información, consulte [Definición de una API personalizada que admita notificaciones periódicas][].

La siguiente función de la API personalizada **OrderPizza** devuelve un documento XML simple como carga de respuesta:

        exports.get = function(request, response) {
          response.set('content-type', 'application/xml');
          var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
          response.send(200, xml);
        };

Una solicitud HTTP GET invoca esta función de la API personalizada para el siguiente extremo:

        https://todolist.azure-mobile.net/api/orderpizza

### <a name="get-api-user"></a>Direccionamiento del de usuarios y encabezados en una API personalizada

En Servicios móviles de Azure puede usar un proveedor de identidades para autenticar usuarios. Para obtener más información, consulte [Introducción a la autenticación][]. Cuando un usuario autenticado solicita una API personalizada, Servicios móviles usa el objeto [user][] para proporcionar información sobre el usuario al código de la API personalizada. Se obtiene acceso al objeto [user][] desde la propiedad del usuario del objeto [request][]. La propiedad **userId** puede usarse para almacenar y recuperar información específica del usuario.

En la siguiente función de la API personalizada **OrderPizza** se establece la propiedad owner de un elemento basado en el userId de un usuario autenticado:

        exports.post = function(request, response) {
            var userTable = request.service.tables.getTable('user');
            userTable.lookup(request.user.userId, {
                success: function(userRecord) {
                    callPizzaAPI(userRecord, request.body, function(orderResult) {
                        response.send(201, orderResult);
                    });
                }
            });
        
        };

Una solicitud HTTP POST invoca esta función de la API personalizada para el siguiente extremo:

        https://<service>.azure-mobile.net/api/orderpizza

También puede obtener acceso a un encabezado HTTP desde el objeto [request][] como se muestra en el siguiente código:

        exports.get = function(request, response) {    
            var header = request.header('my-custom-header');
            response.send(200, "You sent: " + header);
        };

En este ejemplo simple se lee un encabezado personalizado con el nombre `my-custom-header` y, a continuación, se devuelve el valor en la respuesta.

### <a name="api-routes"></a>Direccionamiento del varias rutas en una API personalizada

Servicios móviles le permite definir varias rutas en una API personalizada. Por ejemplo, las solicitudes HTTP GET de las siguientes direcciones URL en una API personalizada **calculator** invocarán una función **add** o **subtract** respectivamente:

-   `https://<service>.azure-mobile.net/api/calculator/add`
-   `https://<service>.azure-mobile.net/api/calculator/sub`

Las distintas rutas se definen mediante la exportación de una función **register**, en la que se pasa un objeto **api** (parecido al objeto [express][] en express.js) que se usa para registrar rutas en el extremo de la API personalizada. En el siguiente ejemplo se implementan los métodos **add** y **sub** en la API personalizada **calculator**:

        exports.register = function (api) {
            api.get('add', add);
            api.get('sub', subtract);
        }
        
        function add(req, res) {
            var result = parseInt(req.query.a) + parseInt(req.query.b);
            res.send(200, { result: result });
        }
        
        function subtract(req, res) {
            var result = parseInt(req.query.a) - parseInt(req.query.b);
            res.send(200, { result: result });
        }

El objeto **api** que pasó a la función **register** ofrece una función para cada método HTTP (**get**, **post**, **put**, **patch** y **delete**). Estas funciones registran una ruta a una función definida para un método HTTP específico. Cada función usa dos parámetros, el primero es el nombre de ruta y el segundo es la función registrada en la ruta.

Las solicitudes HTTP GET pueden invocar las dos rutas del ejemplo anterior de la API personalizada de la siguiente forma (se muestra con la respuesta):

-   `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

        {"result":3}

-   `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

        {"result":-2}

## <a name="scheduler-scripts"></a>Programador de trabajos

Servicios móviles le permite definir scripts del servidor que se ejecuten como trabajos en una programación fija o a petición en el Portal de administración. Los trabajos programados son útiles para la realización de tareas periódicas como la limpieza de datos de una tabla y el procesamiento por lotes. Para obtener más información, consulte [Programar trabajos][].

Los scripts que se han registrado en trabajos programados tienen una función principal con el mismo nombre que el trabajo programado. Puesto que una solicitud HTTP no invoca un script programado, no existe ningún contexto que el tiempo de ejecución del servidor pueda pasar y la función no usa ningún parámetro. Como con otros tipos de scripts, puede disponer de funciones de subrutinas y solicitar módulos compartidos. Para obtener más información, consulte [Control de código fuente, código compartido y funciones auxiliares][].

### <a name="scheduler-scripts"></a>Direccionamiento del scripts de trabajos programados

Puede asignarse un script del servidor a una trabajo definido en el Programador de Servicios móviles. Estos scripts pertenecen al trabajo y se ejecutan de acuerdo con la programación de trabajos. (También puede usar el [Portal de administración][Portal de administración de Azure] para ejecutar trabajos bajo demanda). Un script que define un trabajo programado no cuenta con parámetros porque Servicios móviles no pasa ningún dato; se ejecuta como una función JavaScript normal y no interactúa con Servicios móviles directamente.

Puede definir trabajos programados de una de las siguientes formas:

-   En el [Portal de administración de Azure][] en la pestaña **Script** del programador:

    ![3][4]

    Para obtener más información sobre cómo hacer esto, consulte [Programación de trabajos de back-end en Servicios móviles][].

-   Desde el símbolo del sistema mediante la herramienta de la línea de comandos de Azure. Para obtener más información, consulte [Uso de la herramienta de la línea de comandos][].

> [WACOM.NOTE]Cuando cuente con un control de código fuente habilitado, podrá editar archivos de script de trabajos programados directamente en la subcarpeta .\\service\\scheduler del repositorio git. Para obtener más información, consulte [Inserción de compartido de código mediante el control de código fuente][Direccionamiento del compartido de código mediante el control de código fuente].

## <a name="shared-code"></a>Control de código fuente, código compartido y funciones auxiliares

Puesto que Servicios móviles usa Node.js en el servidor, los scripts ya disponen de acceso a los módulos Node.js integrados. También puede usar el control de código fuente para determinar sus propios módulos o agregar otros módulos Node.js a su servicio.

A continuación se muestran algunos de los módulos más útiles de los que puede sacar provecho en los scripts mediante la función **require** global:

-   **azure**: Ofrece la funcionalidad del SDK de Azure para Node.js. Para obtener más información, consulte [Azure SDK for Node.js][].
-   **crypto**: Proporciona la funcionalidad crypto de OpenSSL. Para obtener más información, consulte [Node.js documentation][].
-   **path**: Contiene utilidades para trabajar con rutas de archivos. Para obtener más información, consulte [Node.js documentation][5].
-   **querystring**: Contiene utilidades para trabajar con cadenas de consulta. Para obtener más información, consulte [Node.js documentation][6].
-   **request**: Envía solicitudes HTTP a servicios REST externos, como Twitter y Facebook. Para obtener más información, consulte [Enviar solicitud HTTP][].
-   **sendgrid**: Envía un correo electrónico mediante el servicio de correo electrónico Sendgrid en Azure. Para obtener más información, consulte [Envío de correo electrónico desde Servicios móviles con SendGrid][].
-   **url**: Contiene utilidades para redistribuir y resolver direcciones URL. Para obtener más información, consulte [Node.js documentation][7].
-   **util**: Contiene varias utilidades, como una comprobación del tipo de objeto y un formato de cadena. Para obtener más información, consulte [Node.js documentation][8].
-   **zlib**: Ofrece la funcionalidad de compresión, como gzip y deflate. Para obtener más información, consulte [Node.js documentation][9].

### <a name="modules-helper-functions"></a>Direccionamiento del módulos

Servicios móviles proporciona una serie de módulos que los scripts pueden cargar mediante la función **require** global. Por ejemplo, un script puede requerir **request** para realizar solicitudes HTTP:

    function update(item, user, request) { 
        var httpRequest = require('request'); 
        httpRequest('http://www.google.com', function(err, response, body) { 
            ... 
        }); 
    } 

### <a name="shared-code-source-control"></a>Direccionamiento del de código mediante el control de código fuente

Puede usar el control de código fuente con el administrador de paquetes Node.js (npm) para controlar qué módulos se encuentran disponibles para el servicio móvil. Existen dos formas de hacerlo:

-   Para los módulos publicados en npm o que npm ha instalado, use el archivo package.json para establecer qué paquete desea que el servicio móvil instale. De esta forma, su servicio siempre dispondrá de acceso a la versión más reciente de los paquetes requeridos. El archivo package.json se encuentra en el directorio `.\service`. Para obtener más información, consulte [Support for package.json in Azure Mobile Services][].

-   En los módulos privados o personalizados, puede usar npm para instalar manualmente el módulo en el directorio `.\service\node_modules` del control de código fuente. Para obtener un ejemplo de cómo cargar manualmente un módulo, consulte [Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor][].

    > [WACOM.NOTE] Si `node_modules` ya existe en la jerarquía de directorios, NPM creará el subdirectorio `\node-uuid` ahí en lugar de crear un nuevo `node_modules` en el repositorio. En este caso, elimine el directorio `node_modules` existente.

Una vez que confirme el archivo package.json o los módulos personalizados en el repositorio para su servicio móvil, use **require** para hacer referencia a los módulos por nombre.

> [WACOM.NOTE] Los módulos que especifica en package.json o carga en el servicio móvil solo se usan en el código del script del servidor. El tiempo de ejecución de Servicios móviles no usa estos módulos.

### <a name="helper-functions"></a>Direccionamiento del funciones auxiliares

Además de requerir módulos, los scripts del servidor individuales pueden incluir funciones auxiliares. Existen funciones que son independientes de la función principal, que pueden usarse para incluir código en el script.

En el siguiente ejemplo, se registra un script de tabla en la operación de inserción, que incluye la función auxiliar **handleUnapprovedItem**:

    function insert(item, user, request) {
        if (!item.approved) {
            handleUnapprovedItem(item, user, request);
        } else {
            request.execute();
        }
    }

    function handleUnapprovedItem(item, user, request) {
        // Do something with the supplied item, user, or request objects.
    }

En un script, las funciones auxiliares deben declararse después de la función principal. Debe declarar todas las variables en su script. Las variables no declaradas provocan un error.

Las funciones auxiliares también pueden definirse una vez y compartirse entre scripts del servidor. Para compartir una función entre scripts, deben exportarse las funciones y el archivo de script debe existir en el directorio `.\service\shared\`. En el siguiente ejemplo se muestra una plantilla sobre cómo exportar una función compartida en un archivo `.\services\shared\helpers.js`:

        exports.handleUnapprovedItem = function (tables, user, callback) {
            
            // Do something with the supplied tables or user objects and 
            // return a value to the callback function.
        };

A continuación, puede usar una función como esta en un script de operaciones de tabla:

        function insert(item, user, request) {
            var helper = require('../shared/helper');
            helper.handleUnapprovedItem(tables, user, function(result) {
                    
                    // Do something based on the result.
                    request.execute();
                }
            }
        }

En este ejemplo debe pasar un objeto [tables][] y un objeto [user][] a la función compartida. Esto se debe a que los scripts compartidos no pueden obtener acceso al objeto [tables][] global y a que el objeto [user][] solo existe en el contexto de una solicitud.

Los archivos de script se cargan en el directorio compartido mediante el [control de código fuente][Direccionamiento del compartido de código mediante el control de código fuente] o con la [herramienta de la línea de comandos][Uso de la herramienta de la línea de comandos].

### <a name="app-settings"></a>Direccionamiento del configuración de aplicaciones

Servicios móviles le permite almacenar de forma segura valores como configuraciones de aplicaciones, a los que los scripts del servidor pueden obtener acceso en el tiempo de ejecución. Cuando agrega datos a la configuración de aplicaciones del servicio móvil, los pares nombre/valor se almacenan cifrados y puede obtener acceso a ellos en los scripts del servidor sin integrarlos como parte del código en el archivo de script. Para obtener más información, consulte [Configuración de aplicación][].

El siguiente ejemplo de API personalizada usa el objeto [service][] proporcionado para recuperar un valor de configuración de aplicaciones.

        exports.get = function(request, response) {
        
            // Get the MY_CUSTOM_SETTING value from app settings.
            var customSetting = 
                request.service.config.appSettings.my_custom_setting;
                
            // Do something and then send a response.

        }

El siguiente código usa el módulo de configuración para recuperar los valores de token de acceso a Twitter (almacenados en la configuración de aplicaciones) que se usan en un script de trabajo programado:

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

Tenga en cuenta que este código también recupera los valores clave del usuario de Twitter almacenados en la pestaña **Identity** del portal. Puesto que no hay disponible un objeto **config** en la operación de tabla y en los scripts de trabajos programados, debe solicitar al módulo de configuración que obtenga acceso a la configuración de aplicaciones. Para ver un ejemplo completo, consulte [Programación de trabajos de back-end en Servicios móviles][].

## <a name="command-prompt"></a>Uso de la herramienta de la línea de comandos

En Servicios móviles, puede crear, modificar y eliminar scripts del servidor mediante la herramienta de la línea de comandos de Azure. Antes de cargar los scripts, asegúrese de que usa la siguiente estructura de directorios:

![4][10]

Tenga en cuenta que esta estructura de directorios es la misma que la del repositorio git cuando se usa el control de código fuente.

Cuando se cargan los archivos de script desde la herramienta de la línea de comandos, primero debe dirigirse al directorio `.\services\`. El siguiente comando carga un script con el nombre `todoitem.insert.js` desde el subdirectorio `table`:

        ~$azure mobile script upload todolist table/todoitem.insert.js
        info:    Executing command mobile script upload
        info:    mobile script upload command OK

El siguiente comando devuelve información sobre cada archivo de script que se mantiene en el servicio móvil:

        ~$ azure mobile script list todolist
        info:    Executing command mobile script list
        + Retrieving script information
        info:    Table scripts
        data:    Name                       Size
        data:    -------------------------  ----
        data:    table/channels.insert      1980
        data:    table/TodoItem.insert      5504
        data:    table/TodoItem.read        64
        info:    Shared scripts
        data:    Name              Size
        data:    ----------------  ----
        data:    shared/helper.js  62
        data:    shared/uuid.js    7452
        info:    Scheduled job scripts
        data:    Job name    Script name           Status    Interval     Last run  Next run
        data:    ----------  --------------------  --------  -----------  --------  --------
        data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
        info:    Custom API scripts
        data:    Name                    Get          Put          Post         Patch        Delete
        data:    ----------------------  -----------  -----------  -----------  -----------  -----------
        data:    completeall             application  application  application  application  application
        data:    register_notifications  application  application  user         application  application
        info:    mobile script list command OK

Para obtener más información, consulte [Comandos para administrar Servicios móviles de Azure][].

## <a name="working-with-tables"></a>Uso de tablas

Muchos escenarios de Servicios móviles requieren que los scripts del servidor obtengan acceso a las tablas en la base de datos. Por ejemplo, puesto que Servicios móviles no conserva el estado entre ejecuciones de scripts, los datos que tengan que mantenerse entre las ejecuciones de scripts deben almacenarse en tablas. Es posible que desee examinar entradas en una tabla de permisos o almacenar datos de auditoría en lugar de escribir simplemente el registro, donde los datos cuentan con una duración limitada y a los que no puede obtenerse acceso mediante programación.

Servicios móviles cuenta con dos formas de obtener acceso a las tablas, usando un proxy de objeto [table][] o componiendo consultas Transact-SQL con el objeto [mssql][]. El objeto [table][] facilita el acceso a los datos de tabla desde el código del script del servidor, pero el objeto [mssql][] es compatible con operaciones de datos más complejas y proporciona la mayor flexibilidad.

### <a name="access-tables"></a>Direccionamiento del tablas desde scripts

La forma más sencilla de obtener acceso a las tablas desde el script es usar el objeto [tables][]. La función **getTable** devuelve una instancia del objeto [table][] que es un proxy para obtener acceso a la tabla solicitada. A continuación, puede llamar a las funciones en el proxy para obtener acceso y cambiar los datos.

Los scripts registrados en las operaciones de tabla y los trabajos programados pueden obtener acceso al objeto [tables][] como un objeto global. Esta línea de código obtiene un proxy para la tabla *TodoItems* desde el objeto [tables][] global:

        var todoItemsTable = tables.getTable('TodoItems');

Los scripts de la API personalizada pueden obtener acceso al objeto [tables][] desde la propiedad **service** del objeto [request][] proporcionado. Esta línea de código obtiene el objeto [tables][] de la solicitud:

        var todoItemsTable = request.service.tables.getTable('TodoItem');

<div class="dev-callout"><strong>Nota:</strong>
<p>Las funciones compartidas no pueden obtener acceso al objeto <strong>tables</strong> directamente. En una funci&oacute;n compartida, debe pasar el objeto tables a la funci&oacute;n.</p></div>

Una vez que disponga de un objeto [table][], puede llamar a una o más funciones de operación de tablas: insertar, actualizar, eliminar o leer. En este ejemplo se leen los permisos de usuario desde una tabla de permisos:

    function insert(item, user, request) {
        var permissionsTable = tables.getTable('permissions');

        permissionsTable
            .where({ userId: user.userId, permission: 'submit order'})
            .read({ success: checkPermissions });
            
        function checkPermissions(results) {
            if(results.length > 0) {
                // Permission record was found. Continue normal execution.
                request.execute();
            } else {
                console.log('User %s attempted to submit an order without permissions.', user.userId);
                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
            }
        }
    }

En el siguiente ejemplo se escribe información de auditoría en una **tabla de auditoría**:

    function update(item, user, request) {
        request.execute({ success: insertAuditEntry });
        
        function insertAuditEntry() {
            var auditTable = tables.getTable('audit');
            var audit = {
                record: 'checkins',
                recordId: item.id,
                timestamp: new Date(),
                values: JSON.stringify(item)
            };
            auditTable.insert(audit, {
                success: function() {
                    // Write to the response now that all data operations are complete
                    request.respond();
                }
            });
        }
    }

Puede encontrar un ejemplo final en la muestra de código aquí: [Direccionamiento del parámetros personalizados][].

### <a name="bulk-inserts"></a>Direccionamiento del de inserciones en masa

Si usa un bucle **for** o **while** para insertar directamente una gran cantidad de elementos en una tabla (1000, por ejemplo), es posible que se encuentre con una limitación de la conexión SQL que provoque que se produzcan errores en las inserciones. Es posible que su solicitud no se complete nunca o que devuelva el error interno del servidor HTTP 500. Para evitar este problema, puede insertar los elementos en lotes de 10. Una vez que se haya insertado el primer lote, envíe el siguiente, y así sucesivamente.

Puede establecer el tamaño de un lote de registros que se va a insertar en paralelo con el siguiente script. Le recomendamos que use un número reducido de registros. La función **insertItems** realiza llamadas recursivas a sí misma cuando se ha completado el lote de inserción asincrónico. El bucle for al final hace que se inserte un registro cada vez y llama a **insertComplete** en success y **errorHandler** en error. **insertComplete** controla si se llamará a **insertItems** de forma recursiva para el siguiente lote o si el trabajo se ha realizado y el script debe salir.

        var todoTable = tables.getTable('TodoItem');
        var recordsToInsert = 1000;
        var batchSize = 10; 
        var totalCount = 0;
        var errorCount = 0; 
        
        function insertItems() {        
            var batchCompletedCount = 0;  
        
            var insertComplete = function() { 
                batchCompletedCount++; 
                totalCount++; 
                if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
                    if(totalCount < recordsToInsert) {
                        // kick off the next batch 
                        insertItems(); 
                    } else { 
                        // or we are done, report the status of the job 
                        // to the log and don't do any more processing 
                        console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
                    } 
                } 
            }; 
        
            var errorHandler = function(err) { 
                errorCount++; 
                console.warn("Ignoring insert failure as part of batch.", err); 
                insertComplete(); 
            };
        
            for(var i = 0; i < batchSize; i++) { 
                var item = { text: "This is item number: " + totalCount + i }; 
                todoTable.insert(item, { 
                    success: insertComplete, 
                    error: errorHandler 
                }); 
            } 
        } 
        
        insertItems(); 

Puede encontrar el ejemplo de código al completo y la discusión relacionada con él en esta [entrada de blog (en inglés)][]. Si usa este código, puede adaptarlo a su situación específica y probarlo minuciosamente.

### <a name="JSON-types"></a>Direccionamiento del de tipos JSON a tipos de base de datos

Las recopilaciones de tipos de datos en el cliente y en la tabla de base de datos de Servicios móviles son diferentes. A veces se asignan fácilmente entre ellas, y otras veces no lo hacen. Servicios móviles realiza una serie de transformaciones de tipo en la asignación:

-   Los tipos específicos del lenguaje del cliente se encuentran serializados en JSON.
-   La representación JSON se traduce a JavaScript antes de que aparezca en los scripts del servidor.
-   Los tipos de datos de JavaScript se convierten a tipos de base de datos SQL cuando se guardan con el objeto [tables][].

La transformación del esquema de cliente en JSON varía entre plataformas. JSON.NET se usa en los clientes de Windows Phone y Tienda Windows. El cliente Android usa la biblioteca gson. El cliente iOS usa la clase NSJSONSerialization. Se utiliza el comportamiento de serialización predeterminado de cada una de estas bibliotecas, excepto en el caso en el que los objetos date se conviertan en cadenas que contengan la fecha codificada mediante ISO 8601.

Cuando escriba scripts del servidor que usen funciones [insert][Insert (función)], [update][Update, función], [read][Read (función)] o [delete][Delete (función)], puede obtener acceso a la representación de JavaScript de sus datos. Servicios móviles usa la función de deserialización de Node.js ([JSON.parse][]) para transformar los elementos JSON del protocolo de cable en objetos JavaScript. Sin embargo, Servicios móviles realiza una transformación para extraer objetos **Date** de las cadenas ISO 8601.

Cuando use el objeto [tables][] o el objeto [mssql][], o simplemente deje que los scripts de tablas se ejecuten, los objetos JavaScript deserializados se insertan en la base de datos SQL. En ese proceso, las propiedades del objeto se asignan a tipos T-SQL:

|----------------------|-------------------|
| Propiedad JavaScript | Tipo T-SQL        |
| Number               | Float(53)         |
| Boolean              | Bit               |
| Date                 | DateTimeOffset(3) |
| String               | Nvarchar(max)     |
| Buffer               | No compatible     |
| Object               | No compatible     |
| Array                | No compatible     |
| Stream               | No compatible     |

### <a name="TSQL"></a>Uso de Transact-SQL para obtener acceso a las tablas

La forma más sencilla de trabajar con datos de tablas de scripts del servidor es mediante un proxy del objeto [table][]. Sin embargo, existen escenarios más avanzados que no son compatibles con el objeto [table][], como las consultas de unión y otras consultas complejas y la invocación de procedimientos almacenados. En estos casos, debe ejecutar las instrucciones Transact-SQL directamente en contra de la tabla relacional mediante el objeto [mssql][]. Este objeto proporciona las siguientes funciones:

-   **query**: Ejecuta una consulta, especificada por una cadena TSQL; los resultados se devuelven a la devolución de llamada **success** en el objeto **options**. La consulta puede incluir parámetros si el parámetro *params* está presente.
-   **queryRaw**: Como *query*, con la excepción de que el conjunto de resultados devuelto de la consulta está en formato sin procesar (consulte el siguiente ejemplo).
-   **open**: Se usa para conectarse a la base de datos de Servicios móviles y, a continuación, puede usar el objeto connection para invocar las operaciones de base de datos como las transacciones.

Estos métodos le proporcionan un control cada vez mayor de nivel bajo sobre el procesamiento de consultas.

-   [Direccionamiento del una consulta estática][]
-   [Direccionamiento del una consulta dinámica][]
-   [Direccionamiento del de tablas relacionales][]
-   [Direccionamiento del de una consulta que devuelve resultados *sin procesar*][]
-   [Direccionamiento del acceso a una conexión de base de datos][]

#### <a name="static-query"></a>Direccionamiento del una consulta estática

La siguiente consulta no dispone de parámetros y devuelve tres registros a partir de la tabla `statusupdate`. El conjunto de filas se encuentra en el formato JSON estándar.

        mssql.query('select top 3 * from statusupdates', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });

#### <a name="dynamic-query"></a>Direccionamiento del una consulta dinámica parametrizada

En el siguiente ejemplo se implementa la autorización personalizada mediante la lectura de permisos para cada usuario desde la tabla de permisos. El marcador de posición (?) se reemplaza por el parámetro proporcionado cuando se ejecuta la consulta.

            var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
            mssql.query(sql, [user.userId], {
                success: function(results) {
                    if (results.length > 0) {
                        // Permission record was found. Continue normal execution. 
                        request.execute();
                    } else {
                        console.log('User %s attempted to submit an order without permissions.', user.userId);
                        request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
                    }
                },
                error: function(err) {
                    console.log("error is: " + err);
                }   
            });

#### <a name="joins"></a>Direccionamiento del tablas relacionales

Puede unir dos tablas mediante el método **query** del objeto [mssql][] para pasar el código TSQL que implementa la unión. Supongamos que disponemos de algunos elementos en la tabla **ToDoItem** y que cada elemento de la tabla dispone de una propiedad **priority** que se corresponde con una columna de la tabla. El aspecto del elemento sería el siguiente:

        { text: 'Take out the trash', complete: false, priority: 1}

Supongamos también que contamos con una tabla adicional denominada **Priority** con filas que contienen un número de prioridad (**number**) y una descripción de texto (**description**). Por ejemplo, el número de prioridad 1 debe tener la descripción de crítico "Critical". El aspecto del objeto será el siguiente:

        { number: 1, description: 'Critical'}

Ahora podemos reemplazar el **número de prioridad** en nuestro elemento por la descripción de texto del número de prioridad. Hacemos esta operación con una unión relacional de las dos tablas.

        mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        });

El script une las dos tablas y escribe los resultados en el registro. Los objetos resultantes pueden tener la siguiente apariencia:

        { text: 'Take out the trash', complete: false, description: 'Critical'}

#### <a name="raw"></a>Direccionamiento del de una consulta que devuelve resultados *sin procesar*

En este ejemplo se ejecuta la consulta, como antes, pero esta devuelve el conjunto de resultados en formato sin procesar, lo cual requiere un análisis fila por fila y columna por columna. Un posible escenario para esto es, por ejemplo, que necesite obtener acceso a tipos de datos que no son compatibles con Servicios móviles. Este código simplemente escribe el resultado en el registro de la consola, por lo que puede inspeccionar el formato sin procesar.

        mssql.queryRaw('SELECT * FROM ToDoItem', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });

Este es el resultado de ejecutar esta consulta. Contiene metadatos sobre cada columna en la tabla. A continuación aparece una representación de las filas y columnas.

        { meta: 
           [ { name: 'id',
               size: 19,
               nullable: false,
               type: 'number',
               sqlType: 'bigint identity' },
             { name: 'text',
               size: 0,
               nullable: true,
               type: 'text',
               sqlType: 'nvarchar' },
             { name: 'complete',
               size: 1,
               nullable: true,
               type: 'boolean',
               sqlType: 'bit' },
             { name: 'priority',
               size: 53,
               nullable: true,
               type: 'number',
               sqlType: 'float' } ],
          rows: 
           [ [ 1, 'good idea for the future', null, 3 ],
             [ 2, 'this is important but not so much', null, 2 ],
             [ 3, 'fix this bug now', null, 0 ],
             [ 4, 'we need to fix this one real soon now', null, 1 ],
           ] }

#### <a name="connection"></a>Direccionamiento del acceso a una conexión de base de datos

Puede usar el método **open** para obtener acceso a la conexión de base de datos. Puede necesitarlo, por ejemplo, para usar transacciones de base de datos.

Una ejecución correcta de **open** provoca que la conexión de base de datos se pase a la función **success** como un parámetro. Puede invocar cualquiera de las siguientes funciones en el objeto **connection**: *close*, *queryRaw*, *query*, *beginTransaction*, *commit* y *rollback*.

            mssql.open({
                success: function(connection) {
                    connection.query(//query to execute);
                },
                error: function(err) {
                    console.log("error is: " + err);
                }
            });

## <a name="debugging"></a>Depuración y solución de problemas

La forma más sencilla de depurar y solucionar los problemas de los scripts del servidor es escribir en el registro de servicio. De forma predeterminada, Servicios móviles escribe errores que se producen durante la ejecución del script del servicio en los registros de servicio. Los scripts también pueden escribir en los registros. La escritura en los registros es un método efectivo para depurar los scripts y validar que se están comportando según lo esperado.

### <a name="write-to-logs"></a>Direccionamiento del del resultado en los registros de servicios móviles

Para escribir en los registros, use el objeto [console][] global. Use la función **log** o **info** para registrar las advertencias de nivel de información. Las funciones **warning** y **error** registran sus niveles respectivos, a los que se llama en los registros.

<div class="dev-callout"><strong>Nota:</strong>
<p>Para ver los registros para el servicio m&oacute;vil, inicie sesi&oacute;n en el <a href="https://manage.windowsazure.com/">Portal de administraci&oacute;n</a>, seleccione el servicio m&oacute;vil y, a continuaci&oacute;n, seleccione la pesta&ntilde;a <strong>Logs</strong>.</p></div>

También puede usar las funciones de registro del objeto [console][] para dar formato a sus mensajes mediante parámetros. En el siguiente ejemplo se proporciona un objeto JSON como parámetro para la cadena de mensaje:

    function insert(item, user, request) {
        console.log("Inserting item '%j' for user '%j'.", item, user);  
        request.execute();
    }

Tenga en cuenta que se usa la cadena `%j` como marcador de posición para un objeto JSON y que esos parámetros se proporcionan en orden secuencial.

Para evitar la sobrecarga del registro, debe quitar o deshabilitar las llamadas a console.log() que no sean necesarias para la producción.

<!-- Anchors. --> 
<!-- URLs. -->

  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-how-to-use-server-scripts/ "Back-end de JavaScript"
  [Introducción]: #intro
  [Operaciones de tabla]: #table-scripts
  [Direccionamiento del operaciones de tabla]: #register-table-scripts
  [Direccionamiento del la respuesta predeterminada]: #override-response
  [Direccionamiento del execute success]: #override-success
  [Direccionamiento del control de errores predeterminado]: #override-error
  [Direccionamiento del parámetros personalizados]: #access-headers
  [Direccionamiento del usuarios de tablas]: #work-with-users
  [API personalizada]: #custom-api
  [Direccionamiento del una API personalizada]: #define-custom-api
  [Direccionamiento del de métodos HTTP]: #handle-methods
  [Direccionamiento del y recepción de datos como XML]: #api-return-xml
  [Direccionamiento del de usuarios y encabezados en una API personalizada]: #get-api-user
  [Direccionamiento del de varias rutas en una API personalizada]: #api-routes
  [Programador de trabajos]: #scheduler-scripts
  [Control de código fuente, código compartido y funciones auxiliares]: #shared-code
  [Direccionamiento del de módulos Node.js]: #modules-helper-functions
  [Direccionamiento del de funciones auxiliares]: #helper-functions
  [Direccionamiento del compartido de código mediante el control de código fuente]: #shared-code-source-control
  [Direccionamiento del de configuración de aplicaciones]: #app-settings
  [Uso de la herramienta de la línea de comandos]: #command-prompt
  [Uso de tablas]: #working-with-tables
  [Direccionamiento del a tablas desde scripts]: #access-tables
  [Direccionamiento del de inserciones en masa]: #bulk-inserts
  [Direccionamiento del de tipos JSON a tipos de base de datos]: #JSON-types
  [Uso de Transact-SQL para obtener acceso a las tablas]: #TSQL
  [Depuración y solución de problemas]: #debugging
  [Direccionamiento del del resultado en los registros de servicios móviles]: #write-to-logs
  [Referencia de script de servidor de Servicios móviles]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554226.aspx
  [query]: http://msdn.microsoft.com/es-es/library/windowsazure/jj613353.aspx
  [user]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554220.aspx
  [request]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554218.aspx
  [Insert (función)]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554229.aspx
  [Update, función]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554214.aspx
  [Delete (función)]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554215.aspx
  [Read (función)]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554224.aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
  [Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
  [Leer y escribir datos]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631640.aspx
  [Modificar la solicitud]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631635.aspx
  [Validar datos]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631638.aspx
  [Modificar la respuesta]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631631.aspx
  [Introducción a la autenticación]: http://go.microsoft.com/fwlink/p/?LinkId=287177
  [2]: http://msdn.microsoft.com/es-es/library/windowsazure/dn280974.aspx
  [response]: http://msdn.microsoft.com/es-es/library/windowsazure/dn303373.aspx
  [express.js library]: http://go.microsoft.com/fwlink/p/?LinkId=309046
  [3]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
  [Llamada a una API personalizada desde el cliente]: /es-es/develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
  [Definición de una API personalizada que admita notificaciones periódicas]: /es-es/develop/mobile/tutorials/create-pull-notifications-dotnet/
  [express]: http://expressjs.com/api.html#express
  [Programar trabajos]: http://msdn.microsoft.com/es-es/library/windowsazure/jj860528.aspx
  [4]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
  [Programación de trabajos de back-end en Servicios móviles]: /es-es/develop/mobile/tutorials/schedule-backend-tasks/
  [Azure SDK for Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=275539
  [Node.js documentation]: http://go.microsoft.com/fwlink/p/?LinkId=288802
  [5]: http://go.microsoft.com/fwlink/p/?LinkId=288803
  [6]: http://go.microsoft.com/fwlink/p/?LinkId=288804
  [Enviar solicitud HTTP]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631641.aspx
  [Envío de correo electrónico desde Servicios móviles con SendGrid]: /es-es/develop/mobile/tutorials/send-email-with-sendgrid/
  [7]: http://go.microsoft.com/fwlink/p/?LinkId=288805
  [8]: http://go.microsoft.com/fwlink/p/?LinkId=288806
  [9]: http://go.microsoft.com/fwlink/p/?LinkId=288807
  [Support for package.json in Azure Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=391036
  [Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor]: /es-es/develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
  [tables]: http://msdn.microsoft.com/es-es/library/windowsazure/jj614364.aspx
  [Configuración de aplicación]: http://msdn.microsoft.com/es-es/library/dn529070.aspx
  [service]: http://msdn.microsoft.com/es-es/library/windowsazure/dn303371.aspx
  [10]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png
  [Comandos para administrar Servicios móviles de Azure]: /es-es/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services/#Mobile_Scripts
  [table]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554210.aspx
  [mssql]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554212.aspx
  [entrada de blog (en inglés)]: http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx
  [JSON.parse]: http://es5.github.io/#x15.12
  [Direccionamiento del una consulta estática]: #static-query
  [Direccionamiento del una consulta dinámica]: #dynamic-query
  [Direccionamiento del de tablas relacionales]: #joins
  [Direccionamiento del de una consulta que devuelve resultados *sin procesar*]: #raw
  [Direccionamiento del acceso a una conexión de base de datos]: #connection
  [console]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554209.aspx
