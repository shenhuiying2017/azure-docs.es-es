1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **Registrations**.

    ![][]

2.  En **Registrations**, haga clic en la pestaña **Script** y seleccione **Insert**.

    ![][1]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **Registrations**.

3.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registrations');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertChannelIfNotFound });
            function insertChannelIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

De esta manera, se registra un nuevo script de inserción, que almacena la información de registro en la nueva tabla.

  []: ./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png
  [1]: ./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png
