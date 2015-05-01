<properties 
   pageTitle="Conexión de una aplicación web en Servicio de aplicaciones de Azure a Caché en Redis a través del protocolo Memcache" 
   description="Conexión de una aplicación web en Servicio de aplicaciones de Azure a Caché en Redis mediante el protocolo Memcache" 
   services="app-service\web" 
   documentationCenter="php" 
   authors="SyntaxC4" 
   manager="wpickett" 
   editor="riande"/>
   
<tags
   ms.service="app-service-web"
   ms.devlang="php"
   ms.topic="article"
   ms.tgt_pltfrm="windows"
   ms.workload="web" 
   ms.date="03/31/2015"
   ms.author="cfowler"/>

# Conexión de una aplicación web en Servicio de aplicaciones de Azure a Caché en Redis a través del protocolo Memcache

En este artículo, aprenderá a conectar una aplicación web de WordPress en [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) a [Caché en Redis de Azure][12] mediante el protocolo [Memcache][13]. Si tiene una aplicación web existente que usa un servidor de Memcache para almacenamiento en caché en memoria, puede migrarla a Servicio de aplicaciones de Azure y usar la solución de almacenamiento en caché de origen en Microsoft Azure con pocos o ningún cambio en el código de aplicación. Además, puede aprovechar su experiencia existente en Memcache para crear aplicaciones distribuidas y altamente escalables en Servicio de aplicaciones de Azure con Caché en Redis de Azure para almacenamiento en caché en memoria, mientras usa marcos de aplicaciones conocidos como .NET, PHP, Node.js, Java y Python.  

Aplicaciones web del Servicio de aplicaciones habilita este escenario de aplicación con las correcciones de compatibilidad (shim) de Memcache de Aplicaciones web, que es un servidor local con Memcache que actúa como proxy de Memcache para almacenar en caché las llamadas a Caché en Redis de Azure. Esto permite que toda aplicación que se comunica mediante el uso del protocolo Memcache almacene en caché datos con Caché en Redis. Estas correcciones de compatibilidad (shim) de Memcache funcionan en el nivel de protocolo, por tanto, cualquier aplicación o marco de aplicaciones puede utilizarlas siempre que se comunique mediante el uso del protocolo Memcache.

## Requisitos previos

Las correcciones de compatibilidad (shim) de Memcache de Aplicaciones web se pueden utilizar con cualquier aplicación, siempre que se comunique mediante el uso del protocolo Memcache. En este ejemplo específico, la aplicación de referencia es un sitio escalable de WordPress, que se puede aprovisionar desde Azure Marketplace. 

Siga los pasos descritos en estas publicaciones (en inglés):

* [Aprovisionamiento de una entrada del Servicio de Caché en Redis de Azure][1]
* [Implementación de un sitio escalable de WordPress en Azure][0]

Una vez que se implementa el sitio web escalable de WordPress y que se aprovisiona una instancia de Caché en Redis, estará preparado para proceder con la habilitación de las correcciones de compatibilidad (shim) de Memcache en Aplicaciones web del Servicio de aplicaciones de Azure.

## Habilitación de las correcciones de compatibilidad de Memcache de Aplicaciones web

Para configurar las correcciones de compatibilidad (shim) de Memcache, debe crear tres ajustes de aplicaciones. Puede hacerlo mediante una variedad de métodos, los que incluyen el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), el [portal antiguo][3], los [cmdlets de Azure PowerShell][5] o las [herramientas de línea de comandos multiplataforma de Azure][5]. En lo que respecta a esta publicación, usaremos el [Portal de Azure][4] para establecer los ajustes de la aplicación. Los valores siguientes se pueden recuperar de la hoja **Configuración** de la instancia de Caché en Redis.

![Azure Redis Cache Settings Blade](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### Agregar la configuración de aplicación REDIS_HOST

La primera configuración de aplicación que debe crear es la configuración de aplicación **REDIS\_HOST**. Esta configuración establece el destino al cual las correcciones de compatibilidad (shim) reenvía la información de caché. El valor que se requiere para la configuración de aplicación REDIS_HOST se puede recuperar de la hoja **Propiedades** de la instancia de Caché en Redis.

![Azure Redis Cache Host Name](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Defina la clave para la configuración de aplicación en **REDIS\_HOST** y el valor de la configuración de aplicación en el **nombre de host** de la instancia de Caché en Redis.

![Web App AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### Agregar la configuración de aplicación REDIS_KEY

La segunda configuración de aplicación que se debe crear es la configuración de aplicación **REDIS\_KEY**. Esta configuración proporciona el token de autenticación que se necesita para tener un acceso seguro a la instancia de Caché en Redis. El valor que se requiere para la configuración de aplicación REDIS_KEY se puede recuperar de la hoja **Claves de acceso** de la instancia Caché en Redis.

![Azure Redis Cache Primary Key](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Establezca la clave de la configuración de aplicación en **REDIS\_KEY** y el valor de la configuración de aplicación en **Clave principal** de la instancia de Caché en Redis.

![Azure Website AppSetting REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### Agregar la configuración de aplicación MEMCACHESHIM_REDIS_ENABLE

La última configuración de aplicación se usa para habilitar las correcciones de compatibilidad (shim) de Memcache en Aplicaciones web, que utilizarán las configuraciones REDIS_HOST y REDIS_KEY para conectarse a Caché en Redis de Azure y reenviar las llamadas a caché. Establezca la clave de la configuración de aplicación en **MEMCACHESHIM\_REDIS\_ENABLE** y el valor en **true**.

![Web App AppSetting MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Una vez que agrega las tres (3) configuraciones de aplicación, haga clic en **Guardar**.

## Habilitar extensión de Memcache para PHP

A fin de que la aplicación se comunique con el protocolo Memcache, es necesario instalar la extensión Memcache en PHP (el marco de lenguaje para el sitio de WordPress).

### Descargar la extensión php_memcache

Vaya a [PECL][6], en la categoría de almacenamiento en caché, haga clic en [memcache][7]. Haga clic en el vínculo DLL en la columna de descargas.

![PHP PECL Website](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Descargue el vínculo No seguro para subprocesos (NTS) x86 para la versión de PHP habilitada en Aplicaciones web. (El valor predeterminado es PHP 5.4)

![PHP PECL Website Memcache Package](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### Habilitar la extensión php_memcache

Después de descargar el archivo, descomprima y cargue **php\_memcache.dll** en el directorio **d:\\home\\site\\wwwroot\\bin\\ext\\**. Una vez que el archivo php_memcache.dll se cargó en la aplicación web, es necesario habilitar la extensión para tiempo de ejecución de PHP. Para habilitar la extensión Memcache en el Portal de Azure, abra la hoja **Configuración de aplicación** de la aplicación web y, a continuación, agregue la nueva configuración de aplicación con la clave de **PHP\_EXTENSIONS** y el valor **bin\\ext\\php_memcache.dll**.


> Si la aplicación web necesita cargar varias extensiones PHP, el valor de PHP_EXTENSIONS debe ser una lista delimitada por comas de rutas de acceso relativas a archivos DLL.

![Web App AppSetting PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Una vez que termine, haga clic en **Guardar**.

## Instalar el complemento WordPress para Memcache

En la página de complementos de WordPress, haga clic en el botón **Agregar nuevo**.

![WordPress Plugin Page](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

En el cuadro de búsqueda, escriba **memcached** y presione la tecla **Entrar**.

![WordPress Add New Plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Busque **Memcached Object Cache** en la lista y, a continuación, haga clic en el botón **Instalar ahora**.

![WordPress Install Memcache Plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### Habilitar el complemento WordPress para Memcache

>[AZURE.NOTE] Siga las instrucciones que aparecen en este blog en [Habilitación de una extensión de sitio en Aplicaciones web][8] (en inglés) para instalar Visual Studio Online.

En el archivo `wp-config.php`, agregue el siguiente fragmento de código sobre el comentario de edición de detención cerca del final del archivo.

```php
$memcached_servers = array(
	'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Una vez que se pega este fragmento de código, monaco guardará automáticamente el documento.

El paso siguiente es habilitar el complemento object-cache. Para hacerlo, arrastre y suelte **object-cache.php** desde la carpeta **wp-content/memcached** a la carpeta **wp-content** para habilitar la funcionalidad Caché de objetos de Memcache.

![Locate the memcache object-cache.php plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Ahora que el archivo **object-cache.php** está en la carpeta **wp-content**, Memcached Object Cache está habilitado.

![Enable the memcache object-cache.php plugin](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## Comprobación de que el complemento Memcache Object Cache funciona

Todos los pasos para habilitar las correcciones de compatibilidad (shim) de Memcache de Aplicaciones web ahora están completos. Lo único que queda es comprobar que los datos rellenan la instancia de Caché en Redis.

### Habilitar la compatibilidad de puertos no SSL en Caché en Redis de Azure

>[AZURE.NOTE] En el momento de la redacción del presente documento, la CLI de Redis no es compatible con la conectividad SSL, por lo que los siguientes pasos resultan necesarios.

En el Portal de Azure, vaya a la instancia de Caché en Redis que creó para esta aplicación web. Una vez abierta la hoja de la caché, haga clic en el icono **Configuración**.

![Azure Redis Cache Settings Button](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Seleccione **Puertos de acceso** en la lista.

![Azure Redis Cache Access Port](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Haga clic en **No** para **Permitir acceso solo vía SSL**.

![Azure Redis Cache Access Port SSL Only](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

Verá que ahora está definido el valor Puerto NO SSL. Haga clic en **Guardar**.

![Azure Redis Cache Redis Access Portal Non-SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### Conectarse a Caché en Redis de Azure desde redis-cli

>[AZURE.NOTE] En este paso se supone que Redis está instalado localmente en su equipo de desarrollo. [Siga estas instrucciones][9] para instalar Redis localmente.

Abra la consola de línea de comandos de su preferencia y escriba el siguiente comando:

```shell
redis-cli -h <hostname-for-redis-cache> -a <primary-key-for-redis-cache> -p 6379
```

Reemplace **<hostname-for-redis-cache>** por el nombre de host xxxxx.redis.cache.windows.net real y **<primary-key-for-redis-cache>** por la clave de acceso para la memoria caché y, a continuación, presione **Entrar**. Una vez que la CLI se conecte a la instancia de Caché en Redis, emita cualquier comando de Redis. En la captura de pantalla que aparece a continuación, elegí enumerar las claves.

![Connect to Azure Redis Cache from Redis CLI in Terminal](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

La llamada para enumerar las claves debe devolver un valor. Si no es así, intente ir a la aplicación web y vuelva a intentarlo.

## Conclusión

¡Enhorabuena! La aplicación de WordPress ahora tiene una caché en memoria centralizada para ayudar a aumentar el rendimiento. Recuerde, las Correcciones de compatibilidad (shim) de Memcache de Aplicaciones web se puede usar con cualquier cliente Memcache, independientemente del lenguaje de programación o del marco de aplicaciones. Si desea enviar comentarios o formular preguntas acerca de las correcciones de compatibilidad (shim) de Memcache de Aplicaciones web, publique en los [Foros de MSDN][10] o en [Stackoverflow][11].

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web inicial de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía para cambiar de sitios web al servicio de aplicaciones, consulte: [El servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía para cambiar del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)


[0]: http://bit.ly/1F0m3tw
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org


<!--HONumber=52-->