1.  Abra el archivo de proyecto default.js y, en la sobrecarga del método **app.OnActivated**, sustituya la última llamada al método **refreshTodoItems** por el siguiente código:

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

    Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método **login** anterior por uno de los siguientes: *microsoftaccount*, *twitter*, *google* o *windowsazureactivedirectory*.

    > [WACOM.NOTE]Si registró la información del paquete de la aplicación de la Tienda Windows en Servicios móviles, debe llamar al método [login][login] proporcionando un valor **true** para el parámetro *useSingleSignOn*. Si no realiza este procedimiento, los usuarios seguirán visualizando una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión.

2.  En el proyecto de aplicación de la Tienda Windows, abra el archivo de proyecto default.html y agregue el siguiente elemento **botón** inmediatamente antes del elemento que define el botón **guardar**:

        <button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en la aplicación con el proveedor de identidades seleccionado.

    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

  [login]: http://go.microsoft.com/fwlink/p/?LinkId=322050
