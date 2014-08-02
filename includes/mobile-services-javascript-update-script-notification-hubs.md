

Finalmente, debe actualizar el script registrado para insertar la operación en la tabla TodoItem a fin de enviar notificaciones.

1.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

      ![](./media/mobile-services-javascript-update-script-notification-hubs/mobile-insert-script-push2.png)

2.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

         function insert(item, user, request) {
         // Definir una carga para la notificación del sistema de la Tienda Windows.
         var payload = '<
         xml version="1.0" encoding="utf-8"
         ><toast><visual>' +    
             '<binding template="ToastText01">  <text id="1">' +
             item.text + '</text></binding></visual></toast>';
            
         request.execute({
             success: function() {
                 // Si la inserción se realiza correctamente, enviar una notificación.
                push.wns.send(null,payload, 'wns/toast', {
                     success: function(pushResponse) {
                         console.log("Sent push:", pushResponse);
                         request.respond();
                         },              
                         error: function (pushResponse) {
                             console.log("Error Sending push:", pushResponse);
                             request.respond(500, { error: pushResponse });
                             }
                         });
                     }
                 });
         }

    Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los registros de aplicaciones de la Tienda Windows una vez que se realiza la inserción.


