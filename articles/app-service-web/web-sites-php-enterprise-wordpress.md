<properties 
	pageTitle="WordPress de clase empresarial en el Servicio de aplicaciones de Azure" 
	description="Obtenga información sobre cómo hospedar un sitio web de WordPress de clase empresarial en el Servicio de aplicaciones de Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

# WordPress de clase empresarial en el Servicio de aplicaciones de Azure

El Servicio de aplicaciones de Azure proporciona un entorno escalable, seguro y fácil de usar para sitios de [WordPress][wordpress] críticos y a gran escala. El propio Microsoft ejecuta sitios de clase empresarial, como los blogs de [Office][officeblog] y de [Bing][bingblog]. Este documento muestra cómo puede usar Aplicaciones web del Servicio de aplicaciones de Azure para establecer y mantener un sitio de WordPress basado en la nube y de clase empresarial que pueda administrar un gran volumen de visitantes.

## Arquitectura y planeación

Una instalación básica de WordPress solamente tiene dos requisitos.

* **Base de datos MySQL**, disponible a través de [ClearDB en la Tienda de Azure][cdbnstore], o bien puede administrar su propia instalación de MySQL en máquinas virtuales de Azure usando [Windows][mysqlwindows] o [Linux][mysqllinux].

    > [AZURE.NOTE] ClearDB proporciona varias configuraciones de MySQL, cada una de ellas con diferentes características de rendimiento. Consulte la [Tienda de Azure][cdbnstore] para obtener información sobre las ofertas proporcionadas a través de dicha tienda o los [precios de ClearDB](http://www.cleardb.com/pricing.view) para conocer las ofertas directamente desde ClearDB.
    
* **PHP 5.2.4 o superior**, Servicio de aplicaciones de Azure proporciona actualmente [las versiones 5.3, 5.4 y 5.5 de PHP][phpwebsite].

	> [AZURE.NOTE] Le recomendamos ejecutar siempre la versión más reciente de PHP para asegurarse de que dispone de las correcciones de seguridad más actuales.

### Implementación básica

Usando simplemente los requisitos básicos, podría crear una solución básica dentro de una región de Azure.

![una aplicación web de Azure y Base de datos MySQL hospedadas en una sola región de Azure][basic-diagram]

Aunque esto podría permitirle escalar la aplicación horizontalmente creando varias instancias de Aplicaciones web del sitio, todo se hospeda dentro de los centros de datos en una región geográfica específica. Los visitantes que se encuentran fuera de esta región pueden experimentar tiempos de respuesta lentos cuando usen el sitio y si los centros de datos de esta región dejan de funcionar, también lo hará su aplicación.


### Implementación en varias regiones

Mediante el [Administrador de tráfico][trafficmanager] de Azure, es posible escalar su sitio de WordPress en varias regiones geográficas proporcionando solamente una dirección URL para los visitantes. Todos los visitantes entran a través del Administrador de tráfico y, después, se les dirige a una región en función de la configuración del equilibrio de carga.

![una aplicación web de Azure, hospedada en varias regiones, con el enrutador de alta disponibilidad CDBR para enrutar a MySQL entre las regiones][multi-region-diagram]

Dentro de cada región, el sitio de WordPress se seguiría escalando entre varias instancias de Aplicaciones web, pero este escalamiento es específico de la región; las regiones con mucho tráfico se pueden escalar de forma diferente a las de poco tráfico.

La replicación y el enrutamiento a varias bases de datos MySQL se puede hacer usando el [enrutador de alta disponibilidad CDBR][cleardbscale] (mostrado a la izquierda) o [CGE de MySQL Cluster][cge]. 

### Implementación en varias regiones con almacenamiento multimedia y almacenamiento en caché

Si el sitio va a aceptar cargas u hospedar archivos multimedia, use almacenamiento de blogs de Azure. Si necesita almacenamiento en caché, considere usar [Caché en Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) o una de las otras ofertas de almacenamiento en caché de la [Tienda de Azure](http://azure.microsoft.com/gallery/store/).

![una aplicación web de Azure, hospedada en varias regiones, con el enrutador de alta disponibilidad CDBR para MySQL con memoria caché administrada, el almacenamiento de blobs y CDN][performance-diagram]

De forma predeterminada, el almacenamiento de blobs se distribuye geográficamente entre regiones, por lo que no tiene que preocuparse por la replicación de archivos entre todos los sitios. También puede habilitar la [Red de distribución de contenido (CDN)][cdn] de Azure para el almacenamiento de blobs, que distribuye archivos a los nodos finales más cercanos a los visitantes.

### Planeación

#### Requisitos adicionales

Para hacer esto... | Use esto...
------------------------ |-----------
**Cargar o almacenar archivos de gran tamaño** | [complemento de WordPress para usar el almacenamiento de blobs][storageplugin]
**Enviar correo electrónico** | [SendGrid][storesendgrid] y el [complemento de WordPress para usar SendGrid][sendgridplugin]
**Personalizar nombres de dominio** | [configure un nombre de dominio personalizado en Servicio de aplicaciones de Azure][customdomain]
**HTTPS** | [habilite HTTPS para una aplicación web en Servicio de aplicaciones de Azure][httpscustomdomain]
**Validación de preproducción** | [configure entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure][staging] <p>Tenga en cuenta que el cambio de una aplicación web de ensayo a producción también mueve la configuración de WordPress. Debe asegurarse de que toda la configuración está actualizada a los requisitos para la aplicación de producción antes de cambiar la aplicación en ensayo a producción.</p> 
**Supervisión y solución de problemas** | [habilite el registro de diagnóstico para aplicaciones web en Servicio de aplicaciones de Azure][log] y [supervise Aplicaciones web en el Servicio de aplicaciones de Azure][monitor]
**Implementar el sitio** | [implemente una aplicación web en el Servicio de aplicaciones de Azure][deploy]

#### Disponibilidad y recuperación ante desastres

Para hacer esto... | Use esto...
------------------------|-----------
**Equilibrar la carga de los sitios** o **distribuir geográficamente los sitios** | [enrute el tráfico con Administrador de tráfico de Azure][trafficmanager]
**Crear copia de seguridad y restaurar** | [Cree una copia de seguridad de una aplicación web en Servicio de aplicaciones de Azure][backup] y [restaure una aplicación web en Servicio de aplicaciones de Azure][restore]

#### Rendimiento

El rendimiento en la nube se logra principalmente mediante almacenamiento en caché y escalamiento horizontal; sin embargo, la memoria, el ancho de banda y otros atributos del hospedaje de Aplicaciones web también debe tenerse en cuenta.

Para hacer esto... | Use esto...
------------------------|-----------
**Comprender funcionalidades de instancia de Servicio de aplicaciones** | [detalles de precio, incluidas las funcionalidades de los niveles de Servicio de aplicaciones][websitepricing]
**Almacenar recursos en caché** | [Caché en Redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) o una de las otras ofertas de almacenamiento en caché de la [Tienda de Azure](/gallery/store/)
**Escalar la aplicación** | [escale una aplicación web en Servicio de aplicaciones de Azure][websitescale] y [enrutamiento de alta disponibilidad de ClearDB][cleardbscale]. Si opta por hospedar y administrar su propia instalación de MySQL, debe considerar el uso de [CGE de MySQL Cluster][cge] para el escalamiento horizontal

#### Migración

Existen dos métodos para migrar un sitio de WordPress existente a Servicio de aplicaciones de Azure.

* **[Exportación de WordPress][export]**: exporta el contenido de su blog, el que luego se puede importar a un sitio de WordPress nuevo en Servicio de aplicaciones de Azure con el [complemento de importador de WordPress][import].

	> [AZURE.NOTE] Aunque este proceso permite migrar el contenido, no migra ningún complemento, tema u otras personalizaciones. Estos se deben instalar de nuevo manualmente.

* **Migración manual**: [cree una copia de seguridad de su sitio][wordpressbackup] y su base de datos [database][wordpressdbbackup] y, a continuación, restaure esa copia de manera manual en una aplicación web en Servicio de aplicaciones de Azure y una base de datos MySQL asociada para migrar sitios altamente personalizados y evitar. la tarea de instalar manualmente complementos, temas y otras personalizaciones.

## Instrucciones paso a paso

### Creación de un sitio de WordPress

1. Use la [Tienda de Azure][cdbnstore] para crear una base de datos MySQL del tamaño que identificó en la sección [Arquitectura y planificación](#planning) , en las regiones en que hospedará su sitio.

2. Siga los pasos en [Crear una aplicación web de WordPress en Servicio de aplicaciones de Azure][createwordpress] para crear una aplicación web de WordPress nueva. Cuando cree la aplicación web, seleccione **Usar una Base de datos MySQL existente** y seleccione la base de datos que se creó en el paso 1.

Si migra un sitio de WordPress existente, consulte [Migración de un sitio de WordPress existente a Azure](#Migrate-an-existing-WordPress-site-to-Azure) después de crear una nueva aplicación web.

### Migración de un sitio de WordPress existente a Azure

Como se ha mencionado en la sección [Arquitectura y planificación](#planning) , hay dos formas de migrar un sitio web de WordPress.

* **exportación e importación**, para sitios sin mucha personalización o donde solamente se desea mover el contenido.

* **copia de seguridad y restauración**, para sitios con mucha personalización donde se desea mover todo.

Use una de las siguientes secciones para migrar el sitio.

#### Método de exportación e importación

1. Utilice [exportación de WordPress][export] para exportar el sitio existente.

2. Cree una aplicación web nueva usando los pasos en la sección [Crear un nuevo sitio WordPress](#Create-a-new-WordPress-site) .

3. Inicie sesión en el sitio de WordPress en Aplicaciones web y haga clic en **Complementos** -> **Agregar nuevo**. Busque e instale el complemento **Importador de WordPress**.

4. Una vez instalado el complemento del importador, haga clic en **Herramientas** -> **Importar** y, a continuación, seleccione **WordPress** para usar el complemento Importador de WordPress.

5. En la página **Importar WordPress**, haga clic en **Elegir archivo**. Busque el archivo WXR exportado desde el sitio de WordPress existente y, a continuación, elija **Cargar archivo e importar**.

6. Haga clic en **Enviar**. Se mostrará un mensaje indicando que la importación se realizó correctamente.

8. Una vez que haya realizado todos estos pasos, reinicie el sitio desde la hoja del [Portal de Azure][mgmtportal] correspondiente a la aplicación web.

Después de importar el sitio, puede que necesite realizar los pasos siguientes para habilitar la configuración no contenida en el archivo de importación.

Si usaba esto... | Haga esto...
------------------ | ----------
**Vínculos permanentes** | en el panel de WordPress del sitio nuevo, haga clic en **Configuración** -> **Vínculos permanentes** y, a continuación, actualice la estructura de vínculos permanentes
**vínculos imágenes/multimedia** | para actualizar los vínculos a la nueva ubicación, utilice el [complemento Update URLs de Velvet Blues][velvet], una herramienta de búsqueda y reemplazo o de manera manual en la base de datos
**Temas** | vaya a **Apariencia** -> **Tema** y actualice el tema del sitio según corresponda
**Menús** | si el tema admite menús, los vínculos a la página principal todavía pueden tener el subdirectorio antiguo incrustados en ellos. Vaya a **Apariencia** -> **Menús** y actualícelos

#### Método de copia de seguridad y restauración

1. Cree una copia de seguridad del sitio de WordPress existente usando la información que se encuentra en [Copias de seguridad de WordPress][wordpressbackup].

2. Cree una copia de seguridad de la base de datos existente con la información que se encuentra en [Copia de seguridad de la base de datos][wordpressdbbackup].

3. Cree una nueva base de datos y restaurare la copia de seguridad.

	1. Compre una base de datos nueva en la [Tienda de Azure][cdbnstore] o configure una base de datos MySQL en una máquina virtual de [Windows][mysqlwindows] o [Linux][mysqllinux].

	2. Con un cliente MySQL como [MySQL Workbench][workbench], conéctese a la base de datos nueva e importe la base de datos de WordPress.

	3. Actualice la base de datos para cambiar las entradas de dominio al nuevo dominio de Servicio de aplicaciones de Azure. Ejemplo: mywordpress.azurewebsites.net. Use el [script de búsqueda y reemplazo para las bases de datos de WordPress][searchandreplace] para cambiar todas las instancias de manera segura.

4. Cree una aplicación web nueva en el Portal de Azure y publique la copia de seguridad de WordPress.

	1. Cree una aplicación web nueva en el [Portal de Azure][mgmtportal] con una base de datos usando **Nuevo** -> **Web + móvil** -> **Azure Marketplace** -> **Aplicaciones web** -> **Aplicación web + SQL** (o **Aplicación web + MySQL**) -> **Crear**. Configure todos los ajustes necesarios para crear una aplicación web vacía.

	2. En la copia de seguridad de WordPress, ubique el archivo **wp-config.php** y ábralo en un editor. Reemplace las entradas siguientes con la información de la nueva base de datos MySQL.

		* **DB_NAME**: el nombre de usuario de la base de datos

		* **DB_USER**: el nombre de usuario que se usa para tener acceso a la base de datos

		* **DB_PASSWORD**: la contraseña del usuario

		Después de cambiar estas entradas, guarde y cierre el archivo **wp-config.php**.

	3. Use la información existente en [Implementación de una aplicación web en Servicio de Aplicaciones de Azure][deploy] para habilitar el método de implementación que desee usar y después implemente la copia de seguridad de WordPress en la aplicación web en Servicio de aplicaciones de Azure.

5. Una vez implementado el sitio de WordPress, debiera poder tener acceso al sitio nuevo (como una aplicación web del Servicio de aplicaciones) usando la dirección URL *.azurewebsite.net correspondiente al sitio.

### Configuración del sitio

Después de crear o migrar el sitio de WordPress, use la siguiente información para mejorar el rendimiento o habilitar funcionalidades adicionales.

Para hacer esto... | Use esto...
------------- | -----------
**Establecer el tamaño y el modo del plan de Servicio de aplicaciones y habilitar el escalamiento** | [escale una aplicación web en Servicio de aplicaciones de Azure][websitescale]
**Habilitar conexiones de base de datos persistentes** <p>De forma predeterminada, WordPress no usa conexiones de base de datos persistentes, lo que puede provocar que la conexión con la base de datos se limite después de varias conexiones.</p>  | <ol><li><p>Edite el archivo <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Busque la línea siguiente.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Reemplace la línea anterior por la siguiente.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Busque la línea siguiente.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Reemplace la línea anterior por la siguiente.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Guarde el archivo <strong>wp-includes/wp-db.php</strong> y vuelva a implementar el sitio.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>Estos cambios se pueden sobrescribir cuando se actualiza WordPress.</p><p>WordPress tiene activadas las actualizaciones automáticas de manera predeterminada, las que se pueden deshabilitar editando el archivo <strong>wp-config.php</strong> y agregando el código <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Otra forma de administrar las actualizaciones sería usar un WebJob que supervise el archivo <strong>wp-db.php</strong> y realice las modificaciones anteriores cada vez que se actualiza el archivo. Consulte <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Introducción a WebJobs</a> para obtener más información.</p></div>
**Mejorar el rendimiento** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deshabilitar la cookie ARR</a>: es posible mejorar el rendimiento cuando se ejecute WordPress en varias instancias de Aplicaciones web</p></li><li><p>Activación del almacenamiento en caché. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Caché en Redis</a> (vista previa) se puede usar con el <a href="https://wordpress.org/plugins/redis-object-cache/">complemento de WordPress para caché de objetos en Redis</a> o usar una de las otras ofertas de caché de la <a href="/gallery/store/">Tienda de Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Hacer que WordPress sea más rápido con Wincache</a>: Wincache está habilitado de forma predeterminada para Aplicaciones web</p></li><li><p><a href="../web-sites-scale/">Escalar una aplicación web en Servicio de aplicaciones de Azure</a> y usar <a href="http://www.cleardb.com/developers/cdbr/introduction">Enrutamiento de alta disponibilidad de ClearDB</a> o <a href="http://www.mysql.com/products/cluster/">CGE de MySQL Cluster</a></p></li></ul>
**Usar blobs para almacenamiento** | <ol><li><p><a href="../storage-create-storage-account/">Crear una cuenta de Almacenamiento de Azure</a></p></li><li><p>Obtenga información sobre el <a href="../cdn-how-to-use/">Uso de la Red de distribución de contenido(CDN)</a> para distribuir geográficamente los datos almacenados en blobs.</p></li><li><p>Instale y configure el <a href="https://wordpress.org/plugins/windows-azure-storage/">complemento de Almacenamiento de Azure para WordPress</a>.</p><p>Para obtener información detallada sobre la instalación y configuración del complemento, consulte la <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guía del usuario</a>.</p> </li></ol>
**Habilitar correo electrónico** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Habilitar SendGrid con la Tienda de Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Instalar el complemento de SendGrid para WordPress</a></p></li></ol>
**Configurar un nombre de dominio personalizado** | [configure un nombre de dominio personalizado en Servicio de aplicaciones de Azure][customdomain]
**Habilitar HTTPS para un nombre de dominio personalizado** | [habilite HTTPS para una aplicación web en Servicio de aplicaciones de Azure][httpscustomdomain]
**Equilibrar la carga del sitio o distribuirlo geográficamente** | [enrute el tráfico con Administrador de tráfico de Azure][trafficmanager]. Si está usando un dominio personalizado, consulte [Configurar un nombre de dominio personalizado en Servicio de aplicaciones de Azure][customdomain] para obtener información sobre el uso de Administrador de tráfico con nombres de dominio personalizados
**Habilitar copias de seguridad automatizadas** | [cree una copia de seguridad de una aplicación web en Servicio de aplicaciones de Azure][backup]
**Habilitar registro de diagnóstico** | [habilite el registro de diagnóstico para aplicaciones web en Servicio de aplicaciones de Azure][log]

## Pasos siguientes

* [Optimización de WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Conversión de WordPress en multisitios en Servicio de aplicaciones de Azure](web-sites-php-convert-wordpress-multisite.md)

* [Asistente para actualización de ClearDB para Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hospedaje de WordPress en una subcarpeta de la aplicación web en Servicio de aplicaciones de Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Paso a paso: Creación de un sitio web de WordPress con Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hospedaje de un blog existente de WordPress en Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Habilitación de vínculos permanentes descriptivos en WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Migración y ejecución del blog de WordPress en Servicio de aplicaciones de Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Ejecución de WordPress en Servicio de aplicaciones de Azure gratuitamente](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress en Azure en 2 minutos o menos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Mover un blog de WordPress a Azure, parte 1: creación de un blog de WordPress en Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Mover un blog de WordPress a Azure, parte 2: transferencia del contenido](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Mover un blog de WordPress a Azure, parte 3: configuración del dominio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Mover un blog de WordPress a Azure, parte 4: vínculos permanentes descriptivos y reglas de reescritura de direcciones URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Mover un blog de WordPress a Azure, parte 5: migración de una subcarpeta a la raíz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Configuración de una aplicación web de WordPress en la cuenta de Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Sustentación de WordPress en Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Sugerencias para WordPress en Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web inicial de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía para cambiar de sitios web al servicio de aplicaciones, consulte: [El servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía para cambiar del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: ../virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../install-configure-powershell.md
[xplat-cli]: ../xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: ../cdn-how-to-use.md

<!--HONumber=52--> 