<properties title="Enterprise class WordPress on Azure Websites" pageTitle="WordPress de clase empresarial en Sitios web Azure" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="01/01/1900" ms.author="cephalin" />

#WordPress de clase empresarial en Sitios web Azure

Sitios web Azure proporciona un entorno escalable, seguro y fácil de usar para sitios de [WordPress][wordpress] de gran escala y críticos. El propio Microsoft ejecuta sitios de clase empresarial como los blogs de[Office][officeblog] y [Bing][bingblog]. Este documento muestra cómo puede usar Sitios web Azure para establecer y mantener un sitio de WordPress basado en la nube y de clase empresarial que pueda administrar un gran volumen de visitantes.

##En este artículo 

* [Arquitectura y planeación](#planning) - Conozca las consideraciones sobre arquitectura, requisitos y rendimiento antes de crear su sitio

* [Procedimientos](#howto) - Cree, implemente y configure su sitio

* [Más recursos](#resources) - Recursos e información adicionales.

##<a id="plan"></a>Arquitectura y planeación

Una instalación básica de WordPress solamente tiene dos requisitos.

* **Base de datos MySQL**, disponible a través de [ClearDB en la Tienda de Azure][cdbnstore], o bien puede administrar su propia instalación de MySQL en máquinas virtuales de Azure usando [Windows][mysqlwindows] o [Linux][mysqllinux].

    > [WACOM.NOTE] ClearDB proporciona varias configuraciones de MySQL, cada una de ellas con diferentes características de rendimiento. Consulte la [Tienda de Azure][cdbnstore] para obtener información sobre las ofertas proporcionadas a través de dicha tienda o los [precios de ClearDB](http://www.cleardb.com/pricing.view) para conocer las ofertas directamente desde ClearDB.
    
* **PHP 5.2.4 o superior** - Sitios web Azure actualmente proporciona [las versiones 5.3, 5.4 y 5.5 de PHP][phpwebsite].

	> [WACOM.NOTE] Le recomendamos ejecutar siempre la versión más reciente de PHP para asegurarse de que dispone de las correcciones de seguridad más actuales.

###Implementación básica

Usando simplemente los requisitos básicos, podría crear una solución básica dentro de una región de Azure.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Aunque esto podría permitirle escalar la aplicación horizontalmente creando varias instancias del sitio web, todo se hospeda dentro de los centros de datos en una región geográfica específica. Los visitantes que se encuentran fuera de esta región pueden experimentar tiempos de respuesta lentos cuando usen el sitio y si los centros de datos de esta región dejan de funcionar, también lo hará su aplicación.


###Implementación en varias regiones

Mediante el [Administrador de tráfico ][trafficmanager] de Azure, es posible escalar su sitio de WordPress en varias regiones geográficas proporcionando solamente una dirección URL para los visitantes. Todos los visitantes entran a través del Administrador de tráfico y, después, se les dirige a una región en función de la configuración del equilibrio de carga.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

Dentro de cada región, el sitio de WordPress se seguiría escalando entre varias instancias del sitio web, pero este escalamiento es específico de la región; las regiones con mucho tráfico se pueden escalar de forma diferente a las de poco tráfico.

La replicación y el enrutamiento a varias bases de datos MySQL se puede hacer usando el [enrutador de alta disponibilidad CDBR][cleardbscale] de ClearDB (mostrado la izquierda) o [CGE de MySQL Cluster][cge]. 

###Implementación en varias regiones con almacenamiento multimedia y almacenamiento en caché

Si el sitio va a aceptar cargas u hospedar archivos multimedia, use almacenamiento de blogs de Azure. Si necesita almacenamiento en caché, plantéese el uso de [Caché en Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/es-es/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/es-es/gallery/store/memcachier/memcachier/) o una de las otras ofertas de almacenamiento en caché de la [Tienda de Azure](http://azure.microsoft.com/es-es/gallery/store/).

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

De forma predeterminada, el almacenamiento de blobs se distribuye geográficamente entre regiones, por lo que no tiene que preocuparse por la replicación de archivos entre todos los sitios. También puede habilitar la [Red de entrega de contenido (CDN)][cdn] de Azure para almacenamientos de blobs, que distribuye archivos a los nodos extremo más próximos a los visitantes.

###Planeación

####Requisitos adicionales

Para hacer esto... | Use esto...
------------------------|-----------
**Cargar o almacenar archivos de gran tamaño** | [Complemento de WordPress mediante almacenamiento de blobs][storageplugin]
**Enviar correo electrónico ** | [SendGrid][storesendgrid] y el [complemento de WordPress para utilizar SendGrid][sendgridplugin]
**Nombres de dominio personalizado** | [Nombres de dominio personalizado con Sitios web Azure][customdomain]
**HTTPS** | [Compatibilidad con HTTPS en Sitios web Azure][httpscustomdomain]
**Validación de preproducción** | [Compatibilidad para la publicación de ensayo para Sitios web Azure][staging] <p>Tenga en cuenta que cambiar un sitio de ensayo a producción también afecta a la configuración de WordPress. Debe asegurarse de que toda la configuración está actualizada a los requisitos para el sitio de producción antes de cambiar el sitio de ensayo a producción.</p> 
**Supervisión y solución de problemas** | [Registro de diagnóstico con Sitios web Azure][log] y [Supervisión de Sitios web Azure][monitor]
**Implementar su sitio** | [Implementar un sitio web de Azure][deploy]

####Disponibilidad y recuperación ante desastres

Para hacer esto... | Use esto...
------------------------|-----------
**Sitios de equilibrio de carga** o **sitios de distribución geográfica** | [Enrutar tráfico con Administrador de tráfico de Azure][trafficmanager]
**Hacer copia de seguridad y restaurar** | [Copia de seguridad de Sitios web Azure][backup] y [Restaurar un sitio web de Azure][restore]

####Rendimiento

El rendimiento en la nube se logra principalmente mediante almacenamiento en caché y escalamiento horizontal; sin embargo, la memoria, el ancho de banda y otros atributos del hospedaje de sitios web también debe tenerse en cuenta.

Para hacer esto... | Use esto...
------------------------|-----------
**Comprender las capacidades de instancia del sitio web** | [Detalles de precios, incluidas las funciones de tamaños y modos de sitio web][websitepricing]
**Recursos de caché** | [Caché en Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/es-es/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/es-es/gallery/store/memcachier/memcachier/) o una de las otras de oferta de caché en la [Tienda Azure](http://azure.microsoft.com/es-es/gallery/store/)
**Escalar la aplicación** | [Escalar un sitio web de Azure][websitescale] y [Enrutamiento de alta disponibilidad de ClearDB][cleardbscale]. Si opta por hospedar y administrar su propia instalación de MySQL, debe plantearse el uso de [CGE de MySQL Cluster][cge] para el escalamiento horizontal

####Migración

Existen dos métodos para migrar un sitio de WordPress existente a Sitios web Azure.

* **[Exportación de WordPress][export]** - Esto exporta el contenido del blog, que después se puede importar a un nuevo sitio de WordPress en Azure usando el [complemento Importador de WordPress][import].

	> [WACOM.NOTE] Aunque este proceso permite migrar el contenido, no migra ningún complemento, tema u otras personalizaciones. Estos elementos se deben instalar de nuevo manualmente.

* **Migración manual** - [Haga una copia de seguridad del sitio][wordpressbackup] y la [base de datos][wordpressdbbackup] y después restáurelo manualmente en un sitio web de Azure y la base de datos MySQL asociada para migrar sitios altamente personalizados y evitar la molestia de instalar complementos, temas y otras personalizaciones manualmente.

##Cómo

###<a id="create"></a>Creación de un sitio de WordPress

1. Use la [Tienda de Azure][cdbnstore] para crear una base de datos MySQL del tamaño que identificó en la sección [Arquitectura y planeación](#planning) en las regiones en las que hospedará el sitio.

2. Siga los pasos de [Creación de un sitio web de WordPress desde la galería de Azure ][createwordpress] para crear un nuevo sitio de WordPress. Cuando cree el sitio, seleccione **Uso de una Base de datos MySQL existente** y, después, seleccione la base datos creada en el paso 1.

Si está migrando un sitio de WordPress existente, consulte [Migración de un sitio de WordPress existente](#migrate) después de crear un nuevo sitio.

###<a id="migrate"></a>Migración de un sitio de WordPress existente a Azure

Como se menciono en la sección [Arquitectura y planeación](#planning), hay dos formas de migrar un sitio web de WordPress.

* **Exportación e importación** - Para sitios sin mucha personalización o donde solamente se desea mover el contenido.

* **Copia de seguridad y restauración** - Para sitios con mucha personalización donde se desea mover todo.

Use una de las siguientes secciones para migrar el sitio.

####Método de exportación e importación

1. Utilice [exportación de WordPress][export] para exportar el sitio existente.

2. Cree un nuevo sitio web usando los pasos de la sección [Creación de un nuevo sitio WordPress](#create).

3. Inicie sesión en el sitio de WordPress en Sitios web Azure y haga clic en **Complementos** -> **Agregar nuevo**. Busque e instale el complemento **Importador de WordPress**.

4. Una vez instalado el complemento del importador, haga clic en **Herramientas** -> **Importar** y seleccione **WordPress** para usar el complemento Importador de WordPress.

5. En la página **Importar WordPress**, haga clic en **Elegir archivo**. Busque el archivo WXR exportado desde el sitio de WordPress existente y después elija **Cargar archivo e importar**.

6. Haga clic en **Enviar**. Se mostrará un mensaje indicando que la importación se realizó correctamente.

8. Cuando haya completado todos estos pasos, reinicie el sitio web desde el panel de dicho sitio en el [Portal de administración de Azure][mgmtportal].

Después de importar el sitio, puede que necesite realizar los pasos siguientes para habilitar la configuración no contenida en el archivo de importación.

Si usaba esto... | Haga esto...
------------------ | ----------
**Vínculos permanentes** | En el panel de WordPress del nuevo sitio, haga clic en **Configuración** -> **Vínculos permanentes** y después actualice la estructura de vínculos permanentes
**vínculos imágenes/multimedia** | Para actualizar estos vínculos a la nueva ubicación, use el [complemento Update URLs de Belvet Blues][velvet], una herramienta de búsqueda y reemplazo, o bien hágalo manualmente en la base de datos
**Temas** | Vaya a **Apariencia** -> **Tema** y actualice el tema del sitio web según sea necesario
**Menús** | Si el tema admite menús, los vínculos a la página principal todavía pueden tener el subdirectorio antiguo incrustado en ellos. Vaya a **Apariencia** -> **Menús** y actualícelos.

####Método de copia de seguridad y restauración

1. Haga una copia de seguridad del sitio de WordPress existente usando la información que se encuentra en [Copias de seguridad de WordPress][wordpressbackup].

2. Haga una copia de seguridad de la base datos existente usando la información que se encuentra en [Copia de seguridad de su base de datos][wordpressdbbackup].

3. Cree una nueva base de datos y restaurare la copia de seguridad.

	1. Adquiera una nueva base de datos en la [Tienda de Azure][cdbnstore] o configure una base de datos MySQL en una VM de [Windows][mysqlwindows] o [Linux][mysqllinux].

	2. Usando un cliente MySQL como [MySQL Workbench][workbench], conéctese a la nueva base datos e importe la base de datos de WordPress.

	3. Actualice la base de datos para cambiar las entradas de dominio al nuevo dominio del sitio web de Azure. Por ejemplo, mywordpress.azurewebsites.net. Utilice [Buscar y reemplazar para script de bases de datos de WordPress][searchandreplace] para cambiar todas las instancias de forma segura.

4. Cree un nuevo sitio web y publique la copia de seguridad de WordPress.

	1. Cree un nuevo sitio web en el [Portal de administración de Azure][mgmtportal] con una base de datos utilizando **Nuevo** -> **Sitio web** -> **Creación personalizada**. Esto creará un sitio vacío.

	2. En la copia de seguridad de WordPress, busque el archivo **wp-config.php** y ábralo en un editor. Reemplace las entradas siguientes con la información de la nueva base de datos MySQL.

		* **DB_NAME**: nombre de usuario de la base de datos

		* **DB_USER**: nombre de usuario utilizado para tener acceso a la base de datos

		* **DB_PASSWORD**: contraseña de usuario

		Después de cambiar estas entradas, guarde y cierre el archivo **wp-config.php**.

	3. Use la información existente en [Implementación de un sitio web de Azure][deploy] para habilitar el método de implementación que desee usar y después implemente la copia de seguridad de WordPress en el sitio web de Azure.

5. Una vez implementado el sitio de WordPress, podrá acceder a nuevo sitio usando la dirección URL *.azurewebsite.net correspondiente al sitio.

###Configuración del sitio

Después de crear o migrar el sitio de WordPress, use la siguiente información para mejorar el rendimiento o habilitar funcionalidades adicionales.

Para hacer esto... | Use esto...
------------- | -----------
**Establecer el modo y el tamaño del sitio web y habilitar el escalado** | [Escalado de sitios web][websitescale]
**Habilitar las conexiones de base de datos persistentes** <p>De manera predeterminada, WordPress no utiliza las conexiones de base de datos persistentes, lo cual puede provocar que la conexión a la base de datos se vea limitada después de varias conexiones.</p>  | <ol><li><p>Edit the <strong>wp-includes/wp-db.php</strong> file.</p></li><li><p>Find the following line.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Replace the previous line with the following.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Find the following line.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Replace the above line with the following.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Save the file <strong>wp-includes/wp-db.php</strong> file and redeploy the site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>NOTA:</h5><p>estos cambios pueden sobrescribirse si se actualiza WordPress.</p><p>WordPress se establece de manera predeterminada en actualizaciones automáticas, lo cual se puede deshabilitar editando el archivo <strong>wp-config.php</strong> y agregando <code>la definición ('WP_AUTO_UPDATE_CORE' false);</code></p><p>Otra forma de direccionamiento de actualizaciones sería usar un Trabajo web que supervise el archivo <strong>wp-db.php</strong> y realice las modificaciones anteriores cada vez que este se actualice. Consulte <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Introducción a Trabajos web</a> para obtener más información.</p></div>
**Mejorar el rendimiento** | <ul><li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deshabilitar la cookie ARR</a>: puede mejorar el rendimiento cuando se ejecuta WordPress en varias instancias de sitio web</p></li><li><p>Habilitar almacenamiento en caché. <a href="http://msdn.microsoft.com/es-es/library/azure/dn690470.aspx">Caché de Redis</a> (vista previa) se puede utilizar con el <a href="https://wordpress.org/plugins/redis-object-cache/">Complemento de WordPress para caché de objeto de Redis</a>, o bien se puede utilizar otra de las ofertas de caché de la <a href="http://azure.microsoft.com/es-es/gallery/store/">Tienda de Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Cómo hacer que WordPress funcione más rápido con Wincache</a> - Wincache está habilitado de forma predeterminada para Sitios web</p></li><li><p><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/">Escale su sitio web Azure</a> y utilice el <a href="http://www.cleardb.com/developers/cdbr/introduction">Enrutamiento de alta disponibilidad de ClearDB</a> o el <a href="http://www.mysql.com/products/cluster/">CGE de MySQL Cluster</a></p></li></ul>
**Usar los blobs de almacenamiento** | <ol><li><p><a href="http://azure.microsoft.com/es-es/documentation/articles/storage-create-storage-account/">Crear una cuenta de Almacenamiento de Azure</a></p></li><li><p>Aprenda a <a href="http://azure.microsoft.com/es-es/documentation/articles/cdn-how-to-use/">usar la red de distribución de contenido (CDN)</a> para distribuir geográficamente los datos almacenados en blobs.</p></li><li><p>Instale y configure el <a href="https://wordpress.org/plugins/windows-azure-storage/">complemento del Almacenamiento de Azure para WordPress.</a>.</p><p>Para ver información detallada de instalación y configuración para el complemento, consulte la <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guía de usuario</a>.</p> </li></ol>
**Habilitar correo electrónico** | <ol><li><p><a href="http://azure.microsoft.com/es-es/gallery/store/sendgrid/sendgrid-azure/">Habilitar SendGrid mediante la Tienda Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Instale el complemento de SendGrid para WordPress</a></p></li></ol>
**Configurar un nombre de dominio personalizado** | [Utilice un nombre de dominio personalizado con un sitio web de Azure][customdomain]
**Habilitar HTTPS para un nombre de dominio personalizado** | [Use HTTPS con un sitio web de Azure][httpscustomdomain]
**Equilibrar la carga de su sitio o distribuirlo geográficamente** | [Enrutar el tráfico con el Administrador de tráfico][trafficmanager]. Si usa un dominio personalizado, consulte [Uso de un nombre de dominio personalizado con un sitio web de Azure][customdomain] para obtener información acerca del Administrador de tráfico con nombres de dominio personalizados
**Habilitar las copias de seguridad de sitio web automatizadas** | [Copia de seguridad de sitios web de Azure][backup]
**Habilitar el registro de diagnóstico** | [Habilitar el registro de diagnóstico para Sitios web][log]

##<a href="resources"></a>Recursos adicionales

* [Optimización de WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Conversión de un sitio de WordPress en un multisitio](http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [Asistente para actualización de ClearDB para Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hospedaje de WordPress en una subcarpeta de su sitio web de Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Paso a paso: creación de un sitio de WordPress con Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

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
[phpwebsite]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/es-es/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/es-es/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/es-es/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/es-es/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[Importación]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: (http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-web-site-gallery/)
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/es-es/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/es-es/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/es-es/documentation/articles/cdn-how-to-use/
