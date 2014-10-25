<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## WordPress de clase empresarial en Sitios web Azure

Sitios web Azure proporciona un entorno escalable, seguro y fácil de usar para sitios de [WordPress][WordPress] de gran escala y críticos. El propio Microsoft ejecuta sitios de clase empresarial como los blogs de [Office][Office] y [Bing][Bing]. Este documento muestra cómo puede usar Sitios web Azure para establecer y mantener un sitio de WordPress basado en la nube y de clase empresarial que pueda administrar un gran volumen de visitantes.

## En este artículo

-   [Arquitectura y planeación][Arquitectura y planeación] - Conozca las consideraciones sobre arquitectura, requisitos y rendimiento antes de crear su sitio.

-   [Cómo][Cómo] - Cree, implemente y configure su sitio.

-   [Más recursos][Más recursos] - Recursos e información adicionales.

## <span id="plan"></span></a>Arquitectura y planeación

Una instalación básica de WordPress solamente tiene dos requisitos.

-   **Base de datos MySQL**, disponible a través de [ClearDB en la Tienda de Azure][ClearDB en la Tienda de Azure], o bien puede administrar su propia instalación de MySQL en máquinas virtuales de Azure usando [Windows][Windows] o [Linux][Linux].

    > [WACOM.NOTE] ClearDB proporciona varias configuraciones de MySQL, cada una de ellas con diferentes características de rendimiento. Consulte la [Tienda de Azure][ClearDB en la Tienda de Azure] para obtener información sobre las ofertas proporcionadas a través de dicha tienda o los [precios de ClearDB][precios de ClearDB] para conocer las ofertas directamente desde ClearDB.

-   **PHP 5.2.4 o superior** - Sitios web Azure actualmente proporciona [las versiones 5.3, 5.4 y 5.5 de PHP][las versiones 5.3, 5.4 y 5.5 de PHP].

    > [WACOM.NOTE] Le recomendamos ejecutar siempre la versión más reciente de PHP para asegurarse de que dispone de las correcciones de seguridad más actuales.

### Implementación básica

Usando simplemente los requisitos básicos, podría crear una solución básica dentro de una región de Azure.

![an Azure Website and MySQL Database hosted in a single Azure region][an Azure Website and MySQL Database hosted in a single Azure region]

Aunque esto podría permitirle escalar la aplicación horizontalmente creando varias instancias del sitio web, todo se hospeda dentro de los centros de datos en una región geográfica específica. Los visitantes que se encuentran fuera de esta región pueden experimentar tiempos de respuesta lentos cuando usen el sitio y si los centros de datos de esta región dejan de funcionar, también lo hará su aplicación.

### Implementación en varias regiones

Mediante el [Administrador de tráfico][Administrador de tráfico] de Azure, es posible escalar su sitio de WordPress en varias regiones geográficas proporcionando solamente una dirección URL para los visitantes. Todos los visitantes entran a través del Administrador de tráfico y, después, se les dirige a una región en función de la configuración del equilibrio de carga.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions]

Dentro de cada región, el sitio de WordPress se seguiría escalando entre varias instancias del sitio web, pero este escalamiento es específico de la región; las regiones con mucho tráfico se pueden escalar de forma diferente a las de poco tráfico.

La replicación y el enrutamiento a varias bases de datos MySQL se puede hacer usando el [enrutador de alta disponibilidad CDBR][enrutador de alta disponibilidad CDBR] de ClearDB (mostrado la izquierda) o [CGE de MySQL Cluster][CGE de MySQL Cluster].

### Implementación en varias regiones con almacenamiento multimedia y almacenamiento en caché

Si el sitio va a aceptar cargas u hospedar archivos multimedia, use almacenamiento de blogs de Azure. Si necesita almacenamiento en caché, plantéese el uso de [Caché en Redis][Caché en Redis], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] o una de las otras ofertas de almacenamiento en caché de la [Tienda de Azure][Tienda de Azure].

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN]

De forma predeterminada, el almacenamiento de blobs se distribuye geográficamente entre regiones, por lo que no tiene que preocuparse por la replicación de archivos entre todos los sitios. También puede habilitar la [Red de entrega de contenido (CDN)][Red de entrega de contenido (CDN)] de Azure para almacenamientos de blobs, que distribuye archivos a los nodos extremo más próximos a los visitantes.

### Planeación

#### Requisitos adicionales

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Para hacer esto...</th>
<th align="left">Use esto...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Cargar o almacenar archivos grandes</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">Complemento de WordPress para usar almacenamiento de blobs</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Enviar correo electrónico</strong></td>
<td align="left"><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> y el <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">complemento de WordPress para usar SendGrid</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Nombres de dominio personalizados</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/">Nombres de dominio personalizados con Sitios web Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/">Compatibilidad con HTTPS en Sitios web Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Validación previa de la producción</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-staged-publishing/">Compatibilidad con la publicación de ensayo para Sitios web Azure</a>
<p>Tenga en cuenta que el cambio de un sitio de ensayo a producción también mueve la configuración de WordPress. Debe asegurarse de que toda la configuración está actualizada a los requisitos para el sitio de producción antes de cambiar el sitio de ensayo a producción.</p></td>
</tr>
<tr class="even">
<td align="left"><strong>Supervisión y solución de problemas</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-enable-diagnostic-log/">Registro de diagnósticos con Sitios web Azure</a> y <a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-monitor/">Supervisión de Sitios web Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Implementación de su sitio</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-deploy/">Implementación de un sitio web de Azure</a></td>
</tr>
</tbody>
</table>

#### Disponibilidad y recuperación ante desastres

| Para hacer esto...                                                          | Use esto...                                                                                   |
|-----------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| **Sitios de equilibrio de carga** o **sitios distribuidos geográficamente** | [Enrutamiento del tráfico con el Administrador de tráfico de Azure][Administrador de tráfico] |
| **Copia de seguridad y restauración**                                       | [Copias de seguridad de Sitios web Azure][Copias de seguridad de Sitios web Azure] y [Restauración de un sitio web de Azure][Restauración de un sitio web de Azure]       |

#### Rendimiento

El rendimiento en la nube se logra principalmente mediante almacenamiento en caché y escalamiento horizontal; sin embargo, la memoria, el ancho de banda y otros atributos del hospedaje de sitios web también debe tenerse en cuenta.

| Para hacer esto...                                          | Use esto...                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Conocer las capacidades de las instancias de sitios web** | [Detalles de precios, incluidas las capacidades de tamaños y modos de los sitios web][Detalles de precios, incluidas las capacidades de tamaños y modos de los sitios web]                                                                                                                                                                                                 |
| **Almacenar recursos en caché**                             | [Caché en Redis][Caché en Redis], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] o una de las otras ofertas de almacenamiento en caché de la [Tienda de Azure][Tienda de Azure]                                                                                                                                                  |
| **Escalar su aplicación**                                   | [Escalamiento de un sitio web de Azure][Escalamiento de un sitio web de Azure] y [Enrutamiento de alta disponibilidad de ClearDB][enrutador de alta disponibilidad CDBR]. Si opta por hospedar y administrar su propia instalación de MySQL, debe plantearse el uso de [CGE de MySQL Cluster][CGE de MySQL Cluster] para escalamiento horizontal |

#### Migración

Existen dos métodos para migrar un sitio de WordPress existente a Sitios web Azure.

-   **[Exportación de WordPress][Exportación de WordPress]** - Mediante este método se exporta el contenido del blog, que después se puede importar a un nuevo sitio de WordPress en Azure usando el [complemento Importador de WordPress][complemento Importador de WordPress].

    > [WACOM.NOTE] Aunque este proceso permite migrar el contenido, no migra ningún complemento, tema u otras personalizaciones. Estos se deben instalar de nuevo manualmente.

-   **Migración manual** - [Haga una copia de seguridad del sitio][Haga una copia de seguridad del sitio] y la [base de datos][base de datos] y después restáurelo manualmente en un sitio web de Azure y la base de datos MySQL asociada para migrar sitios altamente personalizados y evitar la molestia de instalar complementos, temas y otras personalizaciones manualmente.

## Cómo

### <span id="create"></span></a>Creación de un sitio de WordPress

1.  Use la [Tienda de Azure][ClearDB en la Tienda de Azure] para crear una base de datos MySQL del tamaño que identificó en la sección [Arquitectura y planeación][Arquitectura y planeación], en las regiones en las que hospedará el sitio.

2.  Siga los pasos de [Creación de un sitio web de WordPress desde la Galería de Azure][Creación de un sitio web de WordPress desde la Galería de Azure] para crear un nuevo sitio de WordPress. Cuando cree el sitio, seleccione **Usar una base de datos MySQL existente** y, después, seleccione la base datos creada en el paso 1.

Si está migrando un sitio de WordPress existente, consulte [Migración de un sitio de WordPress existente][Migración de un sitio de WordPress existente] después de crear un nuevo sitio.

### <span id="migrate"></span></a>Migración de un sitio de WordPress existente a Azure

Como se menciono en la sección [Arquitectura y planeación][Arquitectura y planeación], hay dos formas de migrar un sitio web de WordPress.

-   **Exportación e importación** - Para sitios sin mucha personalización o donde solamente se desea mover el contenido.

-   **Copia de seguridad y restauración** - Para sitios con mucha personalización donde se desea mover todo.

Use una de las siguientes secciones para migrar el sitio.

#### Método de exportación e importación

1.  Use la [exportación de WordPress][Exportación de WordPress] para exportar el sitio existente.

2.  Cree un nuevo sitio web usando los pasos de la sección [Crear un nuevo sitio WordPress][Crear un nuevo sitio WordPress].

3.  Inicie sesión en el sitio de WordPress en Sitios web Azure y haga clic en **Complementos** -\> **Agregar nuevo**. Busque e instale el complemento **Importador de WordPress**.

4.  Una vez instalado el complemento del importador, haga clic en **Herramientas** -\> **Importar** y seleccione **WordPress** para usar el complemento Importador de WordPress.

5.  En la página **Importar WordPress**, haga clic en **Elegir archivo**. Busque el archivo WXR exportado desde el sitio de WordPress existente y después elija **Cargar archivo e importar**.

6.  Haga clic en **Enviar**. Se mostrará un mensaje indicando que la importación se realizó correctamente.

7.  Cuando haya completado todos estos pasos, reinicie el sitio web desde el panel de dicho sitio en el [Portal de administración de Azure][Portal de administración de Azure].

Después de importar el sitio, puede que necesite realizar los pasos siguientes para habilitar la configuración no contenida en el archivo de importación.

| Si usaba esto...                    | Haga esto...                                                                                                                                                                                |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Vínculos permanentes**            | En el panel de WordPress del nuevo sitio, haga clic en **Configuración** -\> **Vínculos permanentes** y después actualice la estructura de vínculos permanentes.                            |
| **Vínculos de imagen y multimedia** | Para actualizar estos vínculos a la nueva ubicación, use el [complemento Update URLs de Belvet Blues][complemento Update URLs de Belvet Blues], una herramienta de búsqueda y reemplazo, o hágalo manualmente en la base de datos. |
| **Temas**                           | Vaya a **Apariencia** -\> **Tema** y actualice el tema del sitio web según sea necesario.                                                                                                   |
| **Menús**                           | Si el tema admite menús, los vínculos a la página principal todavía pueden tener el subdirectorio antiguo incrustado en ellos. Vaya a **Apariencia** -\> **Menús** y actualícelos.          |

#### Método de copia de seguridad y restauración

1.  Haga una copia de seguridad del sitio de WordPress existente usando la información que se encuentra en [Copias de seguridad de WordPress][Haga una copia de seguridad del sitio].

2.  Haga una copia de seguridad de la base datos existente usando la información que se encuentra en [Copia de seguridad de su base de datos][base de datos].

3.  Cree una nueva base de datos y restaurare la copia de seguridad.

    1.  Adquiera una nueva base de datos de la [Tienda de Azure][ClearDB en la Tienda de Azure], o configure una base de datos MySQL en una máquina virtual [Windows][Windows] o [Linux][Linux].

    2.  Usando un cliente MySQL como [MySQL Workbench][MySQL Workbench], conéctese a la nueva se datos e importe la base de datos de WordPress.

    3.  Actualice la base de datos para cambiar las entradas de dominio al nuevo dominio del sitio web de Azure. Ejemplo: mywordpress.azurewebsites.net. Use el [script de búsqueda y reemplazo para bases de datos de WordPress][script de búsqueda y reemplazo para bases de datos de WordPress] para guardar los cambios de todas las instancias de forma segura.

4.  Cree un nuevo sitio web y publique la copia de seguridad de WordPress.

    1.  Cree un nuevo sitio web en el [Portal de administración de Azure][Portal de administración de Azure] con una base de datos usando **Nuevo** -\> **Sitio web** -\> **Creación personalizada**. Esto creará un sitio vacío.

    2.  En la copia de seguridad de WordPress, localice el archivo **wp-config.php** y ábralo en un editor. Reemplace las entradas siguientes con la información de la nueva base de datos MySQL.

        -   **DB\_NAME**: nombre de usuario de la base de datos.

        -   **DB\_USER**: nombre de usuario usado para acceder a la base de datos.

        -   **DB\_PASSWORD**: contraseña de usuario.

        Después de cambiar estas entradas, guarde y cierre el archivo **wp-config.php**.

    3.  Use la información existente en [Implementación de un sitio web de Azure][Implementación de un sitio web de Azure] para habilitar el método de implementación que desee usar y después implemente la copia de seguridad de WordPress en el sitio web de Azure.

5.  Una vez implementado el sitio de WordPress, podrá acceder a nuevo sitio usando la dirección URL \*.azurewebsite.net correspondiente al sitio.

### Configuración del sitio

Después de crear o migrar el sitio de WordPress, use la siguiente información para mejorar el rendimiento o habilitar funcionalidades adicionales.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Para hacer esto...</th>
<th align="left">Use esto...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Establecer el modo y tamaño del sitio web y habilitar el escalamiento</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/">Escalamiento de sitios web</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Habilitar conexiones de base de datos persistentes</strong>
<p>De forma predeterminada, WordPress no usa conexiones de base de datos persistentes, lo que puede provocar que la conexión con la base de datos se limite después de varias conexiones.</p></td>
<td align="left"><ol>
<li><p>Edite el archivo <strong>wp-includes/wp-db.php</strong>.</p></li>
<li><p>Busque la línea siguiente.</p>
<p><code data-inline="1">$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li>
<li><p>Reemplace la línea anterior por la siguiente.</p>
<p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li>
<li><p>Busque la línea siguiente.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li>
<li><p>Reemplace la línea anterior por la siguiente.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li>
<li><p>Guarde el archivo <strong>wp-includes/wp-db.php</strong> y vuelva a implementar el sitio.</p></li>
</ol>
<div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>NOTA:</h5><p>Estos cambios se pueden sobrescribir cuando se actualiza WordPress.</p><p>WordPress tiene activadas las actualizaciones autom&aacute;ticas de forma predeterminada, que se pueden deshabilitar editando el archivo <strong>wp-config.php</strong> y agregando <code data-inline="1">define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Otra forma de administrar las actualizaciones ser&iacute;a usar un WebJob que supervisara el archivo <strong>wp-db.php</strong> y realizara las modificaciones anteriores cada vez que se actualizara el archivo. Consulte <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Introducci&oacute;n a WebJobs</a> para obtener m&aacute;s informaci&oacute;n.</p></div></td>
</tr>
<tr class="odd">
<td align="left"><strong>Mejorar el rendimiento</strong></td>
<td align="left"><ul>
<li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Desactivación de la cookie ARR</a>: puede mejorar el rendimiento cuando ejecute WordPress en varias instancias del sitio web.</p></li>
<li><p>Activación del almacenamiento en caché. <a href="http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx">Caché en Redis</a> (vista previa) se puede usar con el <a href="https://wordpress.org/plugins/redis-object-cache/">complemento de WordPress para caché de objetos en Redis</a> o usar una de las otras ofertas de caché de la <a href="http://azure.microsoft.com/en-us/gallery/store/">Tienda de Azure</a>.</p></li>
<li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Hacer que WordPress sea más rápido con Wincache</a>: Wincache está habilitado de forma predeterminada para sitios web.</p></li>
<li><p><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/">Escalamiento de un sitio web de Azure</a> y uso del <a href="http://www.cleardb.com/developers/cdbr/introduction">enrutamiento de alta disponibilidad de ClearDB</a> o <a href="http://www.mysql.com/products/cluster/">CGE de MySQL Cluster</a>.</p></li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><strong>Usar blobs para almacenamiento</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/es-es/documentation/articles/storage-create-storage-account/">Creación de una cuenta de almacenamiento de Azure</a></p></li>
<li><p>Obtenga información acerca del <a href="http://azure.microsoft.com/es-es/documentation/articles/cdn-how-to-use/">Uso de la Red de entrega de contenido (CDN)</a> para distribuir geográficamente datos almacenados en blobs.</p></li>
<li><p>Instale y configure el <a href="https://wordpress.org/plugins/windows-azure-storage/">complemento Almacenamiento de Azure para WordPress</a>.</p>
<p>Para obtener información detallada acerca de la instalación y configuración del complemento, consulte la <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guía del usuario</a>.</p></li>
</ol></td>
</tr>
<tr class="odd">
<td align="left"><strong>Habilitar el correo electrónico</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">Activación de SendGrid usando la Tienda de Azure</a></p></li>
<li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Instalación del complemento SendGrid para WordPress</a></p></li>
</ol></td>
</tr>
<tr class="even">
<td align="left"><strong>Configurar un nombre de dominio personalizado</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/">Uso de un nombre de dominio personalizado con un sitio web de Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Habilitar HTTPS para un nombre de dominio personalizado</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/">Uso de HTTPS con un sitio web de Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Equilibrio de carga o distribución geográfica para el sitio</strong></td>
<td align="left"><a href="http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/">Enrutamiento del tráfico con el Administrador de tráfico de Azure</a>. Si usa un dominio personalizado, consulte <a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/">Uso de un nombre de dominio personalizado con un sitio web de Azure</a> para obtener información acerca del Administrador de tráfico con nombres de dominio personalizados.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Activación de las copias de seguridad de sitios web automatizadas</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-backup/">Copia de seguridad de Sitios web Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Activación del registro de diagnósticos</strong></td>
<td align="left"><a href="http://azure.microsoft.com/es-es/documentation/articles/web-sites-enable-diagnostic-log/">Activación del registro de diagnósticos para Sitios web</a></td>
</tr>
</tbody>
</table>

## [][]Recursos adicionales

-   [Optimización de WordPress][Optimización de WordPress]

-   [Conversión de un sitio de WordPress en un multisitio][Conversión de un sitio de WordPress en un multisitio]

-   [Asistente para actualización de ClearDB para Azure][Asistente para actualización de ClearDB para Azure]

-   [Hospedaje de WordPress en una subcarpeta de su sitio web de Azure][Hospedaje de WordPress en una subcarpeta de su sitio web de Azure]

-   [Paso a paso: Creación de un sitio de WordPress mediante Azure][Paso a paso: Creación de un sitio de WordPress mediante Azure]

-   [Hospedaje de un blog de WordPress existente en Azure][Hospedaje de un blog de WordPress existente en Azure]

-   [Activación de vínculos permanentes descriptivos en WordPress][Activación de vínculos permanentes descriptivos en WordPress]

-   [Migración y ejecución del blog de WordPress en Sitios web Azure][Migración y ejecución del blog de WordPress en Sitios web Azure]

-   [Ejecución de WordPress en Sitios web Azure gratuitamente][Ejecución de WordPress en Sitios web Azure gratuitamente]

-   [WordPress en Azure en 2 minutos o menos][WordPress en Azure en 2 minutos o menos]

-   [Movimiento de un bloque de WordPress a Azure - Parte 1: Creación de un blog de WordPress en Azure][Movimiento de un bloque de WordPress a Azure - Parte 1: Creación de un blog de WordPress en Azure]

-   [Movimiento de un bloque de WordPress a Azure - Parte 2: Transferencia del contenido][Movimiento de un bloque de WordPress a Azure - Parte 2: Transferencia del contenido]

-   [Movimiento de un bloque de WordPress a Azure - Parte 3: Configuración de un dominio personalizado][Movimiento de un bloque de WordPress a Azure - Parte 3: Configuración de un dominio personalizado]

-   [Movimiento de un bloque de WordPress a Azure - Parte 4: Vínculos permanentes descriptivos y reglas de reescritura de direcciones URL][Movimiento de un bloque de WordPress a Azure - Parte 4: Vínculos permanentes descriptivos y reglas de reescritura de direcciones URL]

-   [Movimiento de un bloque de WordPress a Azure - Parte 5: Movimiento desde una subcarpeta a la raíz][Movimiento de un bloque de WordPress a Azure - Parte 5: Movimiento desde una subcarpeta a la raíz]

-   [Configuración de un sitio web de WordPress en la cuenta de Azure][Configuración de un sitio web de WordPress en la cuenta de Azure]

-   [Sustentación de WordPress en Azure][Sustentación de WordPress en Azure]

-   [Sugerencias para WordPress en Azure][Sugerencias para WordPress en Azure]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [Arquitectura y planeación]: #planning
  [Cómo]: #howto
  [Más recursos]: #resources
  [ClearDB en la Tienda de Azure]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [precios de ClearDB]: http://www.cleardb.com/pricing.view
  [las versiones 5.3, 5.4 y 5.5 de PHP]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-configure/
  [an Azure Website and MySQL Database hosted in a single Azure region]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [Administrador de tráfico]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [enrutador de alta disponibilidad CDBR]: http://www.cleardb.com/developers/cdbr/introduction
  [CGE de MySQL Cluster]: http://www.mysql.com/products/cluster/
  [Caché en Redis]: http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx
  [Memcache Cloud]: http://azure.microsoft.com/en-us/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/en-us/gallery/store/memcachier/memcachier/
  [Tienda de Azure]: http://azure.microsoft.com/en-us/gallery/store/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [Red de entrega de contenido (CDN)]: http://azure.microsoft.com/es-es/documentation/articles/cdn-how-to-use/
  [Complemento de WordPress para usar almacenamiento de blobs]: https://wordpress.org/plugins/windows-azure-storage/
  [SendGrid]: http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/
  [complemento de WordPress para usar SendGrid]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
  [Nombres de dominio personalizados con Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/
  [Compatibilidad con HTTPS en Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/
  [Compatibilidad con la publicación de ensayo para Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-staged-publishing/
  [Registro de diagnósticos con Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-enable-diagnostic-log/
  [Supervisión de Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-monitor/
  [Implementación de un sitio web de Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-deploy/
  [Copias de seguridad de Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-backup/
  [Restauración de un sitio web de Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-restore/
  [Detalles de precios, incluidas las capacidades de tamaños y modos de los sitios web]: https://azure.microsoft.com/en-us/pricing/details/web-sites/
  [Escalamiento de un sitio web de Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/
  [Exportación de WordPress]: http://en.support.wordpress.com/export/
  [complemento Importador de WordPress]: http://wordpress.org/plugins/wordpress-importer/
  [Haga una copia de seguridad del sitio]: http://codex.wordpress.org/WordPress_Backups
  [base de datos]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Creación de un sitio web de WordPress desde la Galería de Azure]:  "http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-web-site-gallery/"
  [Migración de un sitio de WordPress existente]: #migrate
  [Crear un nuevo sitio WordPress]: #create
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [complemento Update URLs de Belvet Blues]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [script de búsqueda y reemplazo para bases de datos de WordPress]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  [Introducción a WebJobs]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
  [Desactivación de la cookie ARR]: http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx
  [complemento de WordPress para caché de objetos en Redis]: https://wordpress.org/plugins/redis-object-cache/
  [Hacer que WordPress sea más rápido con Wincache]: http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/
  [Creación de una cuenta de almacenamiento de Azure]: http://azure.microsoft.com/es-es/documentation/articles/storage-create-storage-account/
  [guía del usuario]: http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx
  []: resources
  [Optimización de WordPress]: http://codex.wordpress.org/WordPress_Optimization
  [Conversión de un sitio de WordPress en un multisitio]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [Asistente para actualización de ClearDB para Azure]: http://www.cleardb.com/store/azure/upgrade
  [Hospedaje de WordPress en una subcarpeta de su sitio web de Azure]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [Paso a paso: Creación de un sitio de WordPress mediante Azure]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Hospedaje de un blog de WordPress existente en Azure]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [Activación de vínculos permanentes descriptivos en WordPress]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [Migración y ejecución del blog de WordPress en Sitios web Azure]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [Ejecución de WordPress en Sitios web Azure gratuitamente]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [WordPress en Azure en 2 minutos o menos]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [Movimiento de un bloque de WordPress a Azure - Parte 1: Creación de un blog de WordPress en Azure]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [Movimiento de un bloque de WordPress a Azure - Parte 2: Transferencia del contenido]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [Movimiento de un bloque de WordPress a Azure - Parte 3: Configuración de un dominio personalizado]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [Movimiento de un bloque de WordPress a Azure - Parte 4: Vínculos permanentes descriptivos y reglas de reescritura de direcciones URL]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [Movimiento de un bloque de WordPress a Azure - Parte 5: Movimiento desde una subcarpeta a la raíz]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Configuración de un sitio web de WordPress en la cuenta de Azure]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Sustentación de WordPress en Azure]: http://www.johnpapa.net/wordpress-on-azure/
  [Sugerencias para WordPress en Azure]: http://www.johnpapa.net/azurecleardbmysql/
