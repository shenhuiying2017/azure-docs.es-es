El Sistema de nombres de dominio (DNS) se utiliza para buscar recursos en Internet. Por ejemplo, cuando escribe la dirección de un sitio web en el explorador o hace clic en un vínculo de una página web, utiliza DNS para convertir el dominio en una dirección IP. La dirección IP es algo parecido a la dirección de una calle, pero su sistema es algo complejo. Por ejemplo, es mucho más fácil recordar un nombre DNS como **contoso.com** que recordar una dirección IP como 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

El sistema DNS se basa en  *records*. Los registros asocian un  *name* específico, como **contoso.com**, a una dirección IP u otro nombre DNS. Cuando una aplicación, como un explorador web, busca un nombre en DNS, encuentra el registro y utiliza aquello a lo que apunte como dirección. Si el valor al que apunta es una dirección IP, el explorador utilizará ese valor. Si apunta a otro nombre DNS, la aplicación tiene que volver a realizar una resolución. En última instancia, todas las resoluciones de nombre terminarán en una dirección IP.

Cuando se crea un sitio web de Azure, se asigna un nombre DNS automáticamente al sitio. Este nombre adopta la forma de **&lt;nombredelsitio&gt;.azurewebsites.net**. También hay una dirección IP virtual disponible para el uso al crear registros DNS, por lo que puede crear registros que apunten a **.azurewebsites.net** o bien apuntar a la dirección IP.

> [AZURE.NOTE] La dirección IP del sitio web cambiará si elimina y vuelve a crear el sitio web, o si cambia el modo de sitio web a Gratis después de que se haya establecido en Básico, Compartido o Estándar.

También hay varios tipos de registros, cada uno con sus propias funciones y limitaciones, pero para los sitios web, solo nos interesan dos, los registros  *A* y  *CNAME* .

###Registro de dirección (registro D)

Un registro D asigna un dominio, como **contoso.com** o **www.contoso.com**, *or a wildcard domain* como **\*.contoso.com**, a una dirección IP. En el caso de un sitio web de Azure, la IP virtual del servicio o una dirección IP específica que haya adquirido para el sitio web.

Las principales ventajas de un registro D sobre un registro CNAME son:

* Puede asignar un dominio raíz, como **contoso.com** a una dirección IP; muchos registradores solo lo permiten con los registros D.

* Puede tener una entrada que utilice un carácter comodín, como **\*.contoso.com**, que administraría las solicitudes de varios subdominios como **mail.contoso.com**, **blogs.contoso.com** o **www.contso.com**.

> [AZURE.NOTE] Puesto que un registro D se asigna a una dirección IP estática, no puede resolver automáticamente cambios en la dirección IP de su sitio web. Se proporciona una dirección IP para que se use con registros D cuando establezca la configuración del nombre de dominio personalizado para su sitio web; sin embargo, este valor podría cambiar si elimina y vuelve a crear su sitio web o cambia el modo del sitio web para que vuelva a ser gratuito.

###Registro de alias (registro CNAME)

Un registro CNAME asigna un nombre DNS  *specific*, como **mail.contoso.com** o **www.contoso.com**, a otro nombre de dominio (canónico). En el caso de Sitios web Azure, el nombre de dominio canónico es el nombre de dominio **&lt;<nombredesusitio>.azurewebsites.net** de su sitio web. Una vez creado, el CNAME crea un alias para el nombre de dominio **&lt;<nombredesusitio>.azurewebsites.net**. La entrada de CNAME se resolverá en la dirección IP del servicio del nombre de dominio **&lt;nombredesusitio>.azurewebsites.net** de manera automática, por lo que si la dirección IP del sitio web cambia, no tiene que realizar ninguna acción.

> [AZURE.NOTE] Algunos registradores de dominio solo permiten asignar subdominios cuando se usa un registro CNAME, como **www.contoso.com**, y no nombres raíz, como **contoso.com**. Para obtener más información acerca de los registros CNAME, consulte la documentación que proporciona el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia sobre el registro CNAME</a> o el documento <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a>.

###Aspectos específicos de DNS de un sitio web Azure

El uso de un registro D con Sitios web Azure requiere crear primero uno de los siguientes registros CNAME:

* **Para el dominio raíz o subdominios comodín**: un nombre DNS de **awverify**  a **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**.

* **Para un subdominio específico**: nombre de DNS de **awverify.&lt;subdominio>** a **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**. Por ejemplo, **awverify.blogs** si el registro D es para **blogs.contoso.com**.

Este registro CNAME se utiliza para verificar que se posee el dominio que está intentando utilizar. Esto se suma a la creación de un registro D que apunta a la dirección IP virtual del sitio web.

Puede encontrar la dirección IP, así como el nombre **awverify** y los nombres **.azurewebsites.net** para el sitio web, siguiendo estos pasos:

1. En el explorador, abra el [Portal de administración de Azure](https://manage.windowsazure.com).

2. En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** en la parte inferior de la página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE] Si **Administrar dominios** no está habilitado, está usando un sitio web gratuito. No puede utilizar nombres de dominio personalizados con un sitio web gratuito; por tanto, debe actualizar al modo Compartido, Básico o Estándar. Para obtener más información acerca de los modos de los sitios web, incluido cómo cambiar el modo de su sitio, consulte [Escalación de sitios web](http://www.windowsazure.com/es-es/documentation/articles/web-sites-scale/).

6. En el cuadro de diálogo **ADMINISTRAR DOMINIOS PERSONALIZADOS**, verá la información de ** awverify**, el nombre de dominio **.azurewebsites.net** actualmente asignado y la dirección IP virtual. Guarde esta información, puesto que se utilizará al crear registros DNS.

	![](./media/custom-dns-web-site/managecustomdomains.png)


<!--HONumber=42-->
