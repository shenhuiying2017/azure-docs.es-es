<ol>

1.  En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs y actualice la definición de método `PostTodoItem` con el código siguiente:

         public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
         {
             TodoItem current = await InsertAsync(item);
             WindowsPushMessage message = new WindowsPushMessage();
             message.XmlPayload = @"<
         xml version=""1.0"" encoding=""utf-8""
         >" +
                                  @"<toast><visual><binding template=""ToastText01"">" +
                                  @"<text id=""1"">" + item.Text + @"</text>" +
                                  @"</binding></visual></toast>";
             try
             {
                 var result = await Services.Push.SendAsync(message);
                 Services.Log.Info(result.State.ToString());
             }
             catch (System.Exception ex)
             {
                 Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
             }
             return CreatedAtRoute("Tables", new { id = current.Id }, current);
         }

    Este código enviará una notificación de inserción (con el texto del elemento insertado) tras insertar un elemento todo. En caso de error, el código agregará una entrada al registro de errores que aparecerá en la pestaña **Registros** del servicio móvil en el Portal de administración.


