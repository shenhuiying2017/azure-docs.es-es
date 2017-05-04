Vuelva a la ventana del explorador que contiene Azure Portal.

#### <a name="add-hostname"></a>Adición de un nombre de host

Haga clic en el icono **+** situado junto a **Agregar nombre de host**.

![Agregar nombre de host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

#### <a name="validate-hostname"></a>Validación del nombre de host

Escriba el nombre de dominio completo para el que ha configurado anteriormente el registro CNAME (por ejemplo, `www.contoso.com`) y, a continuación, haga clic en **Validar**.

Seleccione la opción **CNAME (www.ejemplo.com o cualquier subdominio)** en el encabezado **Tipo de registro de nombre de host**.

Haga clic en **Agregar nombre de host** para agregar el nombre DNS a la aplicación.

![Agregar el nombre DNS a la aplicación](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

El nuevo nombre de host puede tardar algún tiempo en reflejarse en la página **Dominios personalizados** de la aplicación. Intente actualizar el explorador para actualizar los datos.