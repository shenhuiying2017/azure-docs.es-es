<properties 
	pageTitle="Conversión de un sitio de WordPress en un multisitio" 
	description="Obtenga información acerca de cómo tomar un sitio web de WordPress existente creado a través de la galería en Azure y convertirlo a multisitio de WordPress" 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/28/2014" 
	ms.author="tomfitz"/>





# Conversión de un sitio de WordPress en un multisitio

*Por [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

En este tutorial aprenderá a tomar un sitio web de WordPress existente creado a través de la galería en Azure y convertirlo en una instalación multisitio de WordPress. Adicionalmente, aprenderá a asignar un dominio personalizado a cada uno de los subsitios dentro de su instalación.

Se supone que tiene una instalación existente de WordPress. De lo contrario, siga las instrucciones que se proporcionan en [Creación de un sitio web de WordPress desde la galería de Azure][website-from-gallery].

La conversión de la instalación de un único sitio de WordPress existente a multisitio es generalmente bastante simple y muchos de los pasos iniciales que se presentan aquí provienen directamente de la página de [creación de una red][wordpress-codex-create-a-network] (en inglés) en [WordPress Codex](http://codex.wordpress.org).

Comencemos.

## Permitir multisitio

Primero necesita habilitar Multisitio mediante el archivo `wp-config.php` con la constante**WP\_ALLOW\_MULTISITE**. Hay dos métodos para modificar los archivos del sitio web: el primero es a través de FTP y el segundo mediante Git. Si no está familiarizado con la configuración de alguno de estos métodos, consulte los siguientes tutoriales:

* [Sitio web PHP con MySQL y FTP][website-w-mysql-and-ftp-ftp-setup]

* [Sitio web PHP con MySQL y Git][website-w-mysql-and-git-git-setup]

Abra el archivo  `wp-config.php`wp-config.php con el editor de su preferencia y agregue el siguiente código encima de la línea `/* That's all, stop editing! Happy blogging. */`.

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

¡Asegúrese de guardar el archivo y cargarlo de vuelta al servidor!

## Configuración de la red

Inicie sesión en el área *wp-admin* de su sitio web; debería ver un elemento nuevo en el menú **Herramientas** llamado **Configuración de red**. Haga clic en **Configuración de red** y complete los detalles de su red.

![Network Setup Screen][wordpress-network-setup]

Este tutorial usa el esquema de sitios  *Sub-directories* porque siempre funciona, y más adelante en este tutorial vamos a configurar dominios personalizados para cada subsitio. Sin embargo, debería ser posible configurar una instalación de subdominio si asigna un dominio a través del Portal y configura correctamente un DNS con caracteres comodín correctamente.

Para obtener más información sobre la configuración de subdominios frente a subdirectorios, consulte el artículo sobre[][tipos de red multisitiowordpress-codex-types-of-networks] en WordPress Codex.

## Habilitación de la red

La red está ahora configurada en la base de datos, pero todavía queda un paso para habilitar la funcionalidad de red. Finalice la configuración de  `wp-config.php` y asegúrese de que  `web.config` redirige correctamente cada sitio.


Tras hacer clic en el botón **Instalar** situado en la página *Network Setup*, WordPress intentará actualizar los archivos `wp-config.php` y `web.config`. No obstante, siempre debe comprobar los archivos para asegurarse de que las actualizaciones se realizaron correctamente. De lo contrario, esta pantalla le presentará las actualizaciones necesarias. Edite y guarde los archivos.


Después de realizar las actualizaciones, necesitará cerrar la sesión y volver a iniciarla en el panel de wp-admin.

Debería aparecer ahora un menú adicional en la barra de administración con la etiqueta **Mis sitios**. Este menú le permite controlar su red nueva a través del panel **Administrador de red**.

# Incorporación de dominios personalizados

El complemento [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] facilita enormemente la incorporación de dominios personalizados a cualquier sitio de su red. Para que el complemento funcione correctamente, necesita realizar cierta configuración adicional en el Portal y también en el registrador de su dominio.

## Habilitación de la asignación de dominios al sitio web

El modo de sitio web gratis predeterminado no es compatible con la incorporación de dominios personalizados en los sitios web de Azure. Necesitará cambiar al modo compartido o estándar. Para ello, siga estos pasos:

* Inicie sesión en el portal de Azure y localice su sitio web. 
* Haga clic en la pestaña **Escalar** en el área de contenido principal.
* En **General**, seleccione *SHARED* o *STANDARD*
* Haga clic en **Guardar**.

Es posible que reciba un mensaje pidiéndole que verifique el cambio y que confirme que su sitio web puede incurrir en costes, según el uso y otros ajustes que realice.

El procesamiento de la nueva configuración tarda unos pocos segundos, de modo que ahora es un buen momento para empezar a configurar su dominio.

## Verificación de su dominio

Antes de que Sitios web Azure le permita asignar un dominio al sitio, primero necesita verificar que tiene la autorización para asignar el dominio. Para ello, debe agregar un registro nuevo CNAME a su entrada de DNS.

* Inicie sesión en el administrador de DNS de su dominio.
* Cree un nuevo CNAME *awverify*
* Apunte *awverify* a *awverify.YOUR_DOMAIN.azurewebsites.net*

Puede pasar algún tiempo antes de que los cambios de DNS surtan efecto, de modo que si los siguientes pasos no funcionan inmediatamente, prepárese una taza de café y vuelva después para volver a intentarlo.

## Incorporación del dominio al sitio web

Regrese a su sitio web a través del Portal de Azure y, en esta ocasión, haga clic en la pestaña **CONFIGURE**. El botón **MANAGE DOMAINS** debería estar disponible. Haga clic en él.

El cuadro de diálogo *Manage custom domains* aparece como ventana emergente. Aquí es donde introducirá todos los dominios que desea asignar a su sitio web. Si un dominio no aparece en este lugar, no estará disponible para asignarlo dentro de WordPress, sin considerar la configuración de DNS del dominio.

![Cuadro de diálogo de administración de dominios personalizados][wordpress-manage-domains]

Después de escribir su dominio en el cuadro de texto, Azure verificará el registro CNAME de  *awverify* que creó anteriormente. Si el DNS no se ha propagado completamente, aparecerá un indicador rojo. Si fue correcto, verá una marca de verificación verde. 

Tome nota de la dirección IP que aparece en la parte inferior del cuadro de diálogo. La necesitará para configurar el registro A para su dominio.

## Configuración del registro A del dominio

Si los demás pasos fueron correctos, ahora puede asignar el dominio a su sitio web de Azure a través de un registro D de DNS. 

Es importante tener en cuenta aquí que los Sitios web Azure aceptan tanto los registros CNAME como A; sin embargo,  *must* usar un registro A para permitir una asignación correcta del dominio. Un registro CNAME no se puede reenviar a otro CNAME, que es lo que Azure creó con YOUR_DOMAIN.azurewebsites.net.

Con la ayuda de la dirección IP del paso anterior, vuelva a su administrador de DNS y configure el registro A para que apunte a dicha dirección IP.


## Instalación y configuración del complemento

Multisitio de WordPress no dispone actualmente de un método integrado para asignar dominios personalizados. Sin embargo, hay un complemento llamado [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] que agrega la funcionalidad por usted. Inicie sesión en la sección de Administrador de red de su sitio e instale el complemento **WordPress MU Domain Mapping**.

Después de instalar y activar el complemento, visite **Configuración** > **Asignación de dominios** para configurarlo. En el primer cuadro de texto, *Server IP Address*, indique la dirección IP que usó para configurar el registro A para el dominio. Establezca las *Domain Options* que desee (los valores predeterminados suelen ser correctos) y haga clic en **Guardar**.

## Asignación del dominio

Visite el **Panel** del sitio al cual desea asignar el dominio. Haga clic en **Herramientas** > **Asignación de dominios**, escriba el dominio nuevo en el cuadro de texto y haga clic en **Agregar**.

De manera predeterminada, el dominio nuevo se sobrescribirá con el dominio del sitio generado automáticamente. Si desea que todo el tráfico se envíe al nuevo dominio, compruebe el cuadro *Primary domain for this blog* antes de guardar. Puede agregar un número ilimitado de dominios a un sitio, pero solo uno puede ser principal.

## Vuelva a hacerlo

Sitios web Azure le permite agregar un número ilimitado de dominios a un sitio web. Para agregar otro dominio, necesitará ejecutar las secciones **Verificación de su dominio** y **Configuración del registro A del dominio** para cada dominio.	

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/es-es/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png





<!--HONumber=42-->
