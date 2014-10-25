# Creación una base de datos MySQL en Azure

Esta guía le mostrará cómo usar [ClearDB][] para la creación de una base de datos MySQL desde la [Tienda de Azure][] y cómo crear una base de datos MySQL como un recurso vinculado cuando cree un [Sitio web Azure][]. [ClearDB][] es un proveedor de base de datos como servicio tolerante a errores que le permite ejecutar y administrar bases de datos MySQL en centros de datos de Azure y conectarse a ellos desde cualquier aplicación.

## Tabla de contenido

-   [Direccionamiento del una base de datos MySQL desde la Tienda de Azure][]
-   [Direccionamiento del una base de datos MySQL como recurso vinculado para un Sitio Web Azure][]

<div class="dev-callout"> 
<b>Nota:</b> 
<p>Cuando cree una base de datos MySQL como parte del proceso de creaci&oacute;n del sitio web, solo se le permitir&aacute; crear una base de datos gratuita. Si crea una base de datos MySQL desde la Tienda de Azure, podr&aacute; crear una base de datos gratuita o seleccionar opciones de pago.</p> 
</div>

## <span id="CreateFromStore"></span></a>Direccionamiento del una base de datos MySQL desde la Tienda de Azure

Para crear una base de datos de MySQL desde la [Tienda Azure][Tienda de Azure], realice las siguientes tareas:

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **+NEW** en la parte inferior de la página y, a continuación, seleccione **STORE**.

    ![Seleccionar complemento de la tienda][]

3.  Seleccione **ClearDB MySQL Database** y, a continuación, haga clic en la flecha en la parte inferior del marco.

    ![Seleccionar la base de datos ClearDB MySQL][]

4.  Seleccione un plan, especifique un nombre de base de datos, seleccione una región y, a continuación, haga clic en la flecha de la parte inferior del marco.

    ![Comprar la base de datos MySQL de la tienda][]

5.  Haga clic en la marca de verificación para completar su compra.

    ![Revisar y finalizar la compra][]

6.  Una vez que se ha creado la base de datos, puede administrarla desde la pestaña **ADD-ONS** en el portal de administración.

    ![Administración de la base de datos MySQL en el portal de Azure][]

7.  Puede obtener acceso a la información de la conexión de la base de datos haciendo clic en **CONNECTION INFO** en la parte inferior de la página (se muestra en la parte superior).

    ![Información de la conexión MySql][]

## <span id="CreateForWebSite"></span></a>Direccionamiento del Una base de datos MySQL como recurso vinculado para un Sitio Web Azure

Para crear una base de datos MySQL como recurso vinculado cuando cree un [Sitio web Azure][], realice los siguientes pasos:

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **+NEW** en la parte inferior de la página y, a continuación, seleccione **PROCESO**, **SITIO WEB** y **CREATE WITH DATABASE**.

    ![Crear sitio web con base de datos][]

3.  Proporcione una **URL** para el sitio web, seleccione **REGION** para el sitio y seleccione **Create a new MySQL database** en el cuadro desplegable **DATABASE**. Opcionalmente, puede sustituir el nombre determinado para la cadena de conexión. Haga clic en la flecha de la parte inferior de la página.

    ![Información del sitio web][]

4.  Proporcione el nombre de la base de datos en **NAME**, seleccione la región de la base de datos en **REGION** (puede ser la misma que la región del sitio web), acepte las condiciones legales de ClearDB y haga clic en la marca de verificación de la parte inferior del marco.

    ![Información de MySQL][]

5.  Una vez que se haya creado el sitio web, haga clic en el nombre del sitio para ir al panel del sitio.

    ![Ir al panel del sitio web][]

6.  Haga clic en **CONFIGURE**.

    ![Ir a la pestaña de configuración][]

7.  Desplácese a la sección **connection strings** y haga clic en **Show Connection Strings**.

    ![Mostrar la cadena de conexión][]

8.  Copie la cadena de conexión para usarla en su aplicación.

    ![Mostrar cadena de conexión][]

> [WACOM.NOTE] La aplicación del sitio web puede obtener acceso a las cadenas de conexión mediante el nombre de cadena de conexión. En las aplicaciones .NET, las cadenas de conexión están disponibles en el objeto **connectionStrings**. En otros lenguajes de programación, se puede obtener acceso a las cadenas de conexión como variables de entorno. Para obtener más información, consulte [Configuración de Sitios web][].

  [ClearDB]: http://www.cleardb.com/
  [Tienda de Azure]: /es-es/gallery/store/
  [Sitio web Azure]: /es-es/documentation/services/web-sites/
  [Direccionamiento del una base de datos MySQL desde la Tienda de Azure]: #CreateFromStore
  [Direccionamiento del una base de datos MySQL como recurso vinculado para un Sitio Web Azure]: #CreateForWebSite
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Seleccionar complemento de la tienda]: ./media/create-mysql-db/select-store.png
  [Seleccionar la base de datos ClearDB MySQL]: ./media/create-mysql-db/select-cleardb-mysql.png
  [Comprar la base de datos MySQL de la tienda]: ./media/create-mysql-db/purchase-mysql.png
  [Revisar y finalizar la compra]: ./media/create-mysql-db/complete-mysql-purchase.png
  [Administración de la base de datos MySQL en el portal de Azure]: ./media/create-mysql-db/manage-mysql-add-on.png
  [Información de la conexión MySql]: ./media/create-mysql-db/mysql-conn-info.png
  [Crear sitio web con base de datos]: ./media/create-mysql-db/custom_create.png
  [Información del sitio web]: ./media/create-mysql-db/provide-website-details.png
  [Información de MySQL]: ./media/create-mysql-db/provide-mysql-details.png
  [Ir al panel del sitio web]: ./media/create-mysql-db/go-to-website-dashboard.png
  [Ir a la pestaña de configuración]: ./media/create-mysql-db/go-to-configure-tab.png
  [Mostrar la cadena de conexión]: ./media/create-mysql-db/show-conn-string.png
  [Mostrar cadena de conexión]: ./media/create-mysql-db/shown-conn-string.png
  [Configuración de Sitios web]: ../web-sites-configure/
