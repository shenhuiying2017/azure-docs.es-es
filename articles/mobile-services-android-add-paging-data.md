	<properties urlDisplayName="Add paging to data" pageTitle="Incorporación de la paginación a datos (Android) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="ricksal" />

# Limitación de consultas de Servicios móviles con paginación

€

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación Android desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **top** y **skip** en el cliente para solicitar "páginas" específicas de datos.

<div class="dev-callout"><b>Nota:</b>
<p>Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.</p>
</div>

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos]. Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos]. 

1. En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a los datos].

2. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar la aplicación y, a continuación, escriba texto en el cuadro de texto y haga clic en el botón **Add** (Agregar).

3. Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem. 

4. En el archivo ToDoActivity.java, reemplace el método **RefreshTodoItems** por el siguiente código:

		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

  	Esta consulta devuelve los tres elementos principales que no están marcados como completados.

5. Recompile e inicie la aplicación. 
   
    Observe que solo se muestran los tres primeros resultados de la tabla TodoItem. 

6. (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler]. 

   	Observe que el método `top(3)` se ha traducido en la opción de consulta `$top=3` en el URI de la consulta.

7. Actualice el método **RefreshTodoItems** una vez más con el código siguiente:
            
		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).skip(3).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

   	Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

    <div class="dev-callout"><b>Nota:</b>
    <p>Este tutorial usa un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos <strong>top</strong> y <strong>skip</strong>. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede llamar al método   <strong>includeInlineCount</strong> para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.</p>
    </div>

8. (Opcional) Vea de nuevo el URI de la solicitud enviada al servicio móvil. 

   	Observe que el método `skip(3)` se ha traducido en la opción de consulta `$skip=3` en el URI de la consulta.

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a la autenticación]
  <br/>Obtenga información sobre cómo autenticar usuarios de la aplicación con Cuenta de Windows.
 
* [Introducción a las notificaciones de inserción] 
  <br/>Obtenga información sobre cómo enviar a la aplicación una notificación de inserción muy básica.

<!-- Anchors. -->

[Pasos siguientes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-android
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android

[Portal de administración]: https://manage.windowsazure.com/

