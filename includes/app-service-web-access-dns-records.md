### <a name="access-dns-records-with-domain-provider"></a>Acceso a los registros DNS con el proveedor de dominios

En primer lugar, inicie sesión en el sitio web de su proveedor de dominios.

Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, por lo que debe consultar la documentación del proveedor. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**.

A menudo, puede encontrar el vínculo al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**. Después, busque un vínculo que le permita administrar registros DNS. Este vínculo podría denominarse **Archivo de zona**, **Registros DNS** o **Configuración avanzada**.

La captura de pantalla siguiente es un ejemplo de página de registros DNS:

![Página de registros DNS de ejemplo](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

En la captura de pantalla de ejemplo, haga clic en **Agregar** para crear un registro. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros. De nuevo, consulte la documentación del proveedor.

> [!NOTE]
> En el caso de ciertos proveedores, como GoDaddy, los cambios en los registros DNS no se harán efectivos hasta que haga clic en un vínculo **Guardar cambios** independiente.
>