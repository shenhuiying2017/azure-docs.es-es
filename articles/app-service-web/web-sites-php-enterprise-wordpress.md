---
title: WordPress de clase empresarial en Azure | Microsoft Docs
description: "Obtenga información sobre cómo hospedar un sitio web de WordPress de clase empresarial en el Servicio de aplicaciones de Azure"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 21281955458a2632d96a91d884cab13803f4d296
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="enterprise-class-wordpress-on-azure"></a>WordPress de clase empresarial en Azure
Azure App Service proporciona un entorno escalable, seguro y fácil de usar para sitios de [WordPress][wordpress] críticos y a gran escala. El propio Microsoft ejecuta sitios de clase empresarial como los blogs de [Office][officeblog] y [Bing][bingblog]. Este documento muestra cómo usar la característica Web Apps de Microsoft Azure App Service para establecer y mantener un sitio de WordPress empresarial en la nube que pueda administrar un gran volumen de visitantes.

## <a name="architecture-and-planning"></a>Arquitectura y planeación
Una instalación básica de WordPress solamente tiene dos requisitos:

* **Base de datos MySQL**: este requisito está disponible a través de [ClearDB en Azure Marketplace][cdbnstore]. Como alternativa, puede administrar su propia instalación de MySQL en Azure Virtual Machines usando [Windows] [mysqlwindows] o [Linux][mysqllinux].

  > [!NOTE]
  > ClearDB proporciona varias configuraciones de MySQL. Cada configuración tiene diferentes características de rendimiento. Consulte [Azure Store][cdbnstore] para más información sobre las ofertas proporcionadas a través de dicha tienda o directamente en el [sitio web de ClearDB](http://www.cleardb.com/pricing.view).
  >
  >
* **PHP 5.2.4 o versiones posteriores**: Azure App Service proporciona actualmente las [versiones 5.4, 5.5 y 5.6 de PHP][phpwebsite].

  > [!NOTE]
  > Le recomendamos ejecutar siempre la versión más reciente de PHP para asegurarse de que dispone de las correcciones de seguridad más actuales.
  >
  >

### <a name="basic-deployment"></a>Implementación básica
Podría crear una solución básica dentro de una región de Azure tan solo con los requisitos básicos.

![Una aplicación web de Azure y una base de datos MySQL hospedadas en una sola región de Azure][basic-diagram]

Aunque esto podría permitirle crear varias instancias de Web Apps del sitio para escalar la aplicación horizontalmente, todo se hospeda dentro de los centros de datos en una región geográfica específica. Los visitantes de fuera de esta región podrían experimentar tiempos de respuesta lentos cuando usen el sitio. Si los centros de datos de esta región dejan de funcionar, también lo hace la aplicación.

### <a name="multi-region-deployment"></a>Implementación en varias regiones
Con [Traffic Manager][trafficmanager] de Azure, es posible escalar su sitio de WordPress en varias regiones geográficas y proporcionar solamente una dirección URL para todos los visitantes. Todos los visitantes entran a través de Traffic Manager y, después, se les dirige a una región en función de la configuración del equilibrio de carga.

![Una aplicación web de Azure, hospedada en varias regiones, con el enrutador de alta disponibilidad CDBR para enrutar a MySQL entre las regiones][multi-region-diagram]

Dentro de cada región, el sitio de WordPress se seguiría escalando entre varias instancias de Web Apps, pero este escalado es específico de la región. Las regiones con mucho tráfico se pueden escalar de forma diferente a las de poco tráfico.

Para replicar y enrutar el tráfico a varias bases de datos MySQL, puede usar [enrutadores de alta disponibilidad ClearDB (CDBR)] [ cleardbscale] (se muestran a la izquierda) o [MySQL Cluster Carrier Grade Edition (CGE)][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Implementación en varias regiones con almacenamiento multimedia y almacenamiento en caché
Si el sitio va a aceptar cargas u hospedar archivos multimedia, use Azure Blob Storage. Si necesita almacenamiento en caché, plantéese el uso de [Redis Cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) o una de las otras ofertas de almacenamiento en caché de [Azure Store](http://azure.microsoft.com/gallery/store/).

![Una aplicación web de Azure, hospedada en varias regiones, con el enrutador de alta disponibilidad CDBR para MySQL con Managed Cache, Blob Storage y Content Delivery Network][performance-diagram]

De forma predeterminada, el almacenamiento de blobs se distribuye geográficamente entre regiones, por lo que no tiene que preocuparse por la replicación de archivos entre todos los sitios. También puede habilitar [Azure Content Delivery Network][cdn] para Blob Storage, que distribuye archivos a los nodos extremos más próximos a los visitantes.

### <a name="planning"></a>Planificación
#### <a name="additional-requirements"></a>Requisitos adicionales
| Para hacer esto... | Use esto... |
| --- | --- |
| **Cargar o almacenar archivos grandes** |[Complemento de WordPress para usar Blob Storage][storageplugin] |
| **Enviar correo electrónico** |[SendGrid][storesendgrid] y el [complemento de WordPress para usar SendGrid][sendgridplugin] |
| **Nombres de dominio personalizados** |[Configuración de un nombre de dominio personalizado en Azure App Service][customdomain] |
| **HTTPS** |[Habilitar HTTPS para una aplicación web en Azure App Service][httpscustomdomain] |
| **Validación previa de la producción** |[Configuración de entornos de ensayo para aplicaciones web en Azure App Service][staging] <p>Cuando se pasa una aplicación web de ensayo a producción, también se pasa la configuración de WordPress. Asegúrese de que toda la configuración está actualizada conforme a los requisitos de la aplicación de producción antes de pasar la aplicación en ensayo a producción.</p> |
| **Supervisión y solución de problemas** |[Habilitación del registro de diagnóstico de aplicaciones web en Azure App Service][log] y [Supervisión de aplicaciones web en Azure App Service][monitor] |
| **Implementación de su sitio** |[Implementar una aplicación web en Azure App Service][deploy] |

#### <a name="availability-and-disaster-recovery"></a>Disponibilidad y recuperación ante desastres
| Para hacer esto... | Use esto... |
| --- | --- |
| **Sitios de equilibrio de carga** o **sitios distribuidos geográficamente** |[Enrutamiento del tráfico con Azure Traffic Manager][trafficmanager] |
| **Copia de seguridad y restauración** |[Copia de seguridad de una aplicación web en Azure App Service][backup] y [Restauración de una aplicación web en Azure App Service][restore] |

#### <a name="performance"></a>Rendimiento
El rendimiento en la nube se consigue principalmente mediante el almacenamiento en caché y el escalado horizontal. Sin embargo, deben tenerse en cuenta la memoria, el ancho de banda y otros atributos de hospedaje de aplicaciones web.

| Para hacer esto... | Use esto... |
| --- | --- |
| **Conocer las capacidades de las instancias del Servicio de aplicaciones** |[Detalles de precios, incluidas las funcionalidades de los niveles de App Service][websitepricing] |
| **Almacenar recursos en caché** |[Redis Cache][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) o una de las otras ofertas de almacenamiento en caché de [Azure Store](/gallery/store/) |
| **Escalar su aplicación** |[Escalar una aplicación web en Azure App Service][websitescale] y [Enrutamiento de alta disponibilidad de ClearDB][cleardbscale]. Si opta por hospedar y administrar su propia instalación de MySQL, debe plantearse el uso de [MySQL Cluster CGE][cge] para el escalado horizontal. |

#### <a name="migration"></a>Migración
Existen dos métodos para migrar un sitio de WordPress existente a Azure App Service:

* **[Exportación desde WordPress][export]**: este método exporta el contenido de su blog. Después, puede importar el contenido a un nuevo sitio de WordPress en Azure App Service mediante el [complemento Importador de WordPress][import].

  > [!NOTE]
  > Aunque este proceso permite migrar el contenido, no migra ningún complemento, tema u otras personalizaciones. Debe volver a instalar estos componentes manualmente.
  >
  >
* **Migración manual**: [realice una copia de seguridad del sitio][wordpressbackup] y la [base de datos][wordpressdbbackup] y, a continuación, restáurela manualmente en una aplicación web en Azure App Service y la base de datos MySQL asociada. Este método es útil para migrar sitios altamente personalizados, porque evita la tarea de instalar manualmente los complementos, los temas y otras personalizaciones.

## <a name="step-by-step-instructions"></a>Instrucciones paso a paso
### <a name="create-a-wordpress-site"></a>Creación de un sitio de WordPress
1. Use [Azure Marketplace][cdbnstore] para crear una base de datos MySQL del tamaño que identificó en la sección [Arquitectura y planificación](#planning), en las regiones en las que hospedará el sitio.
2. Siga los pasos en [Crear una aplicación web de WordPress en Azure App Service][createwordpress] para crear una aplicación web de WordPress. Cuando cree la aplicación web, seleccione **Usar una base de datos MySQL existente** y seleccione la base de datos que creó en el paso 1.

Si está migrando un sitio de WordPress existente, consulte [Migración de un sitio de WordPress existente a Azure](#Migrate-an-existing-WordPress-site-to-Azure) después de crear una nueva aplicación web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migración de un sitio de WordPress existente a Azure
Como se mencionó en la sección [Arquitectura y planificación](#planning), hay dos formas de migrar un sitio de WordPress:

* **Use la operación de exportación e importación** para sitios sin mucha personalización o donde solamente se desea mover el contenido.
* **Use la operación de copia de seguridad y restauración** para sitios con mucha personalización donde se desea mover todo.

Use una de las siguientes secciones para migrar el sitio.

#### <a name="the-export-and-import-method"></a>Método de exportación e importación
1. Use la [exportación de WordPress][export] para exportar el sitio existente.
2. Cree una aplicación web con los pasos de la sección [Creación de un sitio de WordPress](#Create-a-new-WordPress-site).
3. Inicie sesión en el sitio de WordPress en [Azure Portal][mgmtportal] y, a continuación, haga clic en **Complementos** > **Agregar nuevo**. Busque e instale el complemento **Importador de WordPress**.
4. Una vez instalado el complemento Importador de WordPress, haga clic en **Herramientas** > **Importar** y seleccione **WordPress** para usar el complemento Importador de WordPress.
5. En la página **Importar WordPress**, haga clic en **Elegir archivo**. Busque el archivo WXR exportado desde el sitio de WordPress existente y, a continuación, haga clic en **Cargar archivo e importar**.
6. Haga clic en **Enviar**. Se muestra un mensaje indicando que la importación se realizó correctamente.
7. Una vez realizados todos estos pasos, reinicie el sitio desde la hoja **App Services** de [Azure Portal][mgmtportal] correspondiente a la aplicación web.

Después de importar el sitio, puede que necesite realizar los pasos siguientes para habilitar la configuración no contenida en el archivo de importación.

| Si usaba esto... | Haga esto... |
| --- | --- |
| **Vínculos permanentes** |En el panel de WordPress del nuevo sitio, haga clic en **Configuración** > **Vínculos permanentes** y, después, actualice la estructura de vínculos permanentes. |
| **Vínculos de imagen y multimedia** |Para actualizar estos vínculos a la nueva ubicación, use el [complemento Update URLs de Velvet Blues][velvet], una herramienta de búsqueda y reemplazo, o hágalo manualmente en la base de datos. |
| **Temas** |Vaya a **Apariencia** > **Tema** y actualice el tema del sitio según corresponda. |
| **Menús** |Si el tema admite menús, los vínculos a la página principal todavía pueden tener el subdirectorio antiguo insertado en ellos. Vaya a **Apariencia** > **Menús** y, a continuación, actualícelos. |

#### <a name="the-backup-and-restore-method"></a>Método de copia de seguridad y restauración
1. Haga una copia de seguridad del sitio de WordPress existente usando la información que se encuentra en [Copias de seguridad de WordPress][wordpressbackup].
2. Haga una copia de seguridad de la base datos existente usando la información que se encuentra en [Copia de seguridad de su base de datos][wordpressdbbackup].
3. Cree una base de datos y restaurare la copia de seguridad.

   1. Compre una nueva base de datos en [Azure Marketplace][cdbnstore] o configure una base de datos MySQL en una máquina virtual [Windows][mysqlwindows] o [Linux][mysqllinux].
   2. Use un cliente MySQL, como [MySQL Workbench][workbench], para conectarse a la nueva base de datos e importe la base de datos de WordPress.
   3. Actualice la base de datos para cambiar las entradas de dominio al nuevo dominio de Azure App Service. Use el [script de búsqueda y reemplazo para bases de datos de WordPress][searchandreplace] para cambiar todas las instancias de forma segura.
4. Cree una aplicación web en Azure Portal y publique la copia de seguridad de WordPress.

   1. Para crear una aplicación web que tenga una base de datos, en [Azure Portal][mgmtportal], haga clic en **Nuevo** > **Web y móvil** > **Azure Marketplace** > **Web Apps** > **Aplicación web + SQL** (o **Aplicación web + MySQL**) > **Crear**. Configure todos los ajustes necesarios para crear una aplicación web vacía.
   2. En la copia de seguridad de WordPress, busque el archivo **wp-config.php** y ábralo en un editor. Reemplace las entradas siguientes con la información de la nueva base de datos MySQL:

      * **DB_NAME**: nombre de usuario de la base de datos.
      * **DB_USER**: nombre de usuario que se usa para acceder a la base de datos.
      * **DB_PASSWORD**: contraseña del usuario.

        Después de cambiar estas entradas, guarde y cierre el archivo **wp-config.php**.
   3. Use la información existente en [Implementación de una aplicación web en Azure App Service][deploy] para habilitar el método de implementación que desee usar y, después, implemente la copia de seguridad de WordPress en la aplicación web en Azure App Service.
5. Una vez implementado el sitio de WordPress, debería tener acceso al sitio nuevo (como una aplicación web de App Service) usando la dirección URL *.azurewebsite.net correspondiente al sitio.

### <a name="configure-your-site"></a>Configuración del sitio
Después de crear o migrar el sitio de WordPress, use la siguiente información para mejorar el rendimiento o habilitar funcionalidades adicionales.

| Para hacer esto... | Use esto... |
| --- | --- |
| **Establecer el tamaño y el modo del plan de Servicio de aplicaciones y habilitar el escalado** |[Escalado de una aplicación web en Azure App Service][websitescale]. |
| **Habilitar conexiones de base de datos persistentes** |De forma predeterminada, WordPress no usa conexiones de base de datos persistentes, lo que puede provocar que la conexión con la base de datos se limite después de varias conexiones. Para habilitar las conexiones persistentes, instale el complemento [Persistent database connection updater](https://wordpress.org/plugins/persistent-database-connection-updater/installation/). |
| **Mejorar el rendimiento** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">Deshabilitar la cookie ARR</a> puede mejorar el rendimiento cuando WordPress se ejecuta en varias instancias de Web Apps.</p></li><li><p>Activación del almacenamiento en caché. Se puede usar <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis Cache</a> (versión preliminar) con el <a href="https://wordpress.org/plugins/redis-object-cache/">complemento de WordPress para caché de objetos en Redis</a>, o se puede usar una de las otras ofertas de almacenamiento en caché de <a href="/gallery/store/">Azure Store</a>.</p></li><li><p>[Agilizar WordPress con Wincache](https://wordpress.org/plugins/w3-total-cache/). Wincache está habilitado de forma predeterminada para aplicaciones web. Cuando se usan conjuntamente WinCache y memoria caché dinámica, desactive la memoria caché de archivos de WinCache, pero deje el usuario y la caché de sesión habilitados. Para desactivar la memoria caché de archivos, en un archivo .ini de nivel de sistema, establezca el valor siguiente:<br/><code>wincache.fcenabled = 0</code></p></li><li><p>[Escalar una aplicación web en Azure App Service][websitescale] y usar <a href="http://www.cleardb.com/developers/cdbr/introduction">Enrutamiento de alta disponibilidad ClearDB</a> o <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>.</p></li></ul> |
| **Usar blobs para almacenamiento** |<ol><li><p>[Creación de una cuenta de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).</p></li><li><p>Obtenga información acerca del [Uso de la Red de entrega de contenido](../cdn/cdn-create-new-endpoint.md) para distribuir geográficamente datos almacenados en blobs.</p></li><li><p>Instale y configure el <a href="https://wordpress.org/plugins/windows-azure-storage/">complemento Azure Storage para WordPress</a>.</p><p>Para más información detallada acerca de la instalación y configuración del complemento, consulte la <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guía del usuario</a>.</p> </li></ol> |
| **Habilitar correo electrónico** |Habilitar <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> mediante Azure Store. Instalar el <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">complemento SendGrid</a> para WordPress. |
| **Configuración de un nombre de dominio personalizado** |[Configuración de un nombre de dominio personalizado en Azure App Service][customdomain]. |
| **Habilitar HTTPS para un nombre de dominio personalizado** |[Habilitar HTTPS para una aplicación web en Azure App Service][httpscustomdomain]. |
| **Equilibrio de carga o distribución geográfica para el sitio** |[Enrutamiento del tráfico con Azure Traffic Manager][trafficmanager]. Si está usando un dominio personalizado, consulte [Configuración de un nombre de dominio personalizado en Azure App Service][customdomain] para obtener información sobre el uso de Traffic Manager con nombres de dominio personalizados. |
| **Activación de las copias de seguridad automatizadas** |[Copia de seguridad de una aplicación web en Azure App Service][backup]. |
| **Activación del registro de diagnósticos** |[Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service][log]. |

## <a name="next-steps"></a>Pasos siguientes
* [Optimización de WordPress](http://codex.wordpress.org/WordPress_Optimization)
* [Conversión de WordPress en multisitios en Azure App Service](web-sites-php-convert-wordpress-multisite.md)
* [Asistente para actualización de ClearDB para Azure](http://www.cleardb.com/store/azure/upgrade)
* [Hospedaje de WordPress en una subcarpeta de la aplicación web en Servicio de aplicaciones de Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [Paso a paso: Creación de un sitio de WordPress mediante Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [Hospedaje de un blog existente de WordPress en Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [Habilitación de vínculos permanentes descriptivos en WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [Migración y ejecución del blog de WordPress en Servicio de aplicaciones de Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [Ejecución de WordPress en Servicio de aplicaciones de Azure gratuitamente](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [WordPress en Azure en menos de dos minutos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [Mover un blog de WordPress a Azure, parte 1: creación de un blog de WordPress en Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [Mover un blog de WordPress a Azure, parte 2: transferencia del contenido](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [Mover un blog de WordPress a Azure, parte 3: configuración del dominio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [Mover un blog de WordPress a Azure, parte 4: vínculos permanentes descriptivos y reglas de reescritura de direcciones URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [Mover un blog de WordPress a Azure, parte 5: migración de una subcarpeta a la raíz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [Configuración de una aplicación web de WordPress en la cuenta de Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [Sustentación de WordPress en Azure](http://www.johnpapa.net/wordpress-on-azure/)
* [Sugerencias para WordPress en Azure](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> Si desea empezar a trabajar con Azure App Service antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba de Azure App Service](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en App Service. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
>
>

## <a name="whats-changed"></a>Lo que ha cambiado
Para obtener una guía del cambio de Websites a App Service, consulte [Azure App Service y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- URL List -->

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
[customdomain]: app-service-web-tutorial-custom-domain.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: app-service-web-tutorial-custom-ssl.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
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
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

