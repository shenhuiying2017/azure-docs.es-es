Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  Si todavía no tiene el [SDK de Android para Servicios móviles][SDK de Android para Servicios móviles], descárguelo ya y expanda los archivos comprimidos.

2.  Copie los archivos `.jar` de la carpeta `mobileservices` del SDK en la carpeta `libs` del proyecto GetStartedWithData.

3.  En explorador de paquetes de Eclipse, haga clic con el botón secundario en la carpeta `libs`, haga clic en **Actualizar** y aparecerán los archivos jar copiados.

    De este modo, se agrega la referencia del SDK de los Servicios móviles al área de trabajo.

4.  Abra el archivo AndroidManifest.xml y agregue la línea siguiente, que permite a la aplicación obtener acceso a los Servicios móviles de Azure.

        <uses-permission android:name="android.permission.INTERNET" />

5.  En el explorador de paquetes, abra el archivo TodoActivity.java ubicado en el paquete com.example.getstartedwithdata y quite la marca de comentario de las siguientes líneas de código:

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  Eliminaremos la lista de la memoria que utiliza actualmente la aplicación, para que podamos reemplazarla por un servicio móvil. En la clase **ToDoActivity**, convierta en comentario la línea de código siguiente que define la lista **toDoItemList** existente.

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  Guarde el archivo y el proyecto indicará los errores de la compilación. Busque las tres ubicaciones restantes en las que se usa la variable `toDoItemList` y convierta en comentarios las secciones indicadas. Quite también `import java.util.ArrayList`. De este modo se elimina por completo la lista de la memoria.

8.  Ahora agregamos nuestros servicios móviles. Quite la marca de comentario de las líneas de código siguientes:

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  Busque la clase ProgressFilter en la parte inferior del archivo y quite la marca de comentario. Esta clase muestra un indicador 'loading' mientras MobileServiceClient está ejecutando operaciones de red.

10. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

11. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

    ![][0]

    Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

12. En el método **onCreate**, quite la marca de comentario de las líneas de código siguientes que definen la variable **MobileServiceClient**:

        try {
        // Create the Mobile Service Client instance, using the provided
        // Mobile Service URL and key
            mClient = new MobileServiceClient(
                    "MobileServiceUrl",
                    "AppKey", 
                    this).withFilter(new ProgressFilter());

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
        } catch (MalformedURLException e) {
            createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
        }

    De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil. Además, se crea la instancia de MobileServiceTable que se usa para el almacenamiento de datos de proxy en el servicio móvil.

13. En el código de arriba, reemplace `MobileServiceUrl` y `AppKey` por la URL y la clave de la aplicación de su servicio móvil, por ese orden.

14. Quite la marca de comentario de estas líneas del método **checkItem**:

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    De este modo se envía una actualización del elemento al servicio móvil y se eliminan los elementos marcados del adaptador.

15. Quite la marca de comentario de estas líneas del método **addItem**:

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    Este código crea un elemento y lo inserta en la tabla del servicio móvil remoto.

16. Quite la marca de comentario de estas líneas del método **refreshItemsFromTable**:

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    De este modo se consulta el servicio móvil y se devuelven todos los elementos que no se hayan marcado como completos. Los elementos se agregan al adaptador para enlace.



  [SDK de Android para Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/download-android-sample-code/mobile-dashboard-tab.png
