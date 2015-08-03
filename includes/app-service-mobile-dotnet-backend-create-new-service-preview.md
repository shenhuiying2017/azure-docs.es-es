

Siga estos pasos para crear una aplicación móvil nueva.

1. Inicie sesión en el [Portal de Azure]. En la parte inferior izquierda de la ventana, haga clic en **+NEW**. Desplácese hasta que vea el elemento **Aplicación móvil**.

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/new-mobile-app.png)

    Se muestra la hoja **Nueva aplicación móvil**.

2. Escriba un nombre para la aplicación móvil. Debe escribirse en minúscula y tener al menos 8 caracteres.

7. Seleccione una región. En este tutorial, se usa **Centro y Sur de EE. UU.**.

    > [AZURE.NOTE]Como parte de este tutorial, va a crear una instancia y un nuevo servidor de Base de datos SQL. Puede reutilizar esta nueva base de datos y administrarla como lo haría con cualquier otra instancia de Base de datos SQL. Si ya hay una base de datos en la misma región que el nuevo back-end de la aplicación móvil, puede elegir **Usar base de datos existente** y, a continuación, seleccionar dicha base de datos. No se recomienda el uso de una base de datos en una región diferente debido a los costes adicionales de ancho de banda y las elevadas latencias.

3. Seleccione su suscripción.

4. Cree un nuevo grupo de recursos con el mismo nombre que la aplicación móvil.

5. En **Configuración del paquete**, seleccione **USERDATABASE**, y podrá elegir si desea usar una base de datos existente o crear una nueva. Para crear una nueva base de datos, escriba el nombre de la **base de datos**, cree un **servidor** nuevo, escriba el nombre de dicho servidor y elija un **nombre de inicio de sesión**, que es el nombre de inicio de sesión de administrador para el nuevo servidor de Base de datos SQL, escriba y confirme la contraseña, y haga clic en el botón Aceptar para completar el proceso. Si selecciona una base de datos existente, tendrá que especificar una **contraseña de administrador de servidor**.

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create-db.png)

6. Cree un nuevo plan de hospedaje web con el mismo nombre que la aplicación móvil.

    > [AZURE.NOTE]Escriba el nombre del plan de hospedaje web, no lo copie y pegue. En este campo se validan los nombres y se producirá un error ni no escribe el nombre. No tiene que ser exactamente el mismo nombre que el sitio web (pero debe seguir las mismas reglas).

8. Seleccione un nivel de precios. En este tutorial, se usa **Estándar 1**.

    La página de configuración de la nueva aplicación móvil será similar a la siguiente:

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create.png)

9. Tras hacer clic en el botón **Crear** de la parte inferior de la hoja debería comenzar la implementación en la ventana de notificaciones.

Ya ha creado un nuevo back-end de aplicación móvil que pueden usar las aplicaciones móviles.

> [AZURE.NOTE]Después de que se cree la aplicación móvil, navegue en el portal al servidor SQL que acaba de crear (asegúrese de seleccionar la base de datos SQL del servidor, no la de Azure). Ahí, haga clic en la parte de configuración, expanda la parte de firewall y cambie la opción "Permitir acceso a servicios de Azure". Si no lo hace, la aplicación no funcionará.

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=July15_HO4-->