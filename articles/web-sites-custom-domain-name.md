<properties  title="Learn how to configure an Azure web site to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />
# Configuración de un nombre de dominio personalizado para un Sitio web Azure

 
<div  class="dev-center-tutorial-selector sublanding"><a  href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Personalizar dominio" class="current">Personalizar dominio</a><a  href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a  href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a  href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a  href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a  href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a  href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a  href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a  href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

 
<div  class="dev-center-tutorial-subselector"><a  href="/en-us/documentation/articles/web-sites-custom-domain-name/" title="Sitios web" class="current">Sitios web</a> | <a  href="/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Sitio web utilizando el Administrador de tráfico">Sitio web utilizando el Administrador de tráfico</a></div>

 [WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

En este artículo se ofrecen instrucciones generales para utilizar un
nombre de dominio personalizado con Sitios web Azure. Consulte las
pestañas que aparecen en la parte superior de este articulo para
comprobar si aparece su registrador de dominios. Si es así, seleccione
la pestaña correspondiente para ver los pasos específicos de ese
registrador en particular.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

* [Descripción de los registros DNS](#understanding-records)
* [Configuración de los sitios web para el modo básico, compartido o
  estándar](#bkmk_configsharedmode)
* [Incorporación de un registro DNS para el dominio
  personalizado](#bkmk_configurecname)
* [Habilitación del dominio en su sitio web](#enabledomain)

<h2><a name="understanding-records"></a>Descripción de los registros DNS</h2>


[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuración de los sitios web para el modo básico, compartido o estándar</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Incorporación de un registro DNS para el dominio
personalizado</h2>

Para asociar su dominio personalizado con un Sitio web Azure, debe
agregar una nueva entrada en la tabla DNS para el dominio personalizado
mediante las herramientas proporcionadas por el registrador de dominios
al que ha adquirido su nombre de dominio. Siga estos pasos para ubicar y
utilizar las herramientas DNS.

1.  Inicie sesión en su cuenta en el registrador de dominios y busque la
    página de administración de los registros DNS. Busque los vínculos o
    áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name
    Server Management**. A menudo se puede encontrar un vínculo a esta
    página al consultar la información de la cuenta (suele poner algo
    parecido a **My domains**).

2.  Cuando haya encontrado la página de administración para su nombre de
    dominio, busque un vínculo que le permita modificar los registros
    DNS. Debe aparecer como **Zone file**, **DNS Records** o un vínculo
    de configuración a las opciones avanzadas (**Advanced**).
    
    * Esta página seguramente mostrará algunos que ya se han creado,
      como una entrada en la que se asocia "**@**" o "\*" a una página
      donde figuran los dominios. Es posible también que contenga
      registros para los subdominios más comunes, como **www**.
    * Esta página mencionará los registros **A records** y **CNAME
      records**, o bien facilitará una lista desplegable donde podrá
      seleccionar el tipo de registro. Es posible también que mencione
      otros registros, como **MX records**. En algunos casos, recibirán
      otros nombres, como **IP Address records** en lugar de "A
      records", o **Alias Records** en lugar de "CNAME records".
    * Esta página contendrá también campos que le permiten **asignar**
      desde un **nombre de host** o un **nombre de dominio** hasta una
      **dirección IP** u otro nombre de dominio.

3.  Aunque los detalles varían en función del registrador que se esté
    utilizando, en general se asigna *desde* el nombre del dominio
    personalizado (como **contoso.com**) *hasta* el nombre de dominio
    del Sitio web Azure (**contoso.azurewebsites.net**) o la dirección
    IP virtual del Sitio web Azure.
    
    * Los registros CNAME asignarán siempre el dominio de Sitios web
      Azure - **contoso.azurewebsites.net**. Por tanto, estará asignando
      *desde* un dominio como **www** *hasta* su dirección
      **<nombresitioweb>.azurewebsites.net**.
      
      > [WACOM.NOTE] Si utiliza un registro D, tiene que agregar
      > también un registro CNAME con alguna de las configuraciones
      > siguientes:
      > 
      > * Asigna *desde* **www** *hasta* su
      >   **<nombresitioweb>.azurewebsites.net**.
      > 
      > O
      > 
      > * Asigna *desde* **awverify.www** *hasta*
      >   **awverify.<nombresitioweb>.azurewebsites.net**.
      > 
      > Azure utiliza este registro CNAME para comprobar que el dominio
      > descrito por el registro D es efectivamente suyo.
    
    * Los registros D asignan siempre hasta la dirección IP virtual de
      Sitios web Azure. Por tanto, asigna *desde* un dominio como
      **www** *hasta* la dirección IP virtual del sitio web.
      
      > [WACOM.NOTE] Para asignar un dominio raíz (como
      > **contoso.com**) a un Sitio web Azure, normalmente tendrá que
      > asignar desde "**@**" o una entrada en blanco hasta la dirección
      > IP virtual. Para crear una asignación comodín que asigne todos
      > los subdominios a la dirección IP virtual, normalmente tendrá
      > que asignar desde "\*" hasta la dirección IP virtual.
      > 
      > Los detalles de asignación de una raíz o un comodín cambian en
      > función del registrador utilizado. Consulte la documentación de
      > su registrador para obtener información más específica.

4.  Una vez que haya terminado de agregar o modificar registros DNS en
    su registrador, guarde los cambios.

<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

