<properties urlDisplayName="HTML Client" pageTitle="Uso de un cliente HTML: Servicios móviles de Azure" metaKeywords="Azure Mobile Services, Mobile Service HTML client, HTML client" description="Obtenga información acerca de cómo usar un cliente HTML para Servicios móviles de Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use an HTML/JavaScript client for Azure Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />


# Uso de un cliente HTML/JavaScript para Servicios móviles de Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/en-us/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/en-us/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/en-us/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


En esta guía se muestra cómo ejecutar escenarios comunes con la utilización de un cliente HTML/JavaScript para Servicios móviles de Azure. Entre las tareas incluidas se encuentran la consulta, inserción, actualización y eliminación de datos, la autenticación de usuarios y la administración de errores. Si no tiene experiencia en Servicios móviles, considere primero la opción de completar la [Guía de inicio rápido de JavaScript para la Tienda Windows] o la [Guía de inicio rápido de HTML] de Servicios móviles. El tutorial de la guía de inicio rápido le ayuda a configurar la cuenta y crear el primer servicio móvil.


## Tabla de contenido

- [Qué es Servicios móviles]
- [Conceptos]
- [Creación del cliente de Servicios móviles]
- [Consulta de datos desde un servicio móvil]
	- [Filtro de datos devueltos]
    - [Clasificación de datos devueltos]
	- [Devolución de datos en páginas]
	- [Selección de columnas específicas]
	- [Búsqueda de datos por identificador]
	- [Ejecución de una operación de consulta de OData]
- [Inserción de datos en un servicio móvil]
- [Modificación de datos en un servicio móvil]
- [Eliminación de datos en un servicio móvil]
- [Visualización de datos en la interfaz de usuario]
- [Autenticación de usuarios]
- [Control de errores]
- [Uso de promesas]
- [Personalización de encabezados de solicitud]
- [Uso compartido de recursos entre orígenes]
- [Pasos siguientes]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Reproducción de del cliente de Servicios móviles



En el editor web, abra el archivo HTML y agregue lo siguiente a las referencias de script de la página:

    <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

>[WACOM.NOTE]Para una aplicación de la Tienda Windows escrita en JavaScript/HTML, solo tiene que agregar el paquete NuGet **WindowsAzure.MobileServices.WinJS** a su proyecto.

En el editor, abra o cree un archivo JavaScript, agregue el siguiente código que define la variable "MobileServiceClient" y, a continuación, proporcione la dirección URL de la aplicación y la clave de la aplicación desde el servicio móvil en el constructor "MobileServiceClient", en dicho orden.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Debe sustituir el marcador de posición "AppUrl" por la dirección URL de la aplicación del servicio móvil y "AppKey" por la clave de la aplicación. Para obtener información acerca de cómo obtener la dirección URL de la aplicación y la clave de la aplicación del servicio móvil, consulte el tutorial [Introducción a los datos en JavaScript de Tienda Windows] o [Introducción a los datos en HTML/JavaScript].

##<a name="querying"></a>Reproducción de desde un servicio móvil

Todo el código que obtiene acceso o modifica los datos de la tabla de Base de datos SQL llama a las funciones del objeto "MobileServiceTable". Obtenga una referencia a la tabla llamando a la función "GetTable()" en una instancia de "MobileServiceClient".

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Reproducción de Filtro de datos devueltos

El siguiente código muestra cómo filtrar los datos incluyendo una cláusula "where" en una consulta. Devuelve todos los elementos de "todoItemTable" cuyo campo "Complete" es igual a "false". "todoItemTable" es la referencia a la tabla de servicio móvil que se ha creado previamente. La función Where aplica un predicado de filtrado de fila a la consulta en la tabla. Acepta como argumento una función o un objeto JSON que define el filtrado de filas y devuelve una consulta que se puede componer aún más.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Al agregar la llamada a "where" en el objeto Query y pasar un objeto como un parámetro, ordenamos a Servicios móviles que devuelva solo las filas cuya columna "complete" contenga el valor "false". Además, consulte el URI de solicitud siguiente y tenga en cuenta que se está modificando la propia cadena de consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Puede ver el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o Fiddler.

Normalmente, esta solicitud se traduciría aproximadamente en la siguiente consulta SQL en el servidor:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

El objeto pasado al método "where" puede tener un número arbitrario de parámetros, que se interpretarán como cláusulas AND para la consulta. Por ejemplo, la siguiente línea:

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Se traduciría aproximadamente (para la misma solicitud mostrada anteriormente) de la siguiente forma:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

La instrucción "where" anterior y la consulta SQL anterior encuentran elementos incompletos asignados a "david" de dificultad "medium".

No obstante, hay otra forma de escribir la misma consulta. Una llamada ".where" en el objeto Query agregará una expresión "AND" a la cláusula "WHERE", por lo que, en su lugar, podríamos haber escrito eso en tres líneas:

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

O bien usar la API fluida:

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

Los dos métodos son equivalentes y pueden usarse indistintamente. Todas las llamadas a "where" hasta el momento obtienen un objeto con algunos parámetros y se comparan en términos de igualdad con respecto a los datos de la base de datos. No obstante, hay otra sobrecarga del método de consulta, que usa una función en lugar del objeto. En esta función, podemos escribir expresiones más complejas, con la utilización de operadores como inequality y otras operaciones relacionales. En estas funciones, la palabra clave "this" enlaza al objeto de servidor.

El cuerpo de la función se traduce en una expresión booleana Open Data Protocol (OData) que se pasa a un parámetro de la cadena de consulta. Es posible transferir una función que no usa ningún parámetro, como:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Si se transfiere una función con parámetros, cualquier argumento después de la cláusula "where" se enlaza a los parámetros de función en orden. Cualquier objeto procedente de fuera del ámbito de la función DEBE transferirse como parámetro; la función no puede capturar ninguna variable externa. En los dos ejemplos siguientes, el argumento "david" se enlaza al parámetro "name" y, en el primer ejemplo, el argumento "medium" también se enlaza al parámetro "level". Además, la función debe consistir en una única instrucción "return" con una expresión admitida, como:

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

Por tanto, siempre que sigamos las reglas, podemos agregar filtros más complejos a las consultas de la base de datos, como:

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

Es posible combinar "where" con "orderBy", "take" y "skip". Consulte la siguiente sección para obtener información detallada.

### <a name="sorting"></a>Reproducción de de datos devueltos

El siguiente código muestra cómo ordenar datos incluyendo una función "orderBy" o "orderByDescending" en la consulta. Devuelve los elementos de "todoItemTable" ordenados de manera ascendente por el campo "text". De forma predeterminada, el servidor devuelve solo los primeros 50 elementos.

<div class="dev-callout"><strong>Nota:</strong> <p>Se utiliza el tamaño de página del servidor de forma predeterminada para evitar que se devuelvan todos los elementos. De esta forma, se evita que las consultas predeterminadas de los conjuntos de datos de gran tamaño incidan negativamente en el servicio. </p> </div>
>
Puede aumentar el número de elementos que se devuelven si llama a "take", tal como se describe en la sección siguiente. "todoItemTable" es la referencia a la tabla de servicio móvil que se ha creado previamente.

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>Reproducción de de datos en páginas

El siguiente código muestra cómo implementar la paginación en los datos devueltos a través de las cláusulas "take" y "skip" en la consulta.  Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Tenga en cuenta que el método "take(3)" se tradujo en la opción de consulta "$top=3" en el URI de consulta.

La siguiente consulta revisada omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

También puede ver el URI de la solicitud enviada al servicio móvil. Observe que el método "skip(3)" se ha traducido a la opción de consulta "$skip=3" en el URI de la consulta.

Este es un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos "take" y "skip". En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores.

### <a name="selecting"></a>Reproducción de de columnas específicas

Puede especificar qué conjunto de propiedades incluir en los resultados agregando una cláusula "Select" a su consulta. Por ejemplo, el siguiente código devuelve las propiedades "id", "complete" y "text" de cada fila en "todoItemTable":

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

Aquí los parámetros para la función de selección son los nombres de las columnas de la tabla que desea devolver.


Todas las funciones descritas en adelante son adicionales, por lo que es posible seguir llamándolas y cada vez irá afectando más a la consulta. A continuación se muestra un ejemplo más:

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>Reproducción de Búsqueda de datos por identificador

La función "lookup" usa solo el valor "id" y devuelve el objeto de la base de datos con dicho identificador. Las tablas de la base de datos se crean con una columna "id" de cadena o de entero. La opción predeterminada es una columna "id" de cadena.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Ejecución de una operación de consulta de OData

Servicios móviles usa las convenciones de URI de consulta OData para la composición y ejecución de las consultas REST.  No todas las consultas OData se pueden crear mediante las funciones de consulta integradas, especialmente las operaciones de filtro complejas como, por ejemplo, la búsqueda de una subcadena en una propiedad. Para estos tipos de consultas complejas, puede pasar cualquier opción de consulta OData válida a la función "read", tal como se indica a continuación:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[WACOM.NOTE]Cuando proporciona una cadena de opción de consulta OData sin formato en la función "read", no es posible usar también los métodos de compilador de consultas en la misma consulta. En este caso, debe componer la consulta completa como una cadena de consulta OData. Para obtener más información sobre las opciones de consulta del sistema OData, consulte la [referencia de opciones de consulta del sistema OData].

<h2><a name="inserting"></a>Reproducción de de datos en un servicio móvil</h2>

El siguiente código muestra cómo insertar filas nuevas en una tabla. El cliente solicita la inserción de una fila de datos mediante el envío de una solicitud POST al servicio móvil. El cuerpo de la solicitud contiene los datos que se insertarán, como un objeto JSON.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

De esta forma se insertan los datos en la tabla a partir del objeto JSON proporcionado. También puede especificar una función de devolución de llamada que se invocará cuando se haya completado la inserción:

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});


Servicios móviles es compatible con valores de cadena personalizados y exclusivos para el identificador de tabla. Esto permite a las aplicaciones usar valores personalizados como nombres de usuario o direcciones de correo electrónico para la columna de identificador de una tabla de Servicios móviles. Si desea identificar cada registro mediante una dirección de correo electrónico, podría usar el siguiente objeto JSON.

			todoItemTable.insert({
			   id: "myemail@domain.com",
			   text: "New Item",
			   complete: false
			})

Si no se proporciona un valor de identificador de cadena cuando se insertan nuevos registros en una tabla, Servicios móviles generará un valor exclusivo para el identificador.

La compatibilidad de los identificadores de cadena ofrece las siguientes ventajas a los desarrolladores:

+ Los identificadores pueden generarse sin realizar un recorrido de ida y vuelta en la base de datos.
+ Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
+ Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

También puede usar scripts de servidor para configurar valores de identificador. En el ejemplo de script siguiente se genera un GUID personalizado y se le asigna un nuevo identificador de registro. Este es similar al valor del identificador que generaría Servicios móviles si no hubiera pasado un valor para el identificador del registro.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Si una aplicación proporciona un valor para un identificador, Servicios móviles lo guardará como está. Esto incluye los espacios en blanco al principio o al final. Los espacios en blanco no se eliminarán del valor.

El valor "id" debe ser exclusivo y no debe incluir caracteres de los siguientes conjuntos:

+ Caracteres de control: [0x0000-0x001F] y [0x007F-0x009F]. Para obtener más información, consulte [Códigos de control ASCII C0 y C1].
+  Caracteres imprimibles: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Los identificadores "." y ".."

También puede usar identificadores de números enteros para las tablas. Para usar un identificador de números enteros, debe crear la tabla con el comando "mobile table create" mediante la opción "--integerId". Este comando se usa con la interfaz de la línea de comandos (CLI) de Azure. Para obtener más información sobre el uso de la CLI, consulte [CLI para administrar tablas de Servicios móviles].


<h2><a name="modifying"></a>Reproducción de datos en un servicio móvil</h2>

El siguiente código muestra cómo actualizar datos en una tabla. El cliente solicita la actualización de una fila de datos mediante el envío de una solicitud PATCH al servicio móvil. El cuerpo de la solicitud contiene los campos específicos que se actualizarán, como un objeto JSON. Actualiza un elemento existente en la tabla "todoItemTable".

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			})

El primer parámetro especifica la instancia que se actualizará en la tabla, según especifique su identificador.

También puede especificar una función de devolución de llamada que se invocará cuando se haya completado la actualización:

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			}).done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="deleting"></a>Reproducción de datos en un servicio móvil</h2>

El siguiente código muestra cómo eliminar datos de una tabla. El cliente solicita la eliminación de una fila de datos mediante el envío de una solicitud DELETE al servicio móvil. Elimina un elemento existente en la tabla todoItemTable.

			todoItemTable.del({
			   id: idToDelete
			})

El primer parámetro especifica la instancia que se eliminará en la tabla, según especifique su identificador.

También puede especificar una función de devolución de llamada que se invocará cuando se haya completado la eliminación:

			todoItemTable.del({
			   id: idToDelete
			}).done(function () {
			   /* Do something */
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="binding"></a>Reproducción de de datos en la interfaz de usuario</h2>

En esta sección se describe cómo mostrar objetos de datos devueltos mediante elementos de la interfaz de usuario. Para consultar elementos en "todoItemTable" y visualizarlos en una lista muy sencilla, puede ejecutar el siguiente código de ejemplo. No se realiza selección, filtrado ni ordenación de ningún tipo.

			var query = todoItemTable;

			query.read().then(function (todoItems) {
			   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
			   var listOfItems = document.getElementById('placeToInsert');
			   for (var i = 0; i < todoItems.length; i++) {
			      var li = document.createElement('li');
			      var div = document.createElement('div');
			      div.innerText = todoItems[i].text;
			      li.appendChild(div);
			      listOfItems.appendChild(li);
			   }
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

En una aplicación de la Tienda Windows, los resultados de una consulta se pueden usar para crear un objeto [WinJS.Binding.List], que puede enlazarse como el origen de datos de un objeto [ListView]. Para obtener más información, consulte [Enlace de datos (aplicaciones de la Tienda Windows con JavaScript y HTML)].

<h2><a name="caching"></a>Reproducción de usuarios</h2>

Servicios móviles es compatible con la autenticación y autorización de los usuarios de aplicaciones mediante una serie de proveedores de identidades externas: Facebook, Google, cuenta de Microsoft y Twitter. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Introducción a la autenticación].

Se admiten dos flujos de autenticación: un _server flow_ y un _client flow_. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

<h3>Flujo de servidor</h3>
Para que Servicios móviles administre el proceso de autenticación en la aplicación de la Tienda Windows o HTML5, debe registrar la aplicación con el proveedor de identidades. A continuación, en el servicio móvil, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor. Para obtener más información, consulte el tutorial "Introducción a la autenticación" ([Tienda Windows][Get started with authentication Windows Store]/[HTML][Get started with authentication]).

Una vez que haya registrado el proveedor de identidades, simplemente llame al método [LoginAsync] con el valor [MobileServiceAuthenticationProvider] del proveedor. Por ejemplo, para iniciar sesión con Facebook, use el siguiente código:

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método "login" anterior por uno de los siguientes: "microsoftaccount", "facebook", "twitter", "google" o "windowsazureactivedirectory".

En este caso, Servicios móviles administra el flujo de autenticación de OAuth 2.0 mostrando la página de inicio de sesión del proveedor seleccionado y generando un token de autenticación de Servicios móviles después de que se realice un inicio de sesión correcto con el proveedor de identidades. La función [login], cuando se completa, devuelve un objeto JSON (**user**) que expone el identificador de usuario y el token de autenticación de Servicios móviles en los campos **userId** y **authenticationToken**, respectivamente. El token puede almacenarse en caché y volver a usarse hasta que expire. Para obtener más información, consulte [Almacenamiento en caché del token de autenticación].

<div class="dev-callout"><b>Aplicación de la Tienda Windows</b>
<p>Cuando use el proveedor de inicio de sesión de la cuenta de Microsoft para autenticar a los usuarios de la aplicación de la Tienda Windows, también debe registrar el paquete de la aplicación con Servicios móviles. Cuando registre la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, el cliente podrá volver a usar las credenciales de inicio de sesión de la cuenta de Microsoft para conseguir una experiencia de inicio de sesión único. Si no realiza este procedimiento, los usuarios de inicio de sesión de la cuenta de Microsoft visualizarán una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión. Para saber cómo registrar el paquete de la aplicación de la Tienda Windows, consulte <a href="/en-us/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft</a>. Una vez que se registre la información del paquete con Servicios móviles, llame al método <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> proporcionando un valor de <strong>true</strong> para el parámetro <em>useSingleSignOn</em> con el fin de volver a usar las credenciales.</p>
</div>

<h3>Flujo de cliente</h3>
La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y, a continuación, proporcionar el token devuelto a Servicios móviles para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

El siguiente ejemplo usa el SDK de Live, que admite el inicio de sesión único para aplicaciones de la Tienda Windows mediante la cuenta de Microsoft:

		WL.login({ scope: "wl.basic"}).then(function (result) {
		      client.login(
		            "microsoftaccount",
		            {"authenticationToken": result.session.authentication_token})
		      .done(function(results){
		            alert("You are now logged in as: " + results.userId);
		      },
		      function(error){
		            alert("Error: " + err);
		      });
		});

Este ejemplo simplificado obtiene un token de Live Connect, que se proporciona a los Servicios móviles realizando una llamada a la función [login]. Para obtener un ejemplo más completo de cómo usar una cuenta de Microsoft para proporciona una experiencia de inicio de sesión único, consulte [Autenticación de la aplicación con el inicio de sesión único].

Al usar las API de Facebook o Google para la autenticación de cliente, el ejemplo varía ligeramente.

		client.login(
		     "facebook",
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

En este ejemplo se asume que el token proporcionado por el SDK del proveedor correspondiente se almacena en la variable "token".
En este momento Twitter no se puede usar para la autenticación de cliente.

<h3>Almacenamiento en caché del token de autenticación</h3>
En algunos casos, la llamada al método de inicio de sesión puede evitarse después de la primera vez que el usuario se autentique. Puede usar [sessionStorage] o [localStorage] para almacenar en caché la identidad del usuario actual la primera vez que se inicie sesión y para que compruebe las veces posteriores si ya dispone de la identidad de usuario en la memoria caché. Si la caché está vacía o se produce un error en la llamada (lo que significa que el inicio de sesión actual ha caducado), será necesario pasar por el proceso de inicio de sesión.

        // After logging in
        sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

        // Log in
        if (sessionStorage.loggedInUser) {
           client.currentUser = JSON.parse(sessionStorage.loggedInUser);
        } else {
           // Regular login flow
       }

         // Log out
        client.logout();
        sessionStorage.loggedInUser = null;


<h2><a name="errors"></a>Reproducción de errores</h2>

Existen varias formas de detectar, validar y solucionar errores en Servicios móviles.

Por ejemplo, los scripts de servidor se registran en un servicio móvil y pueden usarse para realizar una amplia variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. Imagine que define y registra un script de servidor que valida y modifica datos:

			function insert(item, user, request) {
			   if (item.text.length > 10) {
				  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
			   } else {
			      request.execute();
			   }
			}

Este script del servidor valida la longitud de los datos de cadena enviados al servicio móvil y rechaza las cadenas que son muy largas, en este caso superiores a 10 caracteres.

Ahora que el servicio móvil está validando datos y enviando respuestas de error en el servidor, puede actualizar la aplicación HTML para poder gestionar las respuestas de error a partir de la validación.

		todoItemTable.insert({
		   text: itemText,
		   complete: false
		})
		   .then(function (results) {
		   alert(JSON.stringify(results));
		}, function (error) {
		   alert(JSON.parse(error.request.responseText).error);
		});


Para ofrecer más detalles, transfiere el controlador del error como el segundo argumento cada vez que obtiene acceso a los datos:

			function handleError(message) {
			   if (window.console && window.console.error) {
			      window.console.error(message);
			   }
			}

			client.getTable("tablename").read().then(function (data) { /* do something */ }, handleError);

<h2><a name="promises"></a>Reproducción de de promesas</h2>

Las promesas ofrecen un mecanismo para programar el trabajo que hay que hacer en un valor que aún no se ha procesado. Se trata de una abstracción para administrar interacciones con API asincrónicas.

La promesa "done" se ejecuta en cuanto la función proporcionada se ha completado satisfactoriamente o ha producido un error. A diferencia de la promesa "then", se garantiza el lanzamiento de cualquier error no identificado en la función y, después de que los controladores han terminado de ejecutarse, esta función lanza cualquier error que debería haberse devuelto después como una promesa en el estado de error. Para obtener más información, consulte [done].

			promise.done(onComplete, onError);

Como:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

La promesa "then" es igual que "done", pero a diferencia de "then", con "done" se garantiza el lanzamiento de cualquier error no identificado en la función. Si no proporciona un controlador de error a "then" y la operación experimenta un error, no se lanza una excepción, sino que, en su lugar, se devuelve una promesa en el estado de error. Para obtener más información, consulte [then].

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Como:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Puede utilizar promesas de diferentes formas. Puede encadenar operaciones de promesas con llamadas a "then" o "done" en la promesa devuelta por la función "then" anterior. Use "then" para una fase intermedia de la operación (por ejemplo ".then().then()") y "done" para la fase final de la operación (por ejemplo ".then().then().done()").  Puede encadenar varias funciones "then", porque "then" devuelve una promesa. No puede encadenar más de un método "done", porque devuelve undefined. [Obtenga más información acerca de las diferencias entre then y done].

 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a>Reproducción de de encabezados de solicitud de cliente</h2>

Puede enviar encabezados de solicitud personalizados con la función "withFilter", con la lectura y escritura de propiedades arbitrarias de la solicitud que se va a enviar dentro del filtro. Puede agregar dicho encabezado HTTP personalizado si un script del servidor lo necesita o si puede mejorarse con él.

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

Los filtros se usan para mucho más que para personalizar encabezados de solicitud. Pueden usarse para examinar o cambiar solicitudes, examinar o cambiar respuestas, derivar llamadas de redes, enviar varias llamadas, etc.

<h2><a name="hostnames"></a>Reproducción de de recursos de origen cruzado</h2>

Para controlar a qué sitios web se les permite interactuar con solicitudes y enviarlas al servicio móvil, asegúrese de que agrega el nombre de host al sitio web que usa para hospedarlo en la lista blanca de uso compartido de recursos de origen cruzado (CORS) mediante la pestaña Configure. Puede usar caracteres comodín en caso de que sea necesario. De forma predeterminada, los nuevos Servicios móviles ordenan a los exploradores que permitan el acceso solo desde "localhost" y el uso compartido de recursos entre orígenes permite que el código JavaScript se ejecute en un explorador en un nombre de host externo para interactuar con el servicio móvil.  Esta configuración no es necesaria para aplicaciones WinJS.

<h2><a name="nextsteps"></a>Pasos siguientes</h2>

Ahora que ha completado este tema de referencia conceptual, conozca cómo realizar tareas importantes en Servicios móviles de forma detallada:

* [Introducción a los Servicios móviles]
  <br/>Conozca los aspectos básicos de cómo usar Servicios móviles.

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Validación y modificación de datos con scripts]
  <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

* [Autorización de usuarios con scripts]
  <br/>Conozca cómo usar el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. -->
[Qué es Servicios móviles]: #what-is
[Conceptos]: #concepts
[Creación del cliente de Servicios móviles]: #create-client
[Consulta de datos desde un servicio móvil]: #querying
[Filtro de datos devueltos]: #filtering
[Clasificación de datos devueltos]: #sorting
[Devolución de datos en páginas]: #paging
[Selección de columnas específicas]: #selecting
[Búsqueda de datos por identificador]: #lookingup
[Visualización de datos en la interfaz de usuario]: #binding
[Inserción de datos en un servicio móvil]: #inserting
[Modificación de datos en un servicio móvil]: #modifying
[Eliminación de datos en un servicio móvil]: #deleting
[Autenticación de usuarios]: #caching
[Control de errores]: #errors
[Uso de promesas]: #promises
[Personalización de encabezados de solicitud]: #customizing
[Uso compartido de recursos entre orígenes]: #hostnames
[Pasos siguientes]: #nextsteps
[Ejecución de una operación de consulta de OData]: #odata-query



<!-- URLs. -->
[Introducción a los Servicios móviles]: /en-us/develop/mobile/tutorials/get-started-html
[SDK de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=257545
[Introducción a los datos]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-html/
[Introducción a la autenticación]: /en-us/develop/mobile/tutorials/get-started-with-users-html
[Introducción a la autenticación (Tienda Windows)]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[then]: http://msdn.microsoft.com/en-us/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/en-us/library/windows/apps/hh701079.aspx
[Obtenga más información acerca de las diferencias entre then y done]: http://msdn.microsoft.com/en-us/library/windows/apps/hh700334.aspx
[cómo controlar los errores en promesas]: http://msdn.microsoft.com/en-us/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/en-us/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/en-us/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/en-us/library/windows/apps/br211837.aspx
[Enlace de datos (aplicaciones de la Tienda Windows con JavaScript y HTML)]: http://msdn.microsoft.com/en-us/library/windows/apps/hh758311.aspx
[Guía de inicio rápido de JavaScript para la Tienda Windows]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started
[Guía de inicio rápido de HTML]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-html
[Introducción a los datos en JavaScript de Tienda Windows]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-js
[Introducción a los datos en HTML/JavaScript]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-html/
[Puede ver un ejemplo completo de cómo configurar este escenario aquí]: http://www.windowsazure.com/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js/
[Introducción a los datos]: /en-us/develop/mobile/tutorials/get-started-with-data-html
[Validación y modificación de datos con scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Limitación de consultas con paginación]: /en-us/develop/mobile/tutorials/add-paging-to-data-html
[Autorización de usuarios con scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-html
[login]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554236.aspx
[Autenticación de la aplicación con el inicio de sesión único]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[Códigos de control ASCII C0 y C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI para administrar tablas de Servicios móviles]: http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Referencia de opciones de consultas del sistema OData]: http://go.microsoft.com/fwlink/p/?LinkId=444502

<!--HONumber=35.1-->
