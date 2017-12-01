
1. Abra el proyecto en Android Studio.

2. En el **Explorador de proyectos** de Android Studio, abra el archivo `ToDoActivity.java` y agregue las siguientes instrucciones de importación:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Agregue el método siguiente a la clase **TodoActivity** :

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Login using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
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

    Con este código, se crea un método para administrar el proceso de autenticación de Google. Aparece un cuadro de diálogo que muestra el identificador del usuario autenticado. Solo puede continuar si la autenticación es correcta.

    > [!NOTE]
    > Si usa un proveedor de identidades que no sea Google, cambie el valor pasado al método **login** a uno de los siguientes: _MicrosoftAccount_, _Facebook_, _Twitter_ o _windowsazureactivedirectory_.

4. En el método **onCreate**, agregue la siguiente línea de código después del código que crea una instancia del objeto `MobileServiceClient`.

    ```java
    authenticate();
    ```

    De este modo se llama al proceso de autenticación.

5. Mueva el código restante situado después de `authenticate();` en el método **onCreate** a un nuevo método **createTable**:

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Para asegurarse de que el redireccionamiento funcione, agregue el siguiente fragmento de código de `RedirectUrlActivity` a `AndroidManifest.xml`:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Agregue `redirectUriScheme` a `build.gradle` de la aplicación Android.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Agregue `com.android.support:customtabs:23.0.1` a las dependencias de `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. En el menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido.

> [!WARNING]
> El esquema de dirección URL mencionado distingue mayúsculas de minúsculas. Asegúrese de que todas las apariciones de `{url_scheme_of_you_app}` usen las mayúsculas y minúsculas del mismo modo.

Si ha iniciado sesión correctamente, la aplicación se ejecutará sin errores y deberá poder consultar el servicio back-end y realizar actualizaciones en los datos.
