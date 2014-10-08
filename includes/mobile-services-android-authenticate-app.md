1.  En el Explorador de paquetes de Eclipse, abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Agregue el método siguiente a la clase **ToDoActivity**:

        private void authenticate() {

            // Login using the Google provider.
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

    <div class="dev-callout"><b>Nota:</b>
<p>Si usa un proveedor de identidades que no sea Google, cambie el valor que ha pasado al m&eacute;todo <strong>login</strong> anterior por uno de los siguientes: <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em> o <em>windowsazureactivedirectory</em>.</p>
</div>

3.  En el método **onCreate**, agregue la línea de código siguiente después del código que crea instancias en el objeto `MobileServiceClient`.

        authenticate();

    De este modo se llama al proceso de autenticación.

4.  Mueva el código restante situado detrás de `authenticate();` del método **onCreate** a un nuevo método **createTable** cuyo aspecto es el siguiente:

        private void createTable() {

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }

5.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido.

    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.


