Los clientes de Azure pueden desbloquear 25.000 correos electrónicos gratuitos cada mes. Estos 25 000 correos electrónicos mensuales gratuitos le darán acceso a funciones avanzadas de informes y análisis, así como a [todas las API][all APIs] (Web, SMTP, Event, Parse, etc.). Para información sobre los servicios adicionales que ofrece SendGrid, visite la página [Soluciones de SendGrid][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>Para registrarse y obtener una cuenta SendGrid
1. Inicie sesión en [Azure Portal][Azure portal].
2. En el menú de la izquierda, haga clic en **Nuevo**.

    ![command-bar-new][command-bar-new]
3. Haga clic en **Complementos** y, luego, en **Servicio de correo electrónico SendGrid**.

    ![sendgrid-store][sendgrid-store]
4. Complete el formulario de registro y seleccione **Crear**.

    ![sendgrid-create][sendgrid-create]
5. Escriba un **nombre** para identificar el servicio SendGrid en la configuración de Azure. Los nombres deben tener de 1 a 100 caracteres y contener únicamente caracteres alfanuméricos, guiones, puntos y caracteres de subrayado. El nombre debe ser único en la lista de elementos de la Tienda de Azure.
6. Escriba y confirme la **contraseña**.
7. Elija la **suscripción**.
8. Cree un **grupo de recursos**  o use uno existente.
9. En la sección **Plan de tarifa**, seleccione el plan de SendGrid en el que desea registrarse.

    ![sendgrid-pricing][sendgrid-pricing]
10. Escriba un **código de promoción**, si tiene.
11. Escriba la **información de contacto**.
12. Revise y acepte los **términos legales**.
13. Después de confirmar la cuenta, verá una ventana emergente que indica que la **implementación se realizó correctamente** y la cuenta aparecerá en la sección **Todos los recursos**.

    ![all-resources][all-resources]

    Una vez que complete la compra y haga clic en el botón **Administrar** para iniciar el proceso de comprobación de correo electrónico, recibirá un correo electrónico de SendGrid en el que se le pedirá que compruebe la cuenta. Si no recibe este correo electrónico o tiene problemas para comprobar la cuenta, consulte estas preguntas más frecuentes.

    ![administrar][manage]

    **Solo podrá enviar hasta 100 correos electrónicos al día hasta que compruebe la cuenta.**

    Para modificar el plan de suscripción o ver la configuración de contacto de SendGrid, haga clic en el nombre de su servicio SendGrid para abrir el panel del Marketplace de SendGrid.

    ![configuración][settings]

    Para enviar un correo electrónico con SendGrid, debe proporcionar su clave de API.

### <a name="to-find-your-sendgrid-api-key"></a>Para encontrar su clave de API de SendGrid
1. Haga clic en **Administrar**.

    ![administrar][manage]
2. En el panel de SendGrid, seleccione **Configuración** y, luego, **Claves de API** en el menú de la izquierda.

    ![api-keys][api-keys]

3. Haga clic en el menú desplegable **Crear clave de API** y seleccione **Clave de API general**.

    ![general-api-key][general-api-key]
4. Como mínimo, debe proporcionar el **nombre de esta clave** y otorgar acceso completo a **Envío de correo** y seleccione **Guardar**.

    ![acceso][access]
5. En este punto, la API aparecerá una vez. Asegúrese de almacenarla de forma segura.

### <a name="to-find-your-sendgrid-credentials"></a>Para encontrar las credenciales de SendGrid
1. Haga clic en el icono de llave para buscar el **nombre de usuario**.

    ![key][key]
2. La contraseña es la que eligió en el momento de la configuración. Si desea hacer algún cambio, puede seleccionar **Cambiar contraseña** o **Restablecer contraseña**.

Para administrar la configuración de las opciones de entrega de correo electrónico, haga clic en el botón **Administrar**. De esta forma, se le redirigirá al panel de SendGrid.

    ![manage][manage]

    For more information on sending email through SendGrid, visit the [Email API Overview][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
