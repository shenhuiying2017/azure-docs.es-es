<properties title="Enterprise class WordPress on Azure Websites" pageTitle="WordPress de clase empresarial en Sitios web Azure" description="Obtenga información sobre cómo hospedar un sitio WordPress de clase empresarial en Sitios web Azure" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="tomfitz" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="11/11/2014" ms.author="tomfitz" />

#WordPress de clase empresarial en Sitios web Azure

Sitios web Azure proporciona un entorno escalable, seguro y fácil de usar para sitios de [WordPress][wordpress] a gran escala y críticos. El propio Microsoft ejecuta sitios de clase empresarial como los blogs de [Office][officeblog] y [Bing][bingblog]. Este documento muestra cómo puede usar Sitios web Azure para establecer y mantener un sitio de WordPress basado en la nube y de clase empresarial que pueda administrar un gran volumen de visitantes.

##En este artículo 

* [Arquitectura y planeación](#planning) - Conozca las consideraciones sobre arquitectura, requisitos y rendimiento antes de crear su sitio.

* [Creación](#howto) , implementación y configuración de su sitio

* [Más recursos](#resources) - Recursos e información adicionales.

##<a id="plan"></a>Arquitectura y planeación

Una instalación básica de WordPress solamente tiene dos requisitos.

* **Base de datos MySQL**, disponible a través de [ClearDB en la Tienda de Azure][cdbnstore], o bien puede administrar su propia instalación de MySQL en máquinas virtuales de Azure usando [Windows][mysqlwindows] o [Linux][mysqllinux].

    > [WACOM.NOTE] ClearDB proporciona varias configuraciones de MySQL, cada una de ellas con diferentes características de rendimiento. Consulte la [Tienda de Azure][cdbnstore] para obtener información sobre las ofertas disponibles en la Tienda de Azure o los [precios de ClearDB](http://www.cleardb.com/pricing.view) para conocer las ofertas que ClearDB ofrece directamente.
    
* **PHP 5.2.4 o posterior** - Sitios web Azure actualmente proporciona [las versiones 5.3, 5.4 y 5.5 de PHP][phpwebsite].

	> [WACOM.NOTE] Le recomendamos ejecutar siempre la versión más reciente de PHP para asegurarse de que dispone de las correcciones de seguridad más actuales.

###Implementación básica

Usando simplemente los requisitos básicos, podría crear una solución básica dentro de una región de Azure.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Aunque esto podría permitirle escalar la aplicación horizontalmente creando varias instancias del sitio web, todo se hospeda dentro de los centros de datos en una región geográfica específica. Los visitantes que se encuentran fuera de esta región pueden experimentar tiempos de respuesta lentos cuando usen el sitio y si los centros de datos de esta región dejan de funcionar, también lo hará su aplicación.


###Implementación en varias regiones

Mediante el [Administrador de tráfico][trafficmanager] de Azure, es posible escalar su sitio de WordPress en varias regiones geográficas proporcionando solamente una dirección URL para los visitantes. Todos los visitantes entran a través del Administrador de tráfico y, después, se les dirige a una región en función de la configuración del equilibrio de carga.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

Dentro de cada región, el sitio de WordPress se seguiría escalando entre varias instancias del sitio web, pero este escalamiento es específico de la región; las regiones con mucho tráfico se pueden escalar de forma diferente a las de poco tráfico.

La replicación y el enrutamiento a varias bases de datos MySQL se puede hacer usando el [enrutador de alta disponibilidad CDBR][cleardbscale] de ClearDB (mostrado la izquierda) o [MySQL Cluster CGE][cge]. 

###Implementación en varias regiones con almacenamiento multimedia y almacenamiento en caché

Si el sitio va a aceptar cargas u hospedar archivos multimedia, use almacenamiento de blogs de Azure. Si precisa de almacenamiento en caché, considere las opciones [caché Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/en-us/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/en-us/gallery/store/memcachier/memcachier/) o alguna de las demás ofertas de almacenamiento en caché de la [Tienda de Azure](http://azure.microsoft.com/en-us/gallery/store/).

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

De forma predeterminada, el almacenamiento de blobs se distribuye geográficamente entre regiones, por lo que no tiene que preocuparse por la replicación de archivos entre todos los sitios. También puede habilitar la [Red de entrega de contenido (CDN)][cdn] de Azure para almacenamientos de blobs, que distribuye archivos a los nodos extremo más próximos a los visitantes.

###Planeación

####Requisitos adicionales

Para hacer esto... | Use esto...
------------------------|-----------
**Cargar o almacenar archivos grandes** | [Complemento de WordPress para usar el almacenamiento de blobs][storageplugin]
**Enviar correo electrónico** | [SendGrid][storesendgrid] y el [complemento de WordPress para usar SendGrid][sendgridplugin]
**Nombres de dominio personalizados** | [Nombres de dominio personalizados con Sitios web Azure][customdomain]
**HTTPS** | [Compatibilidad HTTPS en Sitios web Azure][httpscustomdomain]
**Validación en fase previa a la producción** | [Compatibilidad con publicación provisional para Sitios web Azure][staging] <p>Tenga en cuenta que el cambio de un sitio de ensayo a producción también mueve la configuración de WordPress. Debe asegurarse de que toda la configuración está actualizada a los requisitos para el sitio de producción antes de cambiar el sitio de ensayo a producción.</p> 
**Supervisión y solución de problemas** | [Registro de diagnósticos con Sitios web Azure][log] y [Supervisión de Sitios web Azure][monitor]
**Implementar su sitio** | [Implementar un sitio web de Azure][deploy]

####Disponibilidad y recuperación ante desastres

Para hacer esto... | Use esto...
------------------------|-----------
**Sitios de equilibrio de carga** o **sitios distribuidos geográficamente** | [Direccionamiento del tráfico con el Administrador de tráfico de Azure][trafficmanager]
**Copia de seguridad y restauración** | [Copia de seguridad de Sitios web Azure][backup] y [Restauración de un sitio web de Azure][restore]

####Rendimiento

El rendimiento en la nube se logra principalmente mediante almacenamiento en caché y escalamiento horizontal; sin embargo, la memoria, el ancho de banda y otros atributos del hospedaje de sitios web también debe tenerse en cuenta.

Para hacer esto... | Use esto...
------------------------|-----------
**Descripción de las capacidades de instancias de sitios web** | [Detalles de precios, incluidas las capacidades de modos y tamaños de sitios web][websitepricing]
**Recursos de caché** | [Caché Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/en-us/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/en-us/gallery/store/memcachier/memcachier/) o alguna de las demás ofertas de almacenamiento en caché de la [Tienda de Azure](http://azure.microsoft.com/en-us/gallery/store/)
**Escale su aplicación** | [Escale un sitio web de Azure][websitescale] y [Direccionamiento de alta disponibilidad de ClearDB][cleardbscale]. Si opta por hospedar y administrar su propia instalación de MySQL, debe plantearse el uso de [CGE de MySQL Cluster][cge] para escalado horizontal

####Migración

Existen dos métodos para migrar un sitio de WordPress existente a Sitios web Azure.

* **[Exportación de WordPress][export]** - Mediante este método se exporta el contenido del blog, que después se puede importar a un nuevo sitio de WordPress en Azure usando el [complemento Importador de WordPress][import].

	> [WACOM.NOTE] Aunque este proceso permite migrar el contenido, no migra ningún complemento, tema u otras personalizaciones. Estos se deben instalar de nuevo manualmente.

* **Migración manual** - [Haga una copia de seguridad del sitio][wordpressbackup] y la [base de datos][wordpressdbbackup] y después restáurelo manualmente en un sitio web de Azure y la base de datos MySQL asociada para migrar sitios altamente personalizados y evitar la molestia de instalar complementos, temas y otras personalizaciones manualmente.

##Creación

###<a id="create"></a>de un sitio de WordPress

1. Use la [Tienda de Azure][cdbnstore] para crear una base de datos MySQL del tamaño identificado en la sección [Arquitectura y planeación](#planning) , en las regiones en que hospedará su sitio.

2. Siga los pasos de [Creación de un sitio web de WordPress desde la Galería de Azure][createwordpress] para crear un nuevo sitio de WordPress. Cuando cree el sitio, seleccione **Usar una base de datos MySQL existente** y, después, seleccione la base datos creada en el paso 1.

Si está migrando un sitio de WordPress existente, consulte [Migración de un sitio de WordPress existente](#migrate) después de crear un sitio nuevo.

###<a id="migrate"></a>Migración de un sitio de WordPress existente a Azure

Como se ha mencionado en la sección [Arquitectura y planeación](#planning) , hay dos formas de migrar un sitio web de WordPress.

* **exportación e importación** - Para sitios sin mucha personalización o donde solamente se desea mover el contenido.

* **copia de seguridad y restauración** - Para sitios con mucha personalización donde se desea mover todo.

Use una de las siguientes secciones para migrar el sitio.

####Método de exportación e importación

1. Use la [exportación de WordPress][export] para exportar el sitio existente.

2. Cree un nuevo sitio web usando los pasos de la sección [Crear un nuevo sitio WordPress](#create) .

3. Inicie sesión en el sitio de WordPress en Sitios web Azure y haga clic en **Complementos** -> **Agregar nuevo**. Busque e instale el complemento **Importador de WordPress**.

4. Una vez instalado el complemento del importador, haga clic en **Herramientas** -> **Importar** y seleccione **WordPress** para usar el complemento Importador de WordPress.

5. En la página **Importar WordPress**, haga clic en **Elegir archivo**. Busque el archivo WXR exportado desde el sitio de WordPress existente y después elija **Cargar archivo e importar**.

6. Haga clic en **Enviar**. Se mostrará un mensaje indicando que la importación se realizó correctamente.

8. Cuando haya completado todos estos pasos, reinicie el sitio web desde el panel de dicho sitio en el [Portal de administración de Azure][mgmtportal].

Después de importar el sitio, puede que necesite realizar los pasos siguientes para habilitar la configuración no contenida en el archivo de importación.

Si usaba esto... | Haga esto...
------------------ | ----------
**Vínculos permanentes** | En el panel de WordPress del nuevo sitio, haga clic en **Configuración** -> **Vínculos permanentes** y después actualice la estructura de vínculos permanentes.
**vínculos de imágenes/contenido multimedia** | Para actualizar estos vínculos a la nueva ubicación, use el [complemento Update URLs de Belvet Blues][velvet], una herramienta de búsqueda y reemplazo, o hágalo manualmente en la base de datos.
**Temas** | Vaya a **Apariencia** -> **Tema** y actualice el tema del sitio web según sea necesario.
**Menús** | Si el tema admite menús, los vínculos a la página principal todavía pueden tener el subdirectorio antiguo incrustado en ellos. Vaya a **Apariencia** -> **Menús** y actualícelos.

####Método de copia de seguridad y restauración

1. Haga una copia de seguridad del sitio de WordPress existente usando la información que se encuentra en [Copias de seguridad de WordPress][wordpressbackup].

2. Haga una copia de seguridad de la base datos existente usando la información que se encuentra en [Copia de seguridad de su base de datos][wordpressdbbackup].

3. Cree una nueva base de datos y restaurare la copia de seguridad.

	1. Adquiera una nueva base de datos de la [Tienda de Azure][cdbnstore] o configure una base de datos MySQL en una máquina virtual [Windows][mysqlwindows] o [Linux][mysqllinux].

	2. Usando un cliente MySQL como [MySQL Workbench][workbench], conéctese a la nueva se datos e importe la base de datos de WordPress.

	3. Actualice la base de datos para cambiar las entradas de dominio al nuevo dominio del sitio web de Azure. Ejemplo: mywordpress.azurewebsites.net. Use [el script de búsqueda y reemplazo para bases de datos de WordPress][searchandreplace] para guardar los cambios de todas las instancias de forma segura.

4. Cree un nuevo sitio web y publique la copia de seguridad de WordPress.

	1. Cree un sitio nuevo en el [Portal de administración de Azure][mgmtportal] con una base de datos; para ello, use **Nuevo** -> **Sitio web** -> **Creación personalizada**. Esto creará un sitio vacío.

	2. En la copia de seguridad de WordPress, localice el archivo **wp-config.php** y ábralo en un editor. Reemplace las entradas siguientes con la información de la nueva base de datos MySQL.

		* **DB_NAME**: nombre de usuario de la base de datos.

		* **DB_USER**: nombre de usuario usado para acceder a la base de datos.

		* **DB_PASSWORD**: contraseña de usuario.

		Después de cambiar estas entradas, guarde y cierre el archivo **wp-config.php**.

	3. Use la información existente en [Implementación de un sitio web de Azure][deploy] para habilitar el método de implementación que desee usar y después implemente la copia de seguridad de WordPress en el sitio web de Azure.

5. Una vez implementado el sitio de WordPress, podrá acceder a nuevo sitio usando la dirección URL *.azurewebsite.net correspondiente al sitio.

###Configuración del sitio

Después de crear o migrar el sitio de WordPress, use la siguiente información para mejorar el rendimiento o habilitar funcionalidades adicionales.

Para hacer esto... | Use esto...
------------- | -----------
**Definición del modo y el tamaño de sitios web y cómo habilitar el escalado** | [Escalado de sitios web][websitescale]
**Habilitar conexiones de base de datos persistentes** <p>De forma predeterminada, WordPress no usa conexiones de base de datos persistentes, lo que puede provocar que la conexión con la base de datos se limite después de varias conexiones.</p>  | <ol><li><p>Edite el archivo <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Busque la línea siguiente.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Reemplace la línea anterior por la siguiente.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Busque la línea siguiente.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Reemplace la línea anterior por la siguiente.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Guarde el archivo <strong>wp-includes/wp-db.php</strong> y vuelva a implementar el sitio.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>NOTA:</h5><p>Estos cambios se pueden sobrescribir cuando se actualiza WordPress.</p><p>WordPress tiene activadas las actualizaciones automáticas de forma predeterminada, que se pueden deshabilitar editando el archivo <strong>wp-config.php</strong> y agregando <code>la definición ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Otra forma de administrar las actualizaciones consistiría en usar un trabajo web que supervise el archivo <strong>wp-db.php</strong> y ejecute las modificaciones anteriores cada vez que el archivo se actualice. Consulte <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Introducción a la trabajos web</a> para obtener más información</p></div>
**Mejorar el rendimiento** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Desactivación de la cookie ARR</a> puede mejorar el rendimiento cuando ejecute WordPress en varias instancias del sitio web.</p></li><li><p>Activación del almacenamiento en caché. <a href="http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx">La Caché de Redis</a> (vista previa) se puede utilizar con el <a href="https://wordpress.org/plugins/redis-object-cache/">complemento de WordPress para la caché de objetos de Redis</a>, o bien se puede usar alguna de las demás ofertas de almacenamiento en caché de la <a href="http://azure.microsoft.com/en-us/gallery/store/">Tienda de Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Cómo agilizar WordPress con Wincache</a> - Wincache está habilitado de forma predeterminada para Sitios web</p></li><li><p><a href="http://azure.microsoft.com/en-us/documentation/articles/web-sites-scale/">Escale el sitio web de Azure</a> y use el <a href="http://www.cleardb.com/developers/cdbr/introduction">redireccionamiento de alta disponibilidad de ClearDB</a> o <a href="http://www.mysql.com/products/cluster/">CGE de MySQL Cluster</a></p></li></ul>
**Usar blobs para almacenamiento** | <ol><li><p><a href="http://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/">Crear una cuenta de almacenamiento de Azure</a></p></li><li><p>Obtenga información sobre cómo <a href="http://azure.microsoft.com/en-us/documentation/articles/cdn-how-to-use/">usar la red de distribución de contenido (CDN)</a> para distribuir geográficamente los datos almacenados en blobs.</p></li><li><p>Instale y configure el complemento <a href="https://wordpress.org/plugins/windows-azure-storage/">Almacenamiento de Azure para WordPress</a>.</p><p>Para obtener información detallada acerca de la instalación y configuración del complemento, consulte <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">la guía del usuario</a>.</p> </li></ol>
**Habilitar el correo electrónico** | <ol><li><p><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">Activación de SendGrid usando la Tienda de Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Instalación del complemento SendGrid para WordPress</a></p></li></ol>
**Configurar un nombre de dominio personalizado** | [Usar un nombre de dominio personalizado con un sitio web de Azure][customdomain]
**Habilitar HTTPS para un nombre de dominio personalizado** | [Use HTTPS con un sitio web de Azure][httpscustomdomain]
**Equilibrio de carga o distribución geográfica del sitio** | [Direccionamiento del tráfico con el Administrador de tráfico de Azure][trafficmanager]. Si usa un dominio personalizado, consulte [Uso de un nombre de dominio personalizado con un sitio web de Azure][customdomain] para obtener información acerca del Administrador de tráfico con nombres de dominio personalizados.
**Habilitar copias de seguridad automáticas de sitios web** | [Copia de seguridad de Sitios web Azure][backup]
**Habilitar registro de diagnósticos** | [Habilitar registro de diagnósticos para Sitios web][log]

##<a href="resources"></a>Recursos adicionales

* [Optimización de WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Conversión de un sitio de WordPress en un multisitio](http://azure.microsoft.com/en-us/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [Asistente para actualización de ClearDB para Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hospedaje de WordPress en una subcarpeta de su sitio web de Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Paso a paso: Creación de un sitio de WordPress mediante Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hospedaje de un blog de WordPress existente en Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Activación de vínculos permanentes descriptivos en WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Migración y ejecución del blog de WordPress en Sitios web Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Ejecución de WordPress en Sitios web Azure gratuitamente](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress en Azure en 2 minutos o menos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Movimiento de un bloque de WordPress a Azure - Parte 1: Creación de un blog de WordPress en Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Movimiento de un bloque de WordPress a Azure - Parte 2: Transferencia del contenido](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Movimiento de un bloque de WordPress a Azure - Parte 3: Configuración de un dominio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Movimiento de un bloque de WordPress a Azure - Parte 4: Vínculos permanentes descriptivos y reglas de reescritura de direcciones URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Movimiento de un bloque de WordPress a Azure - Parte 5: Movimiento desde una subcarpeta a la raíz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Configuración de un sitio web de WordPress en la cuenta de Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Sustentación de WordPress en Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Sugerencias para WordPress en Azure](http://www.johnpapa.net/azurecleardbmysql/)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/en-us/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/en-us/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/en-us/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-php-web-site-gallery/
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/en-us/documentation/articles/cdn-how-to-use/

<!--HONumber=35.1-->
