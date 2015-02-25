<properties pageTitle="Uso de un servicio móvil back-end de JavaScript" description="Proporciona ejemplos acerca de cómo definir, registrar y usar scripts de servidor en Servicios móviles de Azure." services="mobile-services" documentationCenter="" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="ricksal"/>


# Uso de un servicio móvil back-end de JavaScript

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend" class="current">Back-end de JavaScript</a></div>
 
Este artículo proporciona información detallada y ejemplos sobre cómo usar scripts del back-end de JavaScript en Servicios móviles de Azure. Este tema está dividido en las siguientes secciones:

+ [Introducción]
+ [Operaciones de tabla]
	+ [Uso de operaciones de tabla]
	+ [Uso de la respuesta predeterminada]
	+ [Uso de execute success]
	+ [Uso de control de errores predeterminado]
	+ [Uso de parámetros personalizados]
	+ [Uso de Trabajo con los usuarios de la tabla][Uso de Trabajo con usuarios]
+ [API personalizada][Delimitador de API personalizada]
	+ [Uso de una API personalizada]
	+ [Uso de de métodos HTTP]
	+ [Uso de y recepción de datos como XML]
	+ [Uso de de usuarios y encabezados en una API personalizada]
	+ [Uso de de varias rutas en una API personalizada]
+ [Programador de trabajos]
	+ [Uso de de scripts de trabajos programados]
+ [Control de código fuente, código compartido y funciones auxiliares]
	+ [Uso de de módulos Node.js]
	+ [Uso de de funciones auxiliares]
	+ [Uso de compartido de código mediante el control de código fuente]
	+ [Uso de de configuración de aplicaciones] 
+ [Uso de la herramienta de línea de comandos]
+ [Trabajo con tablas]
	+ [Uso de a tablas desde scripts]
	+ [Uso de de inserciones en masa]
	+ [Uso de de tipos JSON a tipos de base de datos]
	+ [Uso de Transact-SQL para obtener acceso a las tablas]
+ [Depuración y solución de problemas]
	+ [Uso de del resultado en los registros de servicios móviles]

##<a name="intro"></a>Introducción

En un servicio móvil de back-end de JavaScript, puede definir la lógica de negocios personalizada como código JavaScript, que se almacena y ejecuta en el servidor. Este código de script del servidor se asigna a una de las siguientes funciones de servidor:

+ [inserción, lectura, actualización o eliminación de operaciones en una tabla de ][Operaciones de tabla] determinada.
+ [Trabajos programados][Programador de trabajos].
+ [Métodos HTTP definidos en una de API personalizada][Delimitador de API personalizada].

La firma de la función principal del script del servidor depende del contexto en el que se use el script. También puede definir el código de script común como módulos nodes.js que se comparten entre scripts. Para obtener más información, vea [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

Para obtener una descripción de funciones y objetos de script de servidor concretos, vea la [documentación de referencia de scripts de servidor de Servicios móviles]. 


##<a name="table-scripts"></a>Operaciones de tabla

Un script de operación de tabla es un script de servidor que está registrado en una operación en una tabla: insertar, leer, actualizar o eliminar (*del*). El nombre del script debe coincidir con el tipo de operación para el que se ha registrado. Solo puede registrarse un script para una operación de tabla determinada. El script se ejecuta cada vez que una solicitud REST&mdash;invoca la operación determinada, por ejemplo,cuando se recibe una solicitud POST para insertar un elemento en la tabla. Servicios móviles no conserva el estado entre ejecuciones de script. Puesto que se crea un nuevo contexto global cada vez que se ejecuta un script, las variables de estado que se definen en el script vuelven a inicializarse. Si desea almacenar el estado de una solicitud a otra, cree una tabla en el servicio móvil y, a continuación, lea y escriba el estado en la tabla. Para obtener más información, consulte [Inserción de tablas desde scripts].

Escriba scripts de operaciones de tabla si necesita forzar la lógica de negocios personalizada cuando se ejecute la operación. Por ejemplo, el siguiente script rechaza las operaciones de inserción si la longitud de la cadena del campo `text` es superior a diez caracteres: 

	function insert(item, user, request) {
	    if (item.text.length > 10) {
	        request.respond(statusCodes.BAD_REQUEST, 
				'Text length must be less than 10 characters');
	    } else {
	        request.execute();
	    }
	}

Una función de script de tabla siempre cuenta con tres argumentos.

- El primer argumento varía según la operación de tabla. 

	- Para las inserciones y las actualizaciones, es un objeto de **elemento**, que es una representación JSON de la fila afectada por la operación. Esto le permite obtener acceso a los valores de columna por nombre, por ejemplo, *item.Owner*, donde *Owner* es uno de los nombres en la representación JSON.
	- Para la eliminación, es el identificador del registro que se va a eliminar. 
	- Y para la lectura, es un [objeto de consulta] que especifica el conjunto de filas que se devuelve.

- El segundo argumento siempre es un [objeto de usuario][Objeto de usuario] que representa el usuario al que se envía la solicitud. 

- El tercer argumento siempre es un [objeto de solicitud][Objeto de solicitud], por medio del cual puede controlar la ejecución de la operación solicitada y la respuesta que se envía al cliente.

A continuación se muestran las firmas canónicas de funciones principales para las operaciones de tabla: 

+ [Insertar][función insert]: `function insert (item, user, request) { ... }`
+ [Actualizar][función update]: `function update (item, user, request) { ... }`
+ [Eliminar][función delete]: `function del (id, user, request) { ... }`
+ [Leer][función read]: `function read (query, user, request) { ... }`

>[AZURE.NOTE]Una función que se registra para la operación de eliminación se debe llamar _del_ dado que delete es una palabra clave reservada en JavaScript. 

Cada script del servidor cuenta con una función principal y dispone de funciones auxiliares opcionales. A pesar de que es posible que se haya creado un script del servidor para una tabla específica, también puede hacer referencia a otras tablas en la misma base de datos. También puede definir funciones comunes como módulos que pueden compartirse entre scripts. Para obtener más información, vea [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

###<a name="register-table-scripts"></a>Uso de scripts de tablas

Puede definir los scripts del servidor que están registrados en una operación de tabla de alguna de las siguientes formas:

+ En el [Portal de administración de Azure][Portal de administración]. Se puede obtener acceso a los scripts de las operaciones de tabla en la pestaña **Scripts** de una tabla determinada. A continuación se muestra el código predeterminado registrado para el script insert en la tabla `TodoItem`. Puede reemplazar este código por su propia lógica de negocios personalizada.

	![1][1]
	
	Para saber cómo realizar esto, vea [Validación y modificación de datos en los Servicios móviles mediante los scripts de servidor].  

+ Mediante el control de código fuente. Cuando tenga el control de código fuente habilitado, simplemente cree un archivo llamado <em>`<table>`</em>.<em>`<operation>`</em>.js en la subcarpeta .\service\table de su repositorio git, donde <em>`<table>`</em> es el nombre de la tabla y <em>`<operation>`</em> es la operación de tabla que se registra. Para obtener más información, vea [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

+ Desde el símbolo del sistema mediante la herramienta de la línea de comandos de Azure. Para obtener más información, vea [Uso de la herramienta de línea de comandos].


Un script de operación de tabla debe llamar al menos a una de las siguientes funciones del [objeto de solicitud] para tener la seguridad de que el cliente recibe una respuesta. 
 
+ **función execute**: La operación se ha completado como se ha solicitado y se devuelve la respuesta estándar.
 
+ **función respond**: Se ha devuelto una respuesta personalizada.

> [AZURE.IMPORTANT] Es posible que la operación no responda cuando un script cuente con una ruta de código en la que no se invoque **execute** ni **respond**.

El siguiente script llama a la función **execute** para finalizar la operación de datos solicitada por el cliente: 

	function insert(item, user, request) { 
	    request.execute(); 
	}

En este ejemplo, el elemento se inserta en la base de datos y se devuelve el código de estado apropiado al usuario. 

Cuando se llama a la función **execute**, la `item`, [consulta][objeto de consulta] o el valor de `id` que se pasó como primer argumento a la función script se usan para realizar la operación. En una operación de inserción, actualización o consulta, puede modificar el elemento o la consulta antes de llamar a **execute**: 

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
 
>[AZURE.NOTE]En un script de eliminación, el cambio del valor de la variable userId suministrada no afecta a qué registro se elimina.

Para ver más ejemplos, consulte [Lectura y escritura de datos], [Modificación de la solicitud] y [Validación de los datos].


###<a name="override-response"></a>Uso de la respuesta predeterminada

También puede usar un script para implementar la lógica de validación que puede reemplazar el comportamiento de la respuesta predeterminada. Si se produce un error en la validación, llame a la función **respond** en lugar de a la función **execute** y escriba la respuesta al cliente: 

	function insert(item, user, request) {
	    if (item.userId !== user.userId) {
	        request.respond(statusCodes.FORBIDDEN, 
	        'You may only insert records with your userId.');
	    } else {
	        request.execute();
	    }
	}

En este ejemplo, se rechaza la solicitud cuando el elemento insertado no dispone de una propiedad `userId` que coincida con el `userId` del [objeto de usuario] proporcionado pata el cliente autenticado. En este caso, no se produce una operación de base de datos (*insert*), y se devuelve una respuesta con el código de estado 403 HTTP y un mensaje de error personalizado al cliente. Para ver más ejemplos, consulte [Modificación de la respuesta].

###<a name="override-success"></a>Uso de execute success

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

Cuando proporcione un controlador **success** a la función **execute**, también debe llamar a la función **respond** como parte del controlador **success** de modo que en tiempo de ejecución se sepa que el script se ha completado y que se puede escribir una respuesta. Cuando llama a **respond** sin pasar argumentos, los Servicios móviles generan la respuesta predeterminada. 

>[AZURE.NOTE]Puede llamar a **respond** sin argumentos para invocar la respuesta predeterminada solo después de llamar primero a la función **execute**.
 
###<a name="override-error"></a>Uso de control de errores predeterminado

La función **execute** puede dar error si hay una pérdida de conectividad con la base de datos, un objeto no válido o una consulta incorrecta. Los scripts del servidor registran el error y escriben un resultado de error en la respuesta de forma predeterminada cuando se produce el error. Puesto que Servicios móviles ofrece un control de errores predeterminado, no tiene que administrar los errores que puedan producirse en el servicio. 

Puede reemplazar el control de error predeterminado mediante la implementación de control explícito de errores si desea una acción de compensación determinada o cuando desee usar el objeto console global para escribir más información detallada en el registro. Para ello, suministre un controlador **error** a la función **execute**:

	function update(item, user, request) { 
	  request.execute({ 
	    error: function(err) { 
	      // Do some custom logging, then call respond. 
	      request.respond(); 
	    } 
	  }); 
	}
 

Cuando proporcione un controlador error, los Servicios móviles devolverán un resultado de error al cliente al llamar a **respond**.

También puede proporcionar un controlador **success** y un controlador **error** si lo desea.

###<a name="access-headers"></a>Uso de parámetros personalizados

Cuando envía una solicitud al servicio móvil, puede incluir parámetros personalizados en el URI de la solicitud para que los scripts de operaciones de tabla sepan cómo procesar una solicitud determinada. A continuación, modifique su script para inspeccionar el parámetro para determinar la ruta de procesamiento.

Por ejemplo, el siguiente URI para una solicitud POST indica al servicio que no permita la inserción de un nuevo *TodoItem* que tenga el mismo valor de texto:

		https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

A estos parámetros de consulta personalizados se tiene acceso como valores JSON desde la propiedad **parameters** del [objeto de solicitud]. Los Servicios móviles suministran el **objeto de solicitud** a cualquier función registrada en una operación de tabla. El siguiente script de servidor de la operación de inserción comprueba el valor del parámetro `duplicateText` antes de que se ejecute la operación de inserción:

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

Tenga en cuenta que en **insertItemIfNotComplete**, se invoca la función **execute** del [objeto de solicitud] para insertar el elemento donde no haya texto duplicado; de lo contrario, se invoca la función **respond** para notificar al cliente el duplicado. 

Observe la sintaxis de la llamada a la función **success** en el código anterior:

 		        }).read({
		            success: insertItemIfNotComplete
		        });

En JavaScript es una versión compacta del equivalente más largo: 

		success: function(results) 
		{ 
			insertItemIfNotComplete(results); 
		}


###<a name="work-with-users"></a>Uso de de usuarios

En Servicios móviles de Azure puede usar un proveedor de identidades para autenticar usuarios. Para obtener más información, vea [Introducción a la autenticación]. Cuando un usuario autenticado invoca una operación de tabla, los Servicios móviles usan el [objeto de usuario] para proporcionar información sobre el usuario a la función script registrada. La propiedad **userId** se puede usar para almacenar y recuperar información específica del usuario. En el siguiente ejemplo se establece la propiedad de un elemento según el userId de un usuario autenticado:

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

##<a name="custom-api"></a>API personalizada

Una API personalizada es un extremo del servicio móvil al que se obtiene acceso mediante uno o varios métodos HTTP estándar: GET, POST, PUT, PATCH y DELETE. Puede definirse una exportación de función independiente para cada método HTTP proporcionado por la API personalizada, todo en un único archivo de script. El script registrado se invoca cuando se recibe una solicitud a la API personalizada con el método determinado. Para obtener más información, vea [API personalizada].

Cuando los Servicios móviles llaman a funciones de API personalizada en tiempo de ejecución, se suministra un objeto de [solicitud][objeto de solicitud] y [respuesta][objeto de respuesta]. Estos objetos exponen la funcionalidad de la [biblioteca express.js], que los scripts pueden aprovechar. La siguiente API personalizada llamada **hello** es un ejemplo muy sencillo que devuelve _Hello, world!_ en respuesta a una solicitud POST:

		exports.post = function(request, response) {
		    response.send(200, "{ message: 'Hello, world!' }");
		} 

La función **send** del [objeto de respuesta] devuelve la respuesta deseada al cliente. Este código se invoca mediante el envío de una solicitud POST a la siguiente dirección URL:

		https://todolist.azure-mobile.net/api/hello  

El estado global se mantiene entre ejecuciones. 

###<a name="define-custom-api"></a>Uso de una API personalizada

Puede definir los scripts del servidor que están registrados en métodos HTTP en un extremo de la API personalizada de alguna de las siguientes formas:

+ En el [Portal de administración de Azure][Portal de administración]. Los scripts de API personalizada se crean y modifican en la pestaña **API**. El código del script del servidor se encuentra en la pestaña **Scripts** de una API personalizada determinada. A continuación se muestra el script que invoca una solicitud POST en el extremo de la API personalizada `CompleteAll`. 

	![2][2]
	
	Los métodos de permisos de acceso a la API personalizada se encuentran asignados en la pestaña Permissions. Para ver cómo se creó esta API personalizada, vea [Llamada a una API personalizada desde el cliente].  

+ Mediante el control de código fuente. Una vez habilitado el control de código fuente, simplemente cree un archivo denominado <em>`<custom_api>`</em>.js en la subcarpeta .\service\api de su repositorio git, donde <em>`<custom_api>`</em> es el nombre de la API personalizada que se registra. Este archivo de script contiene una función _exported_ para cada método HTTP expuesto por la API personalizada. Los permisos se definen en un archivo .json complementario. Para obtener más información, vea [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

+ Desde el símbolo del sistema mediante la herramienta de la línea de comandos de Azure. Para obtener más información, vea [Uso de la herramienta de línea de comandos].

###<a name="handle-methods"></a>Uso de de métodos HTTP

Una API personalizada puede administrar uno o más métodos HTTP: GET, POST, PUT, PATCH y DELETE. Se define una función exportada para cada método HTTP que administra la API personalizada. Un único archivo de código de la API personalizada puede exportar una o todas las funciones siguientes:

		exports.get = function(request, response) { ... };
		exports.post = function(request, response) { ... };
		exports.patch = function(request, response) { ... };
		exports.put = function(request, response) { ... };
		exports.delete = function(request, response) { ... };

No se puede llamar al extremo de la API personalizada mediante un método HTTP que no se haya implementado en el script del servidor y se devuelve una respuesta de error 405 (Método no permitido). Pueden asignarse niveles de permisos independientes a cada método de HTTP compatible.

###<a name="api-return-xml"></a>Uso de Envío y recepción de datos como XML

Cuando los clientes almacenan y recuperan datos, Servicios móviles usa la notación de objetos JavaScript (JSON) para representar datos en el cuerpo del mensaje. Sin embargo, existen escenarios en los que es posible que desee usar una carga XML. Por ejemplo, las aplicaciones de Tienda Windows disponen de una funcionalidad de notificaciones periódicas integrada que requiere que el servicio emita XML. Para obtener más información, vea [Definición de una API personalizada que admita notificaciones periódicas].

La siguiente función de API personalizada **OrderPizza** devuelve un documento XML sencillo como carga de respuesta:

		exports.get = function(request, response) {
		  response.set('content-type', 'application/xml');
		  var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
		  response.send(200, xml);
		};

Una solicitud HTTP GET invoca esta función de la API personalizada para el siguiente extremo:

		https://todolist.azure-mobile.net/api/orderpizza

###<a name="get-api-user"></a>Uso de Trabajo con usuarios y encabezados en una API personalizada

En Servicios móviles de Azure puede usar un proveedor de identidades para autenticar usuarios. Para obtener más información, vea [Introducción a la autenticación]. Cuando un usuario autenticado solicita una API personalizada, los Servicios móviles usan el [objeto de usuario] para proporcionar información sobre el usuario al código de API personalizada. Al [objeto de usuario] se obtiene acceso desde la propiedad user del [objeto de solicitud]. La propiedad **userId** se puede usar para almacenar y recuperar información específica del usuario. 

La siguiente función de API personalizada **OrderPizza** establece la propiedad owner de un elemento según el userId de un usuario autenticado:

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

También puede obtener acceso a un encabezado HTTP desde el [objeto de solicitud], como se muestra en el siguiente código:

		exports.get = function(request, response) {    
    		var header = request.header('my-custom-header');
    		response.send(200, "You sent: " + header);
		};

En este sencillo ejemplo se lee un encabezado personalizado llamado `my-custom-header` y luego se devuelve el valor en la respuesta.

###<a name="api-routes"></a>Uso de Definición de varias rutas en una API personalizada

Servicios móviles le permite definir varias rutas en una API personalizada. Por ejemplo, las solicitudes HTTP GET a las siguientes direcciones URL de una API personalizada **calculator** invocarán una función **add** o **subtract**, respectivamente: 

+ `https://<service>.azure-mobile.net/api/calculator/add`
+ `https://<service>.azure-mobile.net/api/calculator/sub`

Se definen varias rutas exportando una función **register**, a la que se pasa un objeto **api** (similar al [objeto express en express.js]) que se usa para registrar rutas en el extremo de API personalizada. En el ejemplo siguiente se implementan los métodos **add** y **sub** en la API personalizada **calculator**: 

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

El objeto **api** pasado a la función **register** expone una función para cada método HTTP (**get**, **post**, **put**, **patch**, **delete**). Estas funciones registran una ruta a una función definida para un método HTTP específico. Cada función usa dos parámetros, el primero es el nombre de ruta y el segundo es la función registrada en la ruta. 

Las solicitudes HTTP GET pueden invocar las dos rutas del ejemplo anterior de la API personalizada de la siguiente forma (se muestra con la respuesta):

+ `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

		{"result":3}

+ `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

		{"result":-2}

##<a name="scheduler-scripts"></a>Programador de trabajos

Servicios móviles le permite definir scripts del servidor que se ejecuten como trabajos en una programación fija o a petición en el Portal de administración. Los trabajos programados son útiles para la realización de tareas periódicas como la limpieza de datos de una tabla y el procesamiento por lotes. Para obtener más información, vea [Programación de trabajos].

Los scripts que se han registrado en trabajos programados tienen una función principal con el mismo nombre que el trabajo programado. Puesto que una solicitud HTTP no invoca un script programado, no existe ningún contexto que el tiempo de ejecución del servidor pueda pasar y la función no usa ningún parámetro. Como con otros tipos de scripts, puede disponer de funciones de subrutinas y solicitar módulos compartidos. Para obtener más información, vea [Control de código fuente, código compartido y funciones auxiliares].

###<a name="scheduler-scripts"></a>Uso de scripts de trabajos programados

Puede asignarse un script del servidor a una trabajo definido en el Programador de Servicios móviles. Estos scripts pertenecen al trabajo y se ejecutan de acuerdo con la programación de trabajos. (También puede usar el [Portal de administración] para ejecutar trabajos a petición). Un script que define un trabajo programado no cuenta con parámetros porque Servicios móviles no pasa ningún dato; se ejecuta como una función JavaScript normal y no interactúa con Servicios móviles directamente. 

Puede definir trabajos programados de una de las siguientes formas: 

+ En el [Portal de administración de Azure][Portal de administración] en la pestaña **Script** del programador:

	![3][3]

	Para obtener más información sobre cómo hacer esto, vea [Programación de trabajos de back-end en Servicios móviles]. 

+ Desde el símbolo del sistema mediante la herramienta de la línea de comandos de Azure. Para obtener más información, vea [Uso de la herramienta de línea de comandos].

>[AZURE.NOTE]Cuando tenga con un control de código fuente habilitado, podrá editar archivos de script de trabajos programados directamente en la subcarpeta .\service\scheduler del repositorio git. Para obtener más información, consulte [Inserción de compartido de código mediante el control de código fuente].

##<a name="shared-code"></a>Control de código fuente, código compartido y funciones auxiliares

Puesto que Servicios móviles usa Node.js en el servidor, los scripts ya disponen de acceso a los módulos Node.js integrados. También puede usar el control de código fuente para determinar sus propios módulos o agregar otros módulos Node.js a su servicio.

A continuación se muestran algunos de los módulos más útiles de los que puede sacar provecho en los scripts mediante la función **require** global:

+ **azure**: Expone la funcionalidad del SDK de Azure para Node.js. Para obtener más información, vea el [SDK de Azure para Node.js]. 
+ **crypto**: Proporciona la funcionalidad crypto de OpenSSL. Para obtener más información, vea la [documentación de Node.js][API de criptografía].
+ **path**: Contiene utilidades para trabajar con rutas de archivos. Para obtener más información, vea la [documentación de Node.js][API de ruta de acceso].
+ **querystring**: Contiene utilidades para trabajar con cadenas de consulta. Para obtener más información, vea la [documentación de Node.js][API de cadena de consulta].
+ **request**: Envía solicitudes HTTP a servicios REST externos, como Twitter y Facebook. Para obtener más información, vea [Envío de una solicitud HTTP].
+ **sendgrid**: Envía un correo electrónico mediante el servicio de correo electrónico Sendgrid en Azure. Para obtener más información, vea [Envío de correo electrónico desde los Servicios móviles con SendGrid].
+ **url**: Contiene utilidades para redistribuir y resolver direcciones URL. Para obtener más información, vea la [documentación de Node.js][API de url].
+ **util**: Contiene varias utilidades, como una comprobación del tipo de objeto y un formato de cadena. Para obtener más información, vea la [documentación de Node.js][API de util]. 
+ **zlib**: Ofrece la funcionalidad de compresión, como gzip y deflate. Para obtener más información, vea la [documentación de Node.js][API de zlib]. 

###<a name="modules-helper-functions"></a>Uso de módulos

Los Servicios móviles exponen una serie de módulos que los scripts pueden cargar mediante el uso de la función **require** global. Por ejemplo, un script puede necesitar **request** para crear solicitudes HTTP: 

	function update(item, user, request) { 
	    var httpRequest = require('request'); 
	    httpRequest('http://www.google.com', function(err, response, body) { 
	    	... 
	    }); 
	} 


###<a name="shared-code-source-control"></a>Uso de de código mediante el control de código fuente

Puede usar el control de código fuente con el administrador de paquetes Node.js (npm) para controlar qué módulos se encuentran disponibles para el servicio móvil. Existen dos formas de hacerlo:

+ Para los módulos publicados en npm e instalados por npm, use el archivo package.json para declarar los paquetes que desea que instale el servicio móvil. De esta forma, su servicio siempre dispondrá de acceso a la versión más reciente de los paquetes requeridos. El archivo package.json reside en el directorio `.\service`. Para obtener más información, vea [Compatibilidad con package.json en Servicios móviles de Azure].

+ En los módulos privados o personalizados, puede usar npm para instalar manualmente el módulo en el directorio `.\service\node_modules` del control de código fuente. Para obtener un ejemplo de cómo cargar manualmente un módulo, vea [Aprovechamiento del código compartido y de módulos Node.js en los scripts de servidor].

	>[AZURE.NOTE]Si `node_modules` ya existe en la jerarquía de directorios, NPM creará el subdirectorio `\node-uuid` ahí en lugar de crear un nuevo `node_modules` en el repositorio. En este caso, elimine simplemente el directorio `node_modules` existente.

Después de confirmar el archivo package.json o los módulos personalizados en el repositorio para el servicio móvil, use **require** para hacer referencia a los módulos por el nombre.   

>[AZURE.NOTE] Los módulos que especifica en package.json o que carga en su servicio móvil sólo se usan en el código de script de servidor. El tiempo de ejecución de Servicios móviles no usa estos módulos.

###<a name="helper-functions"></a>Uso de funciones auxiliares

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

En este ejemplo debe pasar un [objeto de tablas] y un [objeto de usuario] a la función compartida. Esto se debe a que los scripts compartidos no pueden obtener acceso al [objeto de tablas] y el [objeto de usuario] solo existe en el contexto de una solicitud.

Los archivos de script se cargan en el directorio compartido mediante el uso del [control de código fuente][Uso de Compartir código mediante el uso de control de código fuente] o mediante la [herramienta de línea de comandos][Uso de la herramienta de línea de comandos].

###<a name="app-settings"></a>Uso de configuración de aplicaciones

Servicios móviles le permite almacenar de forma segura valores como configuraciones de aplicaciones, a los que los scripts del servidor pueden obtener acceso en el tiempo de ejecución.  Cuando agrega datos a la configuración de aplicaciones del servicio móvil, los pares nombre/valor se almacenan cifrados y puede obtener acceso a ellos en los scripts del servidor sin integrarlos como parte del código en el archivo de script. Para obtener más información, vea [Configuración de aplicación].

En el siguiente ejemplo de API personalizada, se usa el [objeto de servicio] proporcionado para recuperar un valor de configuración de aplicación.  

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

Tenga en cuenta que este código también recupera los valores clave del usuario de Twitter almacenados en la pestaña **Identidad** del portal. Puesto que no existe un **objeto de configuración** en la operación de tabla y en los scripts de trabajos programados, debe solicitar al módulo de configuración que obtenga acceso a la configuración de aplicaciones. Para ver un ejemplo completo, vea [Programación de trabajos de back-end en Servicios móviles].

<h2><a name="command-prompt"></a>Uso de la herramienta de línea de comandos</h2>

En Servicios móviles, puede crear, modificar y eliminar scripts del servidor mediante la herramienta de la línea de comandos de Azure. Antes de cargar los scripts, asegúrese de que usa la siguiente estructura de directorios:

![4][4]

Tenga en cuenta que esta estructura de directorios es la misma que la del repositorio git cuando se usa el control de código fuente. 

Cuando se cargan los archivos de script desde la herramienta de línea de comandos, primero debe dirigirse al directorio `.\services\`. El siguiente comando carga un script con el nombre `todoitem.insert.js` desde el subdirectorio `table`:

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

Para obtener más información, vea [Comandos para administrar Servicios móviles de Azure]. 

##<a name="working-with-tables"></a>Trabajo con tablas

Muchos escenarios de Servicios móviles requieren que los scripts del servidor obtengan acceso a las tablas en la base de datos. Por ejemplo, puesto que Servicios móviles no conserva el estado entre ejecuciones de scripts, los datos que tengan que mantenerse entre las ejecuciones de scripts deben almacenarse en tablas. Es posible que desee examinar entradas en una tabla de permisos o almacenar datos de auditoría en lugar de escribir simplemente el registro, donde los datos cuentan con una duración limitada y a los que no puede obtenerse acceso mediante programación. 

Los Servicios móviles cuentan con dos formas de obtener acceso a las tablas: usando un proxy de [objeto de tabla] o creando consultas Transact-SQL con el [objeto mssql]. El [objeto de tabla] facilita el acceso a los datos de tabla desde el código de script de servidor, pero el [objeto mssql] admite operaciones de datos más complejas y ofrece la máxima flexibilidad. 

###<a name="access-tables"></a>Uso de tablas desde scripts

La forma más sencilla de obtener acceso a las tablas desde el script es usar el [objeto de tablas]. La función **getTable** devuelve una instancia de [objeto de tabla] que es un proxy para el acceso a la tabla solicitada. A continuación, puede llamar a las funciones en el proxy para obtener acceso y cambiar los datos. 

Los scripts registrados en las operaciones de tabla y los trabajos programados pueden obtener acceso al [objeto de tablas] como un objeto global. Esta línea de código obtiene un proxy para la tabla *TodoItems* del [objeto de tablas] global: 

		var todoItemsTable = tables.getTable('TodoItems');

Los scripts de la API personalizada pueden obtener acceso al [objeto de tablas] desde la propiedad <strong>service</strong> del [objeto de solicitud] suministrado. Esta línea de código obtiene el [objeto de tablas] de la solicitud:

		var todoItemsTable = request.service.tables.getTable('TodoItem');

> [AZURE.NOTE] Las funciones compartidas no pueden obtener acceso al **objeto de tablas** directamente. En una función compartida, debe pasar el objeto tables a la función.

Una vez que tenga un [objeto de tabla], puede llamar a una o varias funciones de operación de tabla: inserción, actualización, eliminación o lectura. En este ejemplo se leen los permisos de usuario desde una tabla de permisos:

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

Puede encontrar un ejemplo final en la muestra de código aquí: [Uso de Acceso a parámetros personalizados][Uso de Incorporación de parámetros personalizados].

###<a name="bulk-inserts"></a>Uso de Realización de inserciones en masa

Si usa un bucle **for** o **while** para insertar directamente una gran cantidad de elementos en una tabla (1000, por ejemplo), es posible que se encuentre con una limitación de la conexión SQL que provoque que se produzcan errores en las inserciones. Es posible que su solicitud no se complete nunca o que devuelva el error interno del servidor HTTP 500.  Para evitar este problema, puede insertar los elementos en lotes de 10. Una vez que se haya insertado el primer lote, envíe el siguiente, y así sucesivamente.

Puede establecer el tamaño de un lote de registros que se va a insertar en paralelo con el siguiente script. Le recomendamos que use un número reducido de registros. La función **insertItems** realiza llamadas recursivas a sí misma cuando se ha completado el lote de inserción asincrónico. El bucle for al final inserta un registro cada vez y llama a **insertComplete** en caso de éxito y a **errorHandler** en caso de error. **insertComplete** controla si **insertItems** se llamará de forma recursiva para el siguiente lote, o si se realiza el trabajo y debe salir del script.

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


Puede encontrar el ejemplo de código al completo y la discusión relacionada con él en esta [entrada de blog](http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx). Si usa este código, puede adaptarlo a su situación específica y probarlo minuciosamente.

###<a name="JSON-types"></a>Uso de de tipos JSON a tipos de base de datos

Las recopilaciones de tipos de datos en el cliente y en la tabla de base de datos de Servicios móviles son diferentes. Algunas veces se asignan fácilmente entre sí y otras veces no. Los Servicios móviles realizan una serie de transformaciones de tipo en la asignación:

- Los tipos específicos del lenguaje del cliente se encuentran serializados en JSON.
- La representación JSON se traduce a JavaScript antes de que aparezca en los scripts del servidor.
- Los tipos de datos de JavaScript se convierten a tipos de base de datos SQL cuando se guardan con el [objeto de tablas].

La transformación del esquema de cliente en JSON varía entre plataformas.  JSON.NET se usa en los clientes de Windows Phone y Tienda Windows. El cliente Android usa la biblioteca gson.  El cliente iOS usa la clase NSJSONSerialization. Se utiliza el comportamiento de serialización predeterminado de cada una de estas bibliotecas, excepto en el caso en el que los objetos date se conviertan en cadenas que contengan la fecha codificada mediante ISO 8601.

Cuando escriba scripts del servidor que usen funciones [insert], [update], [read] o [delete], puede obtener acceso a la representación de JavaScript de sus datos. Los Servicios móviles usan la función de deserialización de Node.js ([JSON.parse](http://es5.github.io/#x15.12)) para transformar JSON en el cable de los objetos JavaScript. Sin embargo, los Servicios móviles realizan una transformación para extraer objetos **Date** de las cadenas ISO 8601.

Cuando usa el [objeto de tablas] o el [objeto mssql], o simplemente deja que se ejecuten los scripts de tabla, los objetos JavaScript deserializados se insertan en la base de datos SQL. En ese proceso, las propiedades del objeto se asignan a tipos T-SQL:

<table border="1">
<tr>
<td>Propiedad JavaScript</td>
<td>Tipo T-SQL</td>
</tr><tr>
<td>Number</td>
<td>Float(53)</td>
</tr><tr>
<td>Boolean</td>
<td>Bit</td>
</tr><tr>
<td>Date</td>
<td>DateTimeOffset(3)</td>
</tr>
<tr>
<td>String</td>
<td>Nvarchar(max)</td>
</tr>
<tr>
<td>Buffer</td>
<td>No compatible</td>
</tr><tr>
<td>Object</td>
<td>No compatible</td>
</tr><tr>
<td>Array</td>
<td>No compatible</td>
</tr><tr>
<td>Stream</td>
<td>No compatible</td>
</tr>
</table> 

###<a name="TSQL"></a>Uso de Transact-SQL para obtener acceso a las tablas

La forma más sencilla de trabajar con datos de tabla de scripts de servidor es mediante un proxy de [objeto de tabla]. Sin embargo, existen escenarios más avanzados que no son compatibles con el [objeto de tabla], como las consultas de unión y otras consultas complejas y la invocación de procedimientos almacenados. En estos casos, debe ejecutar las instrucciones Transact-SQL directamente contra la tabla relacional mediante el [objeto mssql]. Este objeto proporciona las siguientes funciones:

- **query**: ejecuta una consulta, especificada por una cadena TSQL; los resultados se devuelven a la devolución de llamada **success** en el objeto de **opciones**. La consulta puede incluir parámetros si el parámetro *params* está presente.
- **queryRaw**: como *query*, con la excepción de que el conjunto de resultados devuelto por la consulta está en formato sin procesar (consulte el siguiente ejemplo).
- **open**: se usa para conectarse a la base de datos de Servicios móviles y, a continuación, puede usar el objeto de conexión para invocar operaciones de base de datos como las transacciones.

Estos métodos le proporcionan un control cada vez mayor de nivel bajo sobre el procesamiento de consultas.

+ [Uso de una consulta estática]
+ [Uso de una consulta dinámica]
+ [Uso de de tablas relacionales]
+ [Uso de Ejecución de una consulta que devuelve resultados *raw*]
+ [Uso de acceso a una conexión de base de datos]	

####<a name="static-query"></a>Uso de una consulta estática

La siguiente consulta no dispone de parámetros y devuelve tres registros a partir de la tabla `statusupdate`. El conjunto de filas se encuentra en el formato JSON estándar.

		mssql.query('select top 3 * from statusupdates', {
		    success: function(results) {
		        console.log(results);
		    },
            error: function(err) {
                console.log("error is: " + err);
			}
		});


####<a name="dynamic-query"></a>Uso de una consulta dinámica parametrizada

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


####<a name="joins"></a>Uso de tablas relacionales

Puede unir dos tablas mediante el método **query** del [objeto mssql] para pasar el código TSQL que implementa la unión. Supongamos que disponemos de algunos elementos en la tabla **ToDoItem** y que cada elemento de la tabla dispone de una propiedad **priority** que se corresponde con una columna de la tabla. El aspecto del elemento sería el siguiente:

		{ text: 'Take out the trash', complete: false, priority: 1}

Supongamos también que contamos con una tabla adicional denominada **Priority** con filas que contienen un **número** de prioridad y una **descripción** de texto. Por ejemplo, el número de prioridad 1 debe tener la descripción de crítico "Critical". El aspecto del objeto será el siguiente:

		{ number: 1, description: 'Critical'}

Ahora podemos reemplazar el número de **prioridad** en nuestro elemento por la descripción de texto del número de prioridad. Hacemos esta operación con una unión relacional de las dos tablas.

		mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
			success: function(results) {
				console.log(results);
			},
            error: function(err) {
                console.log("error is: " + err);
		});
	
El script une las dos tablas y escribe los resultados en el registro. Los objetos resultantes pueden tener la siguiente apariencia:

		{ text: 'Take out the trash', complete: false, description: 'Critical'}


####<a name="raw"></a>Uso de Ejecución de una consulta que devuelve resultados *raw*

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

####<a name="connection"></a>Uso de acceso a una conexión de base de datos

Puede usar el método **open** para obtener acceso a la conexión de base de datos. Puede necesitarlo, por ejemplo, para usar transacciones de base de datos.

Una ejecución correcta de **open** provoca que la conexión de base de datos se pase a la función **success** como un parámetro. Puede invocar cualquiera de las siguientes funciones en el **objeto de conexión**: *close*, *queryRaw*, *query*, *beginTransaction*, *commit* y *rollback*.

		    mssql.open({
		        success: function(connection) {
		            connection.query(//query to execute);
		        },
	            error: function(err) {
	                console.log("error is: " + err);
				}
		    });

##<a name="debugging"></a>Depuración y solución de problemas

La forma más sencilla de depurar y solucionar los problemas de los scripts del servidor es escribir en el registro de servicio. De forma predeterminada, Servicios móviles escribe errores que se producen durante la ejecución del script del servicio en los registros de servicio. Los scripts también pueden escribir en los registros. La escritura en los registros es un método efectivo para depurar los scripts y validar que se están comportando según lo esperado.

###<a name="write-to-logs"></a>Uso de del resultado en los registros de servicios móviles

Para escribir en los registros, use el [objeto de consola] global. Use la función **log** o **info** para registrar advertencias de nivel de información. Las funciones **warning** y **error** registran sus niveles respectivos, a los que se llama en los registros. 

> [AZURE.NOTE] Para ver los registros para el servicio móvil, inicie sesión en el [Portal de administración](https://manage.windowsazure.com/), seleccione el servicio móvil y, a continuación, seleccione la pestaña **Registros**.

También puede usar las funciones de registro del [objeto de consola] para dar formato a sus mensajes mediante parámetros. En el siguiente ejemplo se proporciona un objeto JSON como parámetro para la cadena de mensaje:

	function insert(item, user, request) {
	    console.log("Inserting item '%j' for user '%j'.", item, user);  
	    request.execute();
	}

Tenga en cuenta que la cadena `%j` se usa como marcador de posición para un objeto JSON y que esos parámetros se proporcionan en orden secuencial. 

Para evitar la sobrecarga del registro, debe quitar o deshabilitar las llamadas a console.log() que no sean necesarias para la producción.

<!-- Anchors. -->
[Introducción]: #intro
[Operaciones de tabla]: #table-scripts
[Uso de operaciones de tabla]: #register-table-scripts
[Uso de Definición de scripts de tabla]: #execute-operation
[Uso de Invalidación de la respuesta predeterminada]: #override-response
[Uso de Modificación de una operación]: #modify-operation
[Uso de Invalidación de éxito y error]: #override-success-error
[Uso de execute success]: #override-success
[Uso de control de errores predeterminado]: #override-error
[Uso de a tablas desde scripts]: #access-tables
[Uso de parámetros personalizados]: #access-headers
[Uso de Trabajo con usuarios]: #work-with-users
[Uso de de scripts de trabajos programados]: #scheduler-scripts
[Uso de Restricción del acceso a tablas]: #authorize-tables
[Uso de Transact-SQL para obtener acceso a las tablas]: #TSQL
[Uso de una consulta estática]: #static-query
[Uso de una consulta dinámica]: #dynamic-query
[Uso de Ejecución de una consulta que devuelve resultados *raw*]: #raw
[Uso de acceso a una conexión de base de datos]: #connection
[Uso de de tablas relacionales]: #joins
[Uso de de inserciones en masa]: #bulk-inserts
[Uso de de tipos JSON a tipos de base de datos]: #JSON-types
[Uso de de módulos Node.js]: #modules-helper-functions
[Uso de del resultado en los registros de servicios móviles]: #write-to-logs
[Control de código fuente, código compartido y funciones auxiliares]: #shared-code
[Uso de la herramienta de línea de comandos]: #command-prompt
[Trabajo con tablas]: #working-with-tables
[Delimitador de API personalizada]: #custom-api
[Uso de una API personalizada]: #define-custom-api
[Uso de compartido de código mediante el control de código fuente]: #shared-code-source-control
[Uso de Funciones auxiliares]: #helper-functions
[Depuración y solución de problemas]: #debugging
[Uso de de métodos HTTP]: #handle-methods
[Uso de Trabajo con usuarios y encabezados en una API personalizada]: #get-api-user
[Uso de Acceso a los encabezados de solicitud de API personalizados]: #get-api-headers
[Programador de trabajos]: #scheduler-scripts
[Uso de de varias rutas en una API personalizada]: #api-routes
[Uso de y recepción de datos como XML]: #api-return-xml
[Uso de Trabajo con configuración de aplicaciones]: #app-settings

[1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
[2]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
[3]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
[4]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png

<!-- URLs. -->
[Referencia del script de servidor de Servicios móviles]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554226.aspx
[Programación de trabajos de back-end en Servicios móviles]: /es-es/develop/mobile/tutorials/schedule-backend-tasks/
[request object]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554218.aspx
[response object]: http://msdn.microsoft.com/es-es/library/windowsazure/dn303373.aspx
[User object]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554220.aspx
[push object]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554217.aspx
[función insert]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554229.aspx
[insert]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554229.aspx
[función update]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554214.aspx
[función delete]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554215.aspx
[función read]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554224.aspx
[update]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554214.aspx
[delete]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554215.aspx
[read]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554224.aspx
[objeto de consulta]: http://msdn.microsoft.com/es-es/library/windowsazure/jj613353.aspx
[apns, objeto]: http://msdn.microsoft.com/es-es/library/windowsazure/jj839711.aspx
[mpns, objeto]: http://msdn.microsoft.com/es-es/library/windowsazure/jj871025.aspx
[wns, objeto]: http://msdn.microsoft.com/es-es/library/windowsazure/jj860484.aspx
[objeto de tabla]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554210.aspx
[objeto de tablas]: http://msdn.microsoft.com/es-es/library/windowsazure/jj614364.aspx
[objeto mssql]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554212.aspx
[objeto de consola]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554209.aspx
[Lectura y escritura de datos]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631640.aspx
[Validación de datos]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631638.aspx
[Modificación de la solicitud]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631635.aspx
[Modificación de la respuesta]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631631.aspx
[Portal de administración]: https://manage.windowsazure.com/
[Programación de trabajos]: http://msdn.microsoft.com/es-es/library/windowsazure/jj860528.aspx
[Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
[Comandos para administrar Servicios móviles de Azure]: /es-es/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services/#Mobile_Scripts
[Inserción de la Tienda Windows]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet/
[Inserción de Windows Phone]: /es-es/develop/mobile/tutorials/get-started-with-push-wp8/
[Inserción de iOS]: /es-es/develop/mobile/tutorials/get-started-with-push-ios/
[Inserción de Android]: /es-es/develop/mobile/tutorials/get-started-with-push-android/
[SDK de Azure para Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=275539
[Envío de solicitud HTTP]: http://msdn.microsoft.com/es-es/library/windowsazure/jj631641.aspx
[Envío de correo electrónico desde Servicios móviles con SendGrid]: /es-es/develop/mobile/tutorials/send-email-with-sendgrid/
[Introducción a la autenticación]: http://go.microsoft.com/fwlink/p/?LinkId=287177
[API de criptografía]: http://go.microsoft.com/fwlink/p/?LinkId=288802
[API de ruta de acceso]: http://go.microsoft.com/fwlink/p/?LinkId=288803
[API de cadena de consulta]: http://go.microsoft.com/fwlink/p/?LinkId=288804
[API de url]: http://go.microsoft.com/fwlink/p/?LinkId=288805
[API de util]: http://go.microsoft.com/fwlink/p/?LinkId=288806
[API de zlib]: http://go.microsoft.com/fwlink/p/?LinkId=288807
[API personalizada]: http://msdn.microsoft.com/es-es/library/windowsazure/dn280974.aspx
[Llamada a una API personalizada desde el cliente]: /es-es/develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
[biblioteca express.js]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[Definición de una API personalizada que admita notificaciones periódicas]: /es-es/develop/mobile/tutorials/create-pull-notifications-dotnet/
[objeto express en express.js]: http://expressjs.com/api.html#express
[Almacenamiento de scripts de servidor en control de fuente]: /es-es/develop/mobile/tutorials/store-scripts-in-source-control/
[Aprovechamiento del código compartido y de módulos Node.js en los scripts del servidor]: /es-es/develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
[objeto de servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/dn303371.aspx
[Configuración de aplicaciones]: http://msdn.microsoft.com/es-es/library/dn529070.aspx
[módulo config]: http://msdn.microsoft.com/es-es/library/dn508125.aspx
[Compatibilidad con package.json en Servicios móviles de Azure]: http://go.microsoft.com/fwlink/p/?LinkId=391036


<!--HONumber=42-->
