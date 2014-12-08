#Creación una base de datos MySQL en Azure

Esta guía le mostrará cómo usar [ClearDB] para la creación de una base de datos MySQL desde la [Tienda de Azure] y cómo crear una base de datos MySQL como un recurso vinculado cuando cree un [Sitio web Azure][waws]. [ClearDB] es un proveedor de base de datos como servicio tolerante a errores que le permite ejecutar y administrar bases de datos MySQL en centros de datos de Azure y conectarse a ellos desde cualquier aplicación.  

##Tabla de contenido
* [Direccionamiento del una base de datos MySQL desde la Tienda de Azure](#CreateFromStore)
* [Direccionamiento del una base de datos MySQL como recurso vinculado para un Sitio Web Azure](#CreateForWebSite)

<div class="dev-callout"> 
<b>Nota</b> 
<p>Cuando cree una base de datos MySQL como parte del proceso de creación del sitio web, solo se le permitirá crear una base de datos gratuita. Si crea una base de datos MySQL desde la Tienda de Azure, podrá crear una base de datos gratuita o seleccionar opciones de pago.</p> 
</div>

<h2><a id="CreateFromStore"></a>Creación de una base de datos MySQL desde la Tienda de Azure</h2>

Para crear una base de datos de MySQL desde la [Tienda de Azure], realice las siguientes tareas:

1. Inicie sesión en el [Portal de administración de Azure][portal].
2. Haga clic en **+NUEVO** en la parte inferior de la página y, a continuación, seleccione **ALMACENAR**.

	![Select add-on from store](./media/create-mysql-db/select-store.png)

3. Seleccione **Base de datos MySQL de ClearDB** y, a continuación, haga clic en la flecha en la parte inferior del marco.

	![Select ClearDB MySQL Database](./media/create-mysql-db/select-cleardb-mysql.png)

4. Seleccione un plan, especifique un nombre de base de datos, seleccione una región y, a continuación, haga clic en la flecha de la parte inferior del marco.

	![Purchase MySQL database from store](./media/create-mysql-db/purchase-mysql.png)

5. Haga clic en la marca de verificación para completar su compra.

	![Review and complete your purchase](./media/create-mysql-db/complete-mysql-purchase.png)

6. Una vez que se ha creado la base de datos, puede administrarla desde la pestaña **COMPLEMENTOS** en el portal de administración.

	![Manage MySQL database in Azure portal](./media/create-mysql-db/manage-mysql-add-on.png)

7. Puede obtener acceso a la información de la conexión de la base de datos haciendo clic en **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la página (se muestra en la parte superior).

	![MySql connection information](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>Creación de una base de datos MySQL como recurso vinculado para un Sitio Web Azure</h2>

Para crear una base de datos MySQL como recurso vinculado cuando cree un [Sitio web Azure][waws], realice los siguientes pasos:

1. Inicie sesión en el [Portal de administración de Azure][portal].
2. Haga clic en **+NUEVO** en la parte inferior de la página y, a continuación, seleccione **PROCESO**, **SITIO WEB** y **CREAR CON BASE DE DATOS**.

	![Create website with database](./media/create-mysql-db/custom_create.png)

3. Proporcione una **URL** para el sitio web, seleccione **REGIÓN** para el sitio y seleccione **Crear una nueva base de datos MySQL** en el menú desplegable **BASE DE DATOS**. Opcionalmente, puede sustituir el nombre determinado para la cadena de conexión. Haga clic en la flecha de la parte inferior de la página.

	![Provide website details](./media/create-mysql-db/provide-website-details.png) 

4. Proporcione el nombre de la base de datos en **NOMBRE**, seleccione la **REGIÓN** de la base de datos (puede ser la misma que la región del sitio web), acepte las condiciones legales de ClearDB y haga clic en la marca de verificación de la parte inferior del marco.

	![Provide MySQL details](./media/create-mysql-db/provide-mysql-details.png)

5. Una vez que se haya creado el sitio web, haga clic en el nombre del sitio para ir al panel del sitio.

	![Go to web site dashboard](./media/create-mysql-db/go-to-website-dashboard.png)

6. Haga clic en **CONFIGURAR**.

	![Go to configure tab](./media/create-mysql-db/go-to-configure-tab.png)

7. Desplácese a la sección **cadenas de conexión** y haga clic en **Mostrar cadenas de conexión**. 

	![Show connection string](./media/create-mysql-db/show-conn-string.png)

8. Copie la cadena de conexión para usarla en su aplicación.

	![Shown connection string](./media/create-mysql-db/shown-conn-string.png)

> [WACOM.NOTE] La aplicación del sitio web puede obtener acceso a las cadenas de conexión mediante el nombre de cadena de conexión. En las aplicaciones .NET, las cadenas de conexión están disponibles en el objeto **connectionStrings**. En otros lenguajes de programación, se puede obtener acceso a las cadenas de conexión como variables de entorno. Para obtener más información, consulte [Configuración de Sitios web][configurar].

[ClearDB]: http://www.cleardb.com/
[waws]: /es-es/documentation/services/web-sites/
[Tienda de Azure]: /es-es/gallery/store/
[portal]: http://manage.windowsazure.com
[configurar]: ../web-sites-configure/
