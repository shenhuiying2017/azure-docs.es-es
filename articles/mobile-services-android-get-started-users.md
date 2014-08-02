<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

[Tienda Windows C#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Tienda Windows C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

[Ver el tutorial (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) [Reproducir vídeo (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services)10:42

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-android).

Para completar este tutorial necesita Eclipse y Android 4.2 o versiones posteriores.

Registro de la aplicaciónRegistro de la aplicación para la autenticación y configuración de Servicios móviles
-------------------------------------------------------------------------------------------------------------

Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

   	![](./media/mobile-services-android-get-started-users/mobile-services-selection.png)

2.  Haga clic en la pestańa **Panel** y anote el valor de **Site URL**.

   	![](./media/mobile-services-android-get-started-users/mobile-service-uri.png)

    Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3.  Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para registrar la aplicación con ese proveedor:
	-   [Cuenta Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Inicio de sesión en Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Inicio de sesión en Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Inicio de sesión en Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

	    Recuerde anotar los valores secretos y la identidad del cliente que genera el proveedor.

	    **Nota de seguridad**

	    El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

1.  Vuelva al Portal de administración, haga clic en la pestańa **Identity**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Save**.

   	![](./media/mobile-services-android-get-started-users/mobile-identity-tab.png)

El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

Restricción de permisosRestricción de permisos a usuarios autenticados
----------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestańa **Data** y, a continuación, en la tabla **TodoItem**.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png)

2.  Haga clic en la pestańa **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png)

3.  En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-android).

4.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación y, a continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

Incorporación de autenticaciónIncorporación de autenticación a la aplicación
----------------------------------------------------------------------------

1.  En el Explorador de paquetes de Eclipse, abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación.

         import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
         import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
         import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Agregue el método siguiente a la clase **ToDoActivity**:

         private void authenticate() {
            
             // Inicie sesión usando el proveedor de Google.
             mClient.login(MobileServiceAuthenticationProvider.Google,
                     new UserAuthenticationCallback() {

                         @Override
                         public void onCompleted(MobileServiceUser user,
                                 Exception exception, ServiceFilterResponse response) {

                             if (exception == null) {
                                 createAndShowDialog(String.format(
                                                 "You are now logged in - %1$2s",
                                                 user.getUserId()), "Success");
                                 createTable();
                             } else {
                                 createAndShowDialog("You must log in. Login Required", "Error");
                             }
                         }
                     });
         }

    De este modo se crea un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Google. Aparecerá un diálogo que muestra el identificador del usuario autenticado. No puede continuar sin una autenticación positiva.

    **Nota:**

    Si usa un proveedor de identidades que no sea Google, cambie el valor que ha pasado al método **login** anterior por uno de los siguientes: *MicrosoftAccount*, *Facebook* o *Twitter*.

3.  En el método **onCreate**, agregue la línea de código siguiente después del código que crea instancias en el objeto `MobileServiceClient`.

         authenticate();

    De este modo se llama al proceso de autenticación.

4.  Mueva el código restante situado detrás de `authenticate();` del método **onCreate** a un nuevo método **createTable** cuyo aspecto es el siguiente:

         private void createTable() {

             // Obtenga la instancia de la tabla de Servicios móviles que haya que usar.
             mToDoTable = mClient.getTable(ToDoItem.class);

             mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

             // Cree un adaptador para enlazar los elementos con la vista.
             mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
             ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
             listViewToDo.setAdapter(mAdapter);

             // Cargue los elementos desde el servicio móvil.
             refreshItemsFromTable();
         }

5.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido.

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

Pasos siguientes
----------------

En el siguiente tutorial, [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android), usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.

