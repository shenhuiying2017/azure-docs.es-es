1. En el **Explorador de soluciones** de Visual Studio, expanda la carpeta **Controladores** en el proyecto de back-end móvil. Abra **TodoItemController.cs**. Al principio del archivo, agregue las siguientes instrucciones `using`:

        using System.Collections.Generic;        
        using Microsoft.Azure.Mobile.Server.Notifications;


2. Reemplace el método `PostTodoItem` por el código siguiente:
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };

            HttpConfiguration config = this.Configuration;

            GooglePushMessage message = new GooglePushMessage(data, System.TimeSpan.FromHours(1));

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                ServiceSettingsDictionary settings = config.GetServiceSettingsProvider().GetServiceSettings();
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=August15_HO8-->