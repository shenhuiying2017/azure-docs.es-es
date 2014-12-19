
1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-selection.png)

2. Haga clic en la pestaña **Insertar**, seleccione **Solo usuarios autenticados** para **Permisos** y, a continuación, haga clic en **Editar script**.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-push-registration-endpoint.png)
	
	Esto permite personalizar la función de devolución de llamada de registro de la notificación de inserción. Si usa Git para editar el código de origen, esta misma función de registro se encuentra en ".\service\extensions\push.js".

3. Reemplace la función **register** existente por el siguiente código:

		exports.register = function (registration, registrationContext, done) {   
		    // Get the ID of the logged-in user.
			var userId = registrationContext.user.userId;    
		    
			// Perform a check here for any disallowed tags.
			if (!validateTags(registration))
			{
				// Return a service error when the client tries 
		        // to set a user ID tag, which is not allowed.		
				done("You cannot supply a tag that is a user ID");		
			}
			else{
				// Add a new tag that is the user ID.
				registration.tags.push(userId);
				
				// Complete the callback as normal.
				done();
			}
		};
		
		function validateTags(registration){
		    for(var i = 0; i < registration.tags.length; i++) { 
		        console.log(registration.tags[i]);           
				if (registration.tags[i]
				.search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
					return false;
				}
				return true;
			}
		}

	Esto agrega una etiqueta al registro que es el identificador del usuario que inició sesión. Las etiquetas suministradas se validan para evitar que un usuario se registre con el identificador de otro usuario. Cuando se envía una notificación a este usuario, se recibe en este dispositivo y cualquier otro que el usuario haya registrado.

4. Haga clic en la flecha atrás, en la pestaña **Datos**, haga clic en **TodoItem**, haga clic en **Script** y seleccione **Insertar**. 
