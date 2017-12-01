
En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio de Azure de back-end cada vez que se inicia la aplicación. Este método no es eficaz y puede tener problemas relacionados con el uso si muchos clientes intentan iniciar la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por el servicio de Azure e intentar usarlo primero antes de emplear un inicio de sesión basado en proveedores.

> [!NOTE]
> Puede almacenar en caché el token emitido por el servicio de Azure de back-end con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.
>
>

1. Abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Agregue los siguientes métodos a la clase `ToDoActivity` .

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. En el archivo ToDoActivity.java, agregue la siguiente definición para el método `cacheUserToken`.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Este método almacena el identificador de usuario y el token en un archivo de preferencias que está marcado como privado. Esto debería proteger el acceso a la memoria caché para que otras aplicaciones del dispositivo no tengan acceso al token. La preferencia es un espacio aislado de la aplicación. Sin embargo, si alguien obtiene acceso al dispositivo, es posible que pueda tener acceso a la caché del token mediante otros medios.

   > [!NOTE]
   > Puede proteger adicionalmente el token con cifrado si el acceso del token a los datos se considera sumamente sensible y alguien puede obtener acceso al dispositivo. Sin embargo, una solución completamente segura está fuera del alcance de este tutorial y depende de los requisitos de seguridad.
   >
   >

4. En el archivo ToDoActivity.java, agregue la siguiente definición para el método `loadUserTokenCache`.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. En el archivo *ToDoActivity.java*, reemplace los métodos `authenticate` y `onActivityResult` por los siguientes, que usan una caché del token. Cambie el proveedor de inicio de sesión si desea usar otra cuenta que no sea la de Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, login and create a token cache
        else
        {
            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // login succeeded
                    createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // login failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. Cree la aplicación y pruebe la autenticación mediante una cuenta válida. Ejecútela al menos dos veces. Durante la primera ejecución, debe recibir un mensaje para iniciar sesión y crear la memoria caché del token. Después de eso, cada ejecución intenta cargar la caché de tokens para la autenticación. No es necesario para iniciar sesión.
