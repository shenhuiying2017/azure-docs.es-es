<properties urlDisplayName="" pageTitle="Incorporación de paginación a datos (Xamarin iOS) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación Xamarin iOS desde Servicios móviles." metaCanonical="" services="mobile-services" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />


# Limitación de consultas de Servicios móviles con paginación

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación iOS desde Servicios móviles de Azure. En este tutorial se utilizarán las propiedades de consulta **Skip** y **Take** en el cliente para solicitar "páginas" específicas de datos.

<div class="dev-callout"><b>Nota:</b>
<p>Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.</p>
</div>

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos]. Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos]. 

1. En Xamarin Studio, abra el proyecto que ha modificado cuando realizó el tutorial [Introducción a los datos].

2. Presione el botón **Run** (Ejecutar) para generar el proyecto e iniciar la aplicación y, a continuación, especifique el texto en el cuadro de texto y haga clic en el icono con el signo más (**+**).

3. Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem. 

4. Abra el archivo **TodoService.cs**, busque el método **RefreshDataAsync** y sustituya la consulta LINQ en <code>todoTable</code> por la siguiente consulta: 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	Esta consulta devuelve los tres elementos principales que no están marcados como completados. 

5. Recompile e inicie la aplicación. 
   
    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem. 

7. Actualice la consulta LINQ en el método **RefreshDataAsync** una vez más con lo siguiente:

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	Esta vez, establezca el valor **Skip** en 3. 

Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    <div class="dev-callout"><b>Nota:</b>
    <p>Este tutorial usa un escenario simplificado para definir valores de paginación codificados de forma rígida para las propiedades <strong>Skip</strong> y <strong>Take</strong>. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede llamar al método <strong>IncludeTotalCount</strong> para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.</p>
    </div>

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en el siguiente tema de Servicios móviles:

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con la cuenta de Windows.
 
<!--
* [Get started with push notifications] 
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.
-->

<!-- Anchors. -->

[Pasos siguientes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introducción a Servicios móviles]: ./es-es/develop/mobile/tutorials/get-started-xamarin-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portal de administración]: https://manage.windowsazure.com/
