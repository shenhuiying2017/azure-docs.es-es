<properties 
	pageTitle="Incorporación de paginación a datos (iOS) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo utilizar la paginación para administrar la cantidad de datos devueltos a su aplicación iOS desde Servicios móviles." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Limitación de consultas de Servicios móviles con paginación

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación iOS desde Servicios móviles de Azure. En este tutorial se utilizarán las propiedades de consulta **fetchLimit** y **fetchOffset** en el cliente para solicitar "páginas" específicas de datos.

> [AZURE.NOTE] Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos]. Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos].

1. En Xcode, abra el proyecto que ha modificado al completar el tutorial [Introducción a los datos].

2. Presione el botón **Run** (Ejecutar) (Comando + R) para crear el proyecto e iniciar la aplicación y, a continuación, escriba algún texto en el cuadro de texto y haga clic en el icono más (**+**).

3. Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem.

4. Abra el archivo QSTodoService.m y ubique el siguiente método:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

   	Reemplazar el cuerpo de todo el método por el código siguiente.

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	
        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];

        query.includeTotalCount = YES; // Request the total item count

        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;

        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {

            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);
            }

            items = [results mutableCopy];

            // Let the caller know that we finished
            completion();
        }];

   	Esta consulta devuelve los tres elementos principales que no están marcados como completados.

5. Recompile e inicie la aplicación.

    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.

7. Actualice el método **refreshDataOnSuccess** una vez más ubicando la siguiente línea de código:

        query.fetchOffset = 0;

   	Esta vez, establezca el valor **query.fetchOffset** en 3.

   	Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    > [AZURE.NOTE] Este tutorial usa un escenario simplificado estableciendo valores de paginación codificados de forma rígida para las propiedades **fetchOffset** y **fetchLimit**. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede definir **query.includeTotalCount = YES** para obtener el recuento total de todos los elementos disponibles en el servidor, junto con los datos paginados.

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en el siguiente tema de Servicios móviles:

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

<!--
* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.
-->

<!-- Anchors. -->

[Pasos siguientes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios

[Portal de administración]: https://manage.windowsazure.com/



<!--HONumber=42-->
