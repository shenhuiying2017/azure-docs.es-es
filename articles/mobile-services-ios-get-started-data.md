<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a los datos en Servicios móviles
=============================================

[C# para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de iOS. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

**Nota:**

Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación de iOS. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-ios).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de iOS](#download-app)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

Este tutorial requiere el [SDK de iOS para Servicios móviles](https://go.microsoft.com/fwLink/p/?LinkID=266533), [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) y iOS 5.0 o versiones posteriores.

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F).

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se basa en la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=268622), una aplicación de iOS. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de iOS de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  Descargue la [aplicación de muestra](http://go.microsoft.com/fwlink/p/?LinkId=268622) GetStartedWithData.

2.  En Xcode, abra el proyecto descargado y examine el archivo QSTodoService.m.

   	Tenga en cuenta que hay ocho comentarios **// TODO** que especifican los pasos que debe tomar para poner esta aplicación en funcionamiento con su servicio móvil.

3.  Presione el botón **Ejecutar** (o la tecla Command+R) para volver a compilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algo de texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

   	![](./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png)

   	Observe que el texto guardado se muestra en la lista a continuación.

Creación de un servicio móvilCreación de un servicio móvil en el Portal de administración
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Actualización de la aplicaciónActualización de la aplicación para usar el servicio móvil para el acceso a datos
---------------------------------------------------------------------------------------------------------------

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  Si todavía no ha instalado el [SDK de iOS para Servicios móviles](https://go.microsoft.com/fwLink/p/?LinkID=266533), hágalo ahora.

2.  En el Project Navigator en Xcode, abra los archivos TodoService.m y TodoService.h que se encuentran en la carpeta Quickstart y agregue la siguiente instrucción de importación:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h> 

3.  En el archivo ToDoService.h, localice la siguiente línea de código comentada:

        // Cree un comentario de propiedad de MSClient en la declaración de #interfaz para TodoService. 

   	Después de este comentario, agregue la siguiente línea de código:

        @property (nonatomic, strong)   MSClient *client;

   	De esta manera se crea una propiedad que representa el MSClient que se conecta al servicio.

4.  En el archivo TodoService.m, localice la siguiente línea de código comentada:

        // Cree una propiedad MSTable para sus elementos.  

   	Después de este comentario, agregue la siguiente línea de código dentro de la declaración @interfaz:

        @property (nonatomic, strong)   MSTable *table;

   	Así se crea una representación de propiedad para su tabla de servicios móviles.

5.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

6.  Haga clic en la pestańa **Panel** y tome nota de la dirección que aparece en **Site URL**; a continuación, haga clic en **Manage keys** y tome nota de la clave indicada en **Application key**.

   	![](./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png)

  	Necesitará estos valores al obtener acceso al servicio móvil desde el código de la aplicación.

1.  Nuevamente en Xcode, abra TodoService.m y localice la siguiente línea de código comentada:

         // Inicialice el cliente de Servicio móvil con su URL y clave.

    Después de este comentario, agregue la siguiente línea de código:

         self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Esto crea una instancia del cliente de Servicios móviles.

2.  Reemplace los valores **APPURL** y **APPKEY** en este código con la URL y la clave de aplicación del servicio móvil que adquirió en el paso 6.

3.  Localice la siguiente línea de código comentada:

         // Cree una instancia MSTable que nos permita trabajar con la tabla TodoItem.

    Después de este comentario, agregue la siguiente línea de código:

         self.table = [self.client tableWithName:@"TodoItem"];

    De esta manera se crea la instancia de la tabla TodoItem.

4.  Localice la siguiente línea de código comentada:

 	    // Cree un predicado que busque elementos donde complete es un comentario false en el método refreshDataOnSuccess.

    Después de este comentario, agregue la siguiente línea de código:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Así se crea una consulta para devolver todas las tareas que no se hayan completado todavía.

1.  Localice la siguiente línea de código comentada:

        // Consulte la tabla TodoItem y actualice la propiedad de los elementos con los resultados del servicio.

	Reemplace ese comentario y la invocación del bloque de **finalización** posterior por el siguiente código:

        // Consulte la tabla TodoItem y actualice la propiedad de los elementos con los resultados del servicio.
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

1.  Localice el método **addItem** y reemplace el cuerpo del método por el siguiente código:

        // Inserte el elemento en la tabla TodoItem y agréguelo a la matriz de elementos en la finalización.
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
         
            // Avise a la persona que llama de que hemos terminado.
            completion(index);
        }];

    Este código envía una solicitud de inserción al servicio móvil.

2.  Localice el método **completeItem** y reemplace el cuerpo del método por el siguiente código:

        // Actualice el elemento en la tabla TodoItem y quítelo de la matriz de elementos en la finalización.
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Obtenga un índice nuevo en caso de que la lista haya cambiado.
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Avise a la persona que llama de que hemos terminado.
            completion(index);
        }]; 

	Este código quita TodoItems después de que se marquen como completados.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

1.  En Xcode, seleccione un emulador para implementar en (iPhone o iPad), presione el botón **Ejecutar** (o la tecla Command+R) para volver a crear el proyecto e iniciar la aplicación.

   	Esta acción ejecuta su cliente de Servicios móviles de Azure, creado con el SDK de iOS, que consulta elementos desde su servicio móvil.

2.  Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestańa **Data** y, a continuación, en **Browse**.

   	![](./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png)
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

Con esto concluye el tutorial **Introducción a los datos** para iOS.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de iOS para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-ios)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, pruebe con uno de estos otros tutoriales para iOS:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.


