

1.  En el Explorador de Soluciones de Visual Studio, haga clic con el botón secundario en la carpeta Controladores para el proyecto del servicio móvil, expanda **Add** y, a continuación, haga clic en **New Scaffolded Item**.

    Se mostrará el cuadro de diálogo Add Scaffold.

2.  Expanda **Servicios móviles de Azure** y haga clic en **Azure Mobile Services Custom Controller** y, a continuación, haga clic en **Add**, proporcione un **nombre de controlador** de `CompleteAllController` y haga clic en **Add** de nuevo.

    ![](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

    De esta forma, se crea una nueva clase de controlador vacía llamada **CompleteAllController**.

	> [WACOM.NOTE]Si el cuadro de diálogo no dispone de scaffolding específico de Servicios móviles, cree una nueva clase para **Web API Controller - Empty**. En esta nueva clase de controlador, agregue una propiedad **Services** pública, que devuelve el tipo **ApiServices**. Esta propiedad se usa para obtener acceso a la configuración específica del servidor desde dentro del controlador.

1.  En el nuevo archivo de proyecto CompleteAllController.cs, agregue las siguientes instrucciones **using**:

         using System.Threading.Tasks;
         using todolistService.Models;

    En el código anterior, reemplace `todolistService` por el espacio de nombres del proyecto de servicio móvil, que debe agregarse al nombre de servicio móvil con `Service`.

2.  Agregue el siguiente código al nuevo controlador:

         // POST api/completeall        
         public Task<int> Post()
         {
             using (todolistContext context = new todolistContext())
             {
                 // Obtener la base de datos a partir del contexto.
                 var database = context.Database;

                 // Crear una instrucción SQL que establezca todos los elementos sin completar
                 // para completar y ejecutar la instrucción de manera asincrónica.
                 var sql = @"UPDATE TodoItems SET Complete = 1 " +
                             @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";
                 var result = database.ExecuteSqlCommandAsync(sql);

                 // Registrar el resultado.
                 Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                     result.ToString()));
                    
                 return result;
             }
         }

    En el código anterior, reemplace `todolistContext` por el nombre de DbContext del modelo de datos, que debe ser el nombre del servicio móvil agregado con `Context`. Este código usa la [clase Database](http://msdn.microsoft.com/en-us/library/system.data.entity.database(v=vs.113).aspx) para obtener acceso a la tabla **TodoItems** directamente a fin de establecer la marca de completado en todos los elementos. Este método es compatible con una solicitud POST y el número de filas cambiadas se devuelve al cliente como un valor entero.

    > [WACOM.NOTE] Los permisos predeterminados están establecidos, lo que significa que cualquier usuario de la aplicación puede llamar a la API personalizada. No obstante, la clave de la aplicación no se distribuye ni almacena de forma segura y no se puede considerar una credencial segura. Por ello, debe considerar restringir el acceso solo a los usuarios autenticados en las operaciones que modifican datos o afectan al servicio móvil.

A continuación, podrá modificar la aplicación de inicio rápido para agregar un botón y código nuevos que llame de forma asincrónica a la nueva API personalizada.

