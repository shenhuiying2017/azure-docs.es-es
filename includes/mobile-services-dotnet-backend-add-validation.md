Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En esta sección, agregará código al servicio móvil que valida la longitud de los datos de la cadena enviados al servicio móvil y rechaza las cadenas que son demasiado largas, en este caso, con más de 10 caracteres.

1.  Inicie Visual Studio con la opción **Ejecutar como administrador** y abra la solución que contiene el proyecto de servicio móvil con el que trabajó en el tutorial de [Introducción][Introducción] o de [Introducción a los datos][Introducción a los datos].

2.  En la ventana del Explorador de soluciones, expanda el proyecto de servicio de la lista todo y expanda **Controllers**. Abra el archivo TodoItemController.cs que forma parte del proyecto de servicios móviles.

    ![][0]

3.  Reemplace el método `PostTodoItem` por el siguiente método que valida que la cadena de texto no tiene más de 10 caracteres. En el caso de los elementos que sí tienen una longitud de texto de más de 10 caracteres, el método devuelve un código de estado HTTP 400 de solicitud incorrecta con un mensaje descriptivo incluido como contenido.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }

4.  Haga clic con el botón secundario en el proyecto de servicio y haga clic en **Build** para compilar el proyecto de servicio móvil. Compruebe que no haya errores.

    ![][1]

5.  Haga clic con el botón secundario en el proyecto de servicio y haga clic en **Publicar**. Publique el servicio móvil en su cuenta de Microsoft Azure usando la configuración de publicación que utilizó anteriormente en el tutorial [Introducción][Introducción] o [Introducción a los datos][Introducción a los datos].

    > [WACOM.NOTE] Puede probar alternativamente el servicio hospedado localmente en IIS Express. Para obtener más información, consulte el tutorial [Introducción a los datos][Introducción a los datos].

    ![][2]



  [Introducción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [0]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png
  [1]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png
  [2]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png
