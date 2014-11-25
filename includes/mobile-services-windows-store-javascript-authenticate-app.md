1.  Abra el archivo de proyecto default.js y, en la sobrecarga del método **app.OnActivated**, sustituya la última llamada al método **refreshTodoItems** por el siguiente código:

        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }            

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    De esta forma se crea una variable de miembro para el almacenamiento del usuario actual y un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook. Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método **login** anterior por uno de los siguientes: *microsoftaccount*, *twitter*, *google* o *windowsazureactivedirectory*.

    > [WACOM.NOTE]Si registró la información del paquete de la aplicación de la Tienda Windows en Servicios móviles, debe llamar al método [login][login] proporcionando un valor **true** para el parámetro *useSingleSignOn*. Si no realiza este procedimiento, los usuarios seguirán visualizando una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión.

2.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en la aplicación con el proveedor de identidades seleccionado.

    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

  [login]: http://go.microsoft.com/fwlink/p/?LinkId=322050
