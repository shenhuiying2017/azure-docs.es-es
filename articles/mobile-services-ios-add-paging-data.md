<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Limitación de consultas de Servicios móviles con paginación
===========================================================

> [AZURE.SELECTOR-LIST (Plataforma | Back-end)]
>  - [(C\# para Tienda Windows | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
>  - [(C\# para Tienda Windows | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md) 
>  - [(JavaScript para Tienda Windows | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md) 
>  - [(JavaScript para Tienda Windows | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md) 
>  - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data) 
>  - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data) 
>  - [(iOS | JavaScript)](mobile-services-ios-add-paging-data) 
>  - [(Android | JavaScript)](mobile-services-android-add-paging-data) 
>  - [(HTML | .NET)](mobile-services-html-add-paging-data) 
>  - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
>  - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación iOS desde Servicios móviles de Azure. En este tutorial se utilizarán las propiedades de consulta **fetchLimit** y **fetchOffset** en el cliente para solicitar "páginas" específicas de datos.

**Nota:**

Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-ios). Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

1.  En Xcode, abra el proyecto que ha modificado al completar el tutorial [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

2.  Presione el botón **Ejecutar** (Comando + R) para crear el proyecto e iniciar la aplicación y, a continuación, escriba algún texto en el cuadro de texto y haga clic en el icono más (**+**).

3.  Repita los pasos anteriores al menos tres veces para que pueda tener más de tres elementos almacenados en la tabla TodoItem.

4.  Abra el archivo QSTodoService.m y ubique el siguiente método:

		- (void)refreshDataOnSuccess:(QSCompletionBlock)completion

  Reemplazar el cuerpo de todo el método por el código siguiente. 

		// Crear un predicado que encuentre elementos activos en los que la acción de completar sea falsa
		NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
		
		// Recuperar la instancia MSQueary de MSTable con el predicado que acaba de crear.
		MSQuery * query = [self.table queryWithPredicate:predicate];
		    
		query.includeTotalCount = YES; // Solicitar el recuento total de elementos
		
		// Comenzar con el primer elemento y recuperar solo tres
		query.fetchOffset = 0;
		query.fetchLimit = 3;
		
		// Invocar directamente la instancia MSQuery, en lugar de usar los métodos auxiliares MSTable.
		[query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {
		        
		    [self logErrorIfNotNil:error];
		    if (!error)
		    {
		        // Registrar recuento total.
		        NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
		    }
		   
		    items = [results mutableCopy];
		   
		    // Avise a la persona que llama de que hemos terminado.
		    completion();
		}];
		
	Esta consulta devuelve los tres elementos principales que no están marcados como completados. 

5.  Recompile e inicie la aplicación.

    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

6.  Actualice el método **refreshDataOnSuccess** una vez más ubicando la siguiente línea de código:

         query.fetchOffset = 0;

  Esta vez, definir el valor de **query.fetchOffset** en 3. 

  Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    **Nota:**

    Este tutorial usa un escenario simplificado para definir valores de paginación codificados de forma rígida para las propiedades **fetchOffset** y **fetchLimit**. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede definir **query.includeTotalCount = YES** para obtener el recuento total de todos los elementos disponibles en el servidor, junto con los datos paginados.

Pasos siguientes
----------------

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en el siguiente tema de Servicios móviles:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

