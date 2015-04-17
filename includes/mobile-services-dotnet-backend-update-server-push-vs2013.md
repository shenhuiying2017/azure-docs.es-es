Con estos pasos se crea un nuevo [ApiController][ApiController] personalizado que envía notificaciones push a la aplicación. Puede implementar este mismo código en un [TableController][TableController] o en cualquier sitio de sus servicios back-end.

1.  En el Explorador de Soluciones de Visual Studio, haga clic con el botón secundario en la carpeta Controladores para el proyecto del servicio móvil, expanda **Add** y, a continuación, haga clic en **New Scaffolded Item**.

    Se mostrará el cuadro de diálogo Add Scaffold.

2.  Expanda **Servicios móviles de Azure** y haga clic en **Controlador personalizado de Servicios móviles de Azure**; luego haga clic en **Agregar**, indique un **nombre de controlador** de `NotifyAllUsersController` y haga clic en **Agregar** de nuevo.

    ![Cuadro de diálogo de Agregar Scaffold de la API web][Cuadro de diálogo de Agregar Scaffold de la API web]

    De esta forma, se crea una nueva clase de controlador vacía llamada **NotifyAllUsersController**.

3.  En el nuevo archivo de proyecto NotifyAllUsersController.cs, agregue las siguientes instrucciones **using**:

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4.  Agregue el código siguiente que implementa el método POST en el controlador:

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
                // that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<toast><visual><binding template=\"ToastText01\">" + 
                    "<text id=\"1\">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

    > [WACOM.NOTE]Se puede llamar a este método POST con cualquier cliente que tenga la clave de la aplicación, que no es segura. Si quiere asegurar el extremo, aplique el atributo `[AuthorizeLevel(AuthorizationLevel.User)]` al método o la clase para requerir autenticación.

  [ApiController]: http://go.microsoft.com/fwlink/p/?LinkId=512673
  [TableController]: http://msdn.microsoft.com/es-es/library/azure/dn643359.aspx
  [Cuadro de diálogo de Agregar Scaffold de la API web]: ./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png
