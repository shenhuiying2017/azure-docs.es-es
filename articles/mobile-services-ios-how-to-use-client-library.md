<properties linkid="mobile-services-how-to-ios-client" urlDisplayName="iOS Client Library" pageTitle="How to use the iOS client library - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service iOS client library, iOS client library" description="Learn how to use the iOS client library for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the iOS client library for Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />




# Uso de la biblioteca del cliente iOS para Servicios móviles

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/es-es/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/es-es/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/es-es/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/es-es/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/es-es/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

Esta guía le muestra cómo realizar algunas tareas comunes a través del cliente iOS para Servicios móviles de Azure. Los ejemplos se han escrito en Objective-C y requieren el [SDK de Servicios móviles][SDK de Servicios móviles]. Este tutorial también requiere el [SDK de iOS][SDK de iOS]. Entre las tareas incluidas se encuentran la consulta, inserción, actualización y eliminación de datos, la autenticación de usuarios y la administración de errores. Si no tiene experiencia en el uso de Servicios móviles, considere primero la opción de completar la [guía de inicio rápido de Servicios móviles][guía de inicio rápido de Servicios móviles] (en inglés). El tutorial de la guía de inicio rápido le ayuda a configurar la cuenta y crear el primer servicio móvil.

## Tabla de contenido

-   [Qué es Servicios móviles][Qué es Servicios móviles]
-   [Conceptos][Conceptos]
-   [Configuración y requisitos previos][Configuración y requisitos previos]
-   [Creación del cliente de Servicios móviles][Creación del cliente de Servicios móviles]
-   [Creación de una referencia de tabla][Creación de una referencia de tabla]
-   [Consulta de datos desde un servicio móvil][Consulta de datos desde un servicio móvil]

    -   [Filtro de datos devueltos][Filtro de datos devueltos]
    -   [Uso del objeto MSQuery][Uso del objeto MSQuery]
    -   [de columnas específicas][de columnas específicas]
-   [Inserción de datos en un servicio móvil][Inserción de datos en un servicio móvil]
-   [Modificación de datos en un servicio móvil][Modificación de datos en un servicio móvil]
-   [Enlace de datos a la interfaz de usuario][Enlace de datos a la interfaz de usuario]
-   [Autenticación de usuarios][Autenticación de usuarios]
-   [Gestión de errores][Gestión de errores]

<!--- [How to: Design unit tests] - [How to: Customize the client]     - [Customize request headers]     - [Customize data type serialization] - [Next steps][Next steps]-->

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

## <a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un servicio móvil con una tabla. Para obtener más información, consulte [Creación de una tabla][Creación de una tabla] o reutilice la tabla `ToDoItem` creada en el tutorial [Introducción a los Servicios móviles][guía de inicio rápido de Servicios móviles]. En los ejemplos de este tema se usa una tabla denominada `ToDoItem`, que tiene las siguientes columnas:

-   `id`
-   `text`
-   `complete`
-   `duration`

Si va a crear la aplicación de iOS desde el principio, asegúrese de agregar `WindowsAzureMobileServices.framework` al valor [**Link Binary With Libraries**][**Link Binary With Libraries**] de la aplicación. Durante este paso, haga clic en "Agregar otro…", vaya a la ubicación del SDK de los Servicios móviles de Windows Azure descargado y seleccione esa ubicación.

Asimismo, debe agregar la siguiente referencia a los archivos adecuados o al archivo .pch de la aplicación.

    #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

## <a name="create-client"></a><span class="short-header">Creación del cliente</span>Creación del cliente de Servicios móviles

El código siguiente crea el objeto de cliente del servicio móvil que se usa para obtener acceso al servicio móvil.

    MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

En el código anterior, reemplace `MobileServiceUrl` y `AppKey` por la dirección URL y la clave de aplicación del servicio móvil. A fin de determinar estos valores para su servicio móvil, seleccione el servicio en el Portal de administración de Azure y, a continuación, haga clic en **Panel**.

También puede crear el cliente a partir de un objeto **NSURL** que sea la dirección URL del servicio, de la siguiente forma:

    MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];      

## <a name="table-reference"></a><span class="short-header">Creación de una referencia de tabla</span>Creación de una referencia de tabla

Para tener acceso a los datos del servicio móvil, debe obtener una referencia a la tabla desde la que desea consultar, actualizar o eliminar elementos. En el ejemplo siguiente, `ToDoItem` es el nombre de la tabla:

    MSTable *table = [client tableWithName:@"ToDoItem"]; 

## <a name="querying"></a><span class="short-header">Consulta de datos</span>Consulta de datos desde un servicio móvil

Una vez que disponga de un objeto MSTable, puede crear su consulta. La sencilla consulta que se muestra a continuación sirve para obtener todos los elementos de la tabla ToDoItem.

    [table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
            for(NSDictionary *item in items) {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
            }
        }
    }];

Tenga en cuenta que, en este caso, simplemente se escribe el texto de la tarea en el registro.

Los parámetros siguientes se proporcionan en la devolución de llamada:

-   *items*: Matriz **NSArray** de los registros que coinciden con la consulta.
-   *totalCount*: Recuento total de los elementos de todas las páginas de la consulta, no solo aquellos que se devuelven en la página actual. Este valor está establecido en -1, a menos que se solicite explícitamente el recuento total en la solicitud. Para obtener más información, consulte [Devolución de datos en páginas][Devolución de datos en páginas].
-   *error*: Cualquier error que se haya producido; de lo contrario, `nil`.

### <a name="filtering"></a>Filtro de datos devueltos

Si desea filtrar los resultados, tiene a su disposición diversas opciones.

La más habitual es usar un objeto NSPredicate para este fin.

    [table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

El predicado siguiente devuelve solo los elementos incompletos de la tabla ToDoItem:

    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
    [table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
            for(NSDictionary *item in items) {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
            }
        }
    }];

Un único registro puede recuperarse usando su identificador.

    [table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
        }
    }];

Tenga en cuenta que, en este caso, los parámetros de devolución de llamada son algo distintos. En lugar de obtener una matriz de resultados y un recuento opcional, solamente se obtiene el registro en cuestión.

### <a name="query-object"></a>Uso del objeto MSQuery

Use el objeto **MSQuery** en caso de requerir una consulta más compleja que no se limite al filtrado de filas, como cambiar el criterio de ordenación de los resultados o limitar el número de registros de datos que se obtiene. En los dos ejemplos siguientes se muestra cómo crear una instancia del objeto MSQuery:

    MSQuery *query = [table query]; 

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

El objeto MSQuery permite controlar los siguientes comportamientos de la consulta:

-   Especificar el orden en que se devuelven los resultados.
-   Limitar qué campos se devuelven.
-   Limitar el número de registros que se devuelven.
-   Especificar si debe incluirse el recuento total en la respuesta.
-   Especificar parámetros de la cadena de consulta personalizados en la solicitud.

Para definir aún más una consulta, pueden aplicarse una o varias funciones. Una vez definida la consulta, esta se ejecuta mediante la llamada a la función **readWithCompletion**.

#### <a name="sorting"></a>Ordenación de datos devueltos

Las siguientes funciones se usan para especificar los campos que se utilizan para la ordenación:

    -(void) orderByAscending:(NSString *)field
    -(void) orderByDescending:(NSString *)field

Esta consulta ordena los resultados en primer lugar por su duración y, después, dependiendo de si la tarea se ha finalizado:

    [query orderByAscending:@"duration"];
    [query orderByAscending:@"complete"];
    [query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        //code to parse results here
    }]; 

#### <a name="paging"></a>Devolución de datos en páginas

Servicios móviles limita la cantidad de registros que se devuelven en una sola respuesta. Para controlar el número de registros que se muestra a los usuarios, debe implementar un sistema de paginación. La paginación se obtiene mediante el uso de las tres propiedades siguientes del objeto **MSQuery**:

-   `BOOL includeTotalCount`
-   `NSInteger fetchLimit`
-   `NSInteger fetchOffset`

En el ejemplo siguiente, una sola función solicita 20 registros del servidor y, a continuación, los anexa a la colección local de registros previamente cargados:

    - (bool) loadResults() {
        MSQuery *query = [table query];

        query.includeTotalCount = YES;
        query.fetchLimit = 20;
        query.fetchOffset = self.loadedItems.count;

        [query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
            if(!error) {
                // Add the items to our local copy
                [self.loadedItems addObjectsFromArray:items];       

                // Set a flag to keep track if there are any additional records we need to load
                self.moreResults = (self.loadedItems.count < totalCount);
            }
        }];
    }

#### <a name="selecting"></a>Limitación de los campos devueltos

Para limitar los campos que devuelve la consulta, solo tiene que especificar los nombres de los campos deseados en la propiedad **selectFields**. En el ejemplo siguiente solamente se devuelven los campos de texto y aquellos que se hayan rellenado:

    query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>Especificación de parámetros adicionales de la cadena de consulta

La biblioteca de clientes hace posible incluir parámetros adicionales de la cadena de consulta en la solicitud al servidor. Es posible que los scripts del servidor requieran estos parámetros. En el ejemplo siguiente se agregan dos parámetros de la cadena de consulta a la solicitud:

    query.parameters = @{
        @"myKey1" : @"value1",
        @"myKey2" : @"value2",
    };

Estos parámetros se anexan al URI de la consulta como `myKey1=value1&myKey2=value2`.
Para obtener más información, consulte [Acceso a parámetros personalizados][Acceso a parámetros personalizados].

## <a name="inserting"></a><span class="short-header">Inserción de datos</span>Inserción de datos en un servicio móvil

Para insertar una nueva fila en la tabla, debe crear un [objeto NSDictionary][objeto NSDictionary] y pasarlo a la función de inserción. El código siguiente inserta un elemento "ToDo" nuevo en la tabla:

    NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

Servicios móviles es compatible con valores de cadena personalizados y exclusivos para el identificador de tabla. Esto permite a las aplicaciones usar valores personalizados como nombres de usuario o direcciones de correo electrónico para la columna de identificador de una tabla de Servicios móviles. Si desea identificar cada registro mediante una dirección de correo electrónico, podría usar el siguiente objeto JSON.

    NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

Si no se proporciona un valor de identificador de cadena cuando se insertan nuevos registros en una tabla, Servicios móviles generará un valor exclusivo para el identificador.

La compatibilidad de los identificadores de cadena ofrece las siguientes ventajas a los desarrolladores:

-   Los identificadores pueden generarse sin realizar un recorrido de ida y vuelta en la base de datos.
-   Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
-   Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

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

El valor `id` debe ser exclusivo y no debe incluir caracteres de los siguientes conjuntos:

-   Caracteres de control: [0x0000-0x001F] y [0x007F-0x009F]. Para obtener más información, consulte [Códigos de control ASCII C0 y C1][Códigos de control ASCII C0 y C1].
-   Caracteres imprimibles: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Los identificadores "." y ".."

También puede usar identificadores de números enteros para las tablas. Para usar un identificador de números enteros, debe crear la tabla con el comando `mobile table create` usando la opción `--integerId`. Este comando se usa con la interfaz de la línea de comandos (CLI) de Azure. Para obtener más información sobre el uso de la CLI, consulte [CLI para administrar tablas de Servicios móviles][CLI para administrar tablas de Servicios móviles].

Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en los campos del objeto de la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico][Esquema dinámico].

## <a name="modifying"></a><span class="short-header">Modificación de datos</span>Modificación de datos en un servicio móvil

Para actualizar un objeto existente, modifique un elemento devuelto de una consulta anterior y, a continuación, llame a la función **update**.

    NSMutableDictionary *item = [self.results.item objectAtIndex:0];
    [item setObject:@YES forKey:@"complete"];
    [table update:item completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

Al realizar una actualización, solo tiene que proporcionar el campo que se va a actualizar junto con el identificador de fila, como en el ejemplo siguiente:

    [table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @YES} completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

Para eliminar un elemento de la tabla, solo tiene que pasar el elemento al método de eliminación, de la siguiente forma:

    [table delete:item completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }];

También puede eliminar un registro usando su identificador directamente, como en el ejemplo siguiente:

    [table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }]; 

Tenga en cuenta que, en caso de actualizar y eliminar, debe establecerse como mínimo el atributo `id`.

## <a name="authentication"></a><span class="short-header">Autenticación</span>Autenticación de usuarios

Servicios móviles permite usar los siguientes proveedores de identidades para autenticar a los usuarios:

-   Facebook
-   Google
-   Cuenta Microsoft
-   Twitter
-   Azure Active Directory

Para obtener más información acerca de cómo configurar un proveedor de identidades, consulte [Introducción a la autenticación][Introducción a la autenticación].

Servicios móviles admite los dos flujos de trabajo de autenticación siguientes:

-   En un inicio de sesión administrado por el servidor, Servicios móviles administra el proceso de inicio de sesión en nombre de su aplicación. La biblioteca de clientes muestra una página de inicio de sesión específica del proveedor y Servicios móviles realiza la autenticación con el proveedor elegido.

-   En un inicio de sesión administrado por el cliente, la aplicación debe solicitar un token al proveedor de identidades y, después, presentar dicho token a Servicios móviles para su autenticación.

Una vez realizada la autenticación, se devuelve un objeto de usuario que contiene el valor de identificador de usuario asignado y el token de autenticación. Este identificador de usuario se puede utilizar en los scripts del servidor para validar o modificar solicitudes. Para obtener más información, consulte [Uso de scripts para autorizar usuarios][Uso de scripts para autorizar usuarios]. El token en sí puede almacenarse en la memoria caché de forma segura para usarlo en inicios de sesión posteriores.

También puede establecer permisos en las tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. Para obtener más información, consulte [Permisos][Permisos].

### Inicio de sesión administrado por el servidor

A continuación se muestra un ejemplo de cómo iniciar sesión con una cuenta Microsoft. Puede llamarse a este código desde el objeto ViewDidLoad del controlador o bien desencadenarse manualmente a partir de un botón UIButton. De esta manera, se mostrará una interfaz de usuario estándar para iniciar sesión en el proveedor de identidades.

    [client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
        completion:^(MSUser *user, NSError *error) {
            NSString *msg;
            if(error) {
                msg = [@"An error occured: " stringByAppendingString:error.description];
            } else {
                msg = [@"You are logged in as " stringByAppendingString:user.userId];
            }
        
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                  message:msg 
                                  delegate:nil 
                                  cancelButtonTitle:@"OK" 
                                  otherButtonTitles: nil];
            [alert show];
    }]; 

Nota: si usa un proveedor de identidades distinto al de la cuenta Microsoft, cambie el valor que ha pasado al método de inicio de sesión anterior por uno de los siguientes: `facebook`, `twitter`, `google` o `windowsazureactivedirectory`.

También puede obtener una referencia a MSLoginController y mostrarla mediante:

    -(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### Inicio de sesión administrado por el cliente (inicio de sesión único)

En algunos casos, el inicio de sesión se realiza como proceso externo al cliente de Servicios móviles. Esto puede hacerse para habilitar la funcionalidad de inicio de sesión único o cuando la aplicación debe ponerse en contacto con el proveedor de identidades directamente para obtener información del usuario. En estos casos, para iniciar sesión en Servicios móviles, proporcione un token que haya obtenido con independencia del proveedor de identidades admitido.

En el ejemplo siguiente se usa el [SDK de Live Connect][SDK de Live Connect] a fin de habilitar el inicio de sesión único para las aplicaciones iOS.

    [client loginWithProvider:@"microsoftaccount" 
        token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
        completion:^(MSUser *user, NSError *error) {
            self.loggedInUser = user;
            NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                message:msg 
                delegate:nil 
                cancelButtonTitle:@"OK" 
                otherButtonTitles: nil];
            [alert show];
    }];

En este código se asume que ya se ha creado previamente una instancia de **LiveConnectClient** denominada `liveClient` en el controlador y que el usuario ha iniciado sesión.

### <a name="caching-tokens"></a>Almacenamiento de tokens de autenticación en la memoria caché

Para evitar que los usuarios tengan que autenticarse cada vez que ejecuten la aplicación, puede almacenar la identidad del usuario actual en caché una vez que haya iniciado sesión. Esa información podrá utilizarse posteriormente para crear el usuario directamente y omitir el proceso de inicio de sesión. Para ello, debe almacenar el identificador de usuario y el token de autenticación localmente. En el ejemplo siguiente, el token se almacena en caché de forma segura en [KeyChain]:

    - (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
    {
        NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
        [query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
        [query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];
        
        if(isSearch) {
            // Use the proper search constants, return only the attributes of the first match.
            [query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
        }

        return query;
    }

    - (IBAction)loginUser:(id)sender {
        NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
        CFDictionaryRef cfresult;

        OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
        if (status == noErr) {
            NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;
            
            //create an MSUser object
            MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
            NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
            user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
            [self.todoService.client setCurrentUser:user];
            
        } else if (status == errSecItemNotFound) {
            //we need to log the user in
            [self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
                completion:^(MSUser *user, NSError *error) {
                    NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
                    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                            message:msg delegate:nil 
                                            cancelButtonTitle:@"OK" 
                                            otherButtonTitles: nil];
                    [alert show];
                
                    //save the user id and token to the KeyChain
                    NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client 
                                                            andIsSearch:NO];
                    [newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
                    [newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding] 
                                                    forKey:(__bridge id)kSecValueData];
                 
                    OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
                    if(status != errSecSuccess) {
                        //handle error as needed
                        NSAssert(NO, @"Error caching password.");
                    }
            }];
        }   

<div class="dev-callout"><strong>Nota:</strong>
<p>Los tokens son datos confidenciales y deben almacenarse cifrados por si el dispositivo se pierde o se roba.</p>
</div>

Cuando se usa un token almacenado en caché, el usuario no deberá iniciar sesión de nuevo hasta que el token expire. Si un usuario intenta iniciar sesión con un token que haya expirado, se devuelve una respuesta 401 de uso no autorizado. En este punto, el usuario deberá volver a iniciar sesión para obtener un nuevo token, que podrá almacenarse de nuevo en la memoria caché. Puede usar los filtros a fin de evitar tener que escribir código para administrar los tokens expirados siempre que la aplicación llame al servicio móvil. Los filtros permiten interceptar las llamadas al servicio móvil, así como las respuestas del mismo. El código del filtro comprueba la respuesta para detectar un posible error 401, desencadena el proceso de inicio de sesión en caso de que el token haya expirado y, a continuación, prueba de nuevo la solicitud que generó dicho error. Para obtener los detalles, consulte [Administración de tokens expirados][Administración de tokens expirados].

## <a name="errors"></a><span class="short-header">Gestión de errores</span>Gestión de errores

Al realizar una llamada al servicio móvil, el bloque de finalización contiene un parámetro `NSError *error`. En caso de producirse un error, se devuelve un valor distinto de NULL para este parámetro. En su código, debe marcar este parámetro y administrar el error según sea necesario.

En caso de haberse producido un error, incluya el archivo MSError.h en el código si desea obtener más información:

    #import <WindowsAzureMobileServices/MSError.h>

Este archivo define las siguientes constantes, que se pueden usar para obtener acceso a datos adicionales de `[error userInfo]`:

-   **MSErrorResponseKey**: Datos de respuesta HTTP asociados al error.
-   **MSErrorRequestKey**: Datos de la solicitud HTTP asociada al error.

Además, se define una constante para cada código de error. En el archivo MSError.h puede encontrarse una explicación de estos códigos.

Para obtener un ejemplo sobre cómo realizar y controlar una validación, consulte [Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor][Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor]. En este tema, la validación del servidor se implementa mediante scripts del servidor. Al enviar datos no válidos, se devuelve una respuesta de error que administrará el cliente.

<!--
<h2><a name="#unit-testing"></a><span class="short-header">Designing tests</span>How to: Design unit tests</h2>

_(Optional) This section shows how to write unit test when using the client library (info from Yavor)._

<h2><a name="#customizing"></a><span class="short-header">Customizing the client</span>How to: Customize the client</h2>

_(Optional) This section shows how to send customize client behaviors._

###<a name="custom-headers"></a>How to: Customize request headers

_(Optional) This section shows how to send custom request headers._

For more information see, New topic about processing headers in the server-side.

###<a name="custom-serialization"></a>How to: Customize serialization

_(Optional) This section shows how to use attributes to customize how data types are serialized._

For more information see, New topic about processing headers in the server-side.

## <a name="next-steps"></a>Next steps
--> 

 

 



  [SDK de Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [SDK de iOS]: https://developer.apple.com/xcode
  [guía de inicio rápido de Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
  [Qué es Servicios móviles]: #what-is
  [Conceptos]: #concepts
  [Configuración y requisitos previos]: #Setup
  [Creación del cliente de Servicios móviles]: #create-client
  [Creación de una referencia de tabla]: #table-reference
  [Consulta de datos desde un servicio móvil]: #querying
  [Filtro de datos devueltos]: #filtering
  [Uso del objeto MSQuery]: #query-object
  [de columnas específicas]: #selecting
  [Inserción de datos en un servicio móvil]: #inserting
  [Modificación de datos en un servicio móvil]: #modifying
  [Enlace de datos a la interfaz de usuario]: #binding
  [Autenticación de usuarios]: #authentication
  [Gestión de errores]: #errors
  [Creación de una tabla]: http://msdn.microsoft.com/es-es/library/windowsazure/jj193162.aspx
  [**Link Binary With Libraries**]: https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html
  [Devolución de datos en páginas]: #paging
  [Acceso a parámetros personalizados]: /es-es/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
  [objeto NSDictionary]: http://go.microsoft.com/fwlink/p/?LinkId=301965
  [Códigos de control ASCII C0 y C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
  [CLI para administrar tablas de Servicios móviles]: http://www.windowsazure.com/es-es/manage/linux/other-resources/command-line-tools/#Mobile_Tables
  [Esquema dinámico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
  [Uso de scripts para autorizar usuarios]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [Permisos]: http://msdn.microsoft.com/es-es/library/windowsazure/jj193161.aspx
  [SDK de Live Connect]: http://go.microsoft.com/fwlink/p/?LinkId=301960
  [Administración de tokens expirados]: http://go.microsoft.com/fwlink/p/?LinkId=301955
  [Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-ios
