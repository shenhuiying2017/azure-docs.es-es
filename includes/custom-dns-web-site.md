# Configuración de un nombre de dominio personalizado para un sitio web de Azure.

Cuando crea un sitio web, Azure proporciona un subdominio fácil de usar en el dominio azurewebsites.net para que los usuarios puedan obtener acceso a su sitio web mediante una URL como [http://\<mysite][]\>.azurewebsites.net. Sin embargo, si configura sitios web para el modo estándar o compartido, puede asignar su sitio web a su propio nombre de dominio.

También puede utilizar el Administrador de tráfico de Azure para equilibrar la carga del tráfico entrante en su sitio web. Para obtener más información acerca de cómo trabaja el Administrador de tráfico con Sitios web, consulte [Control del tráfico de Sitios web de Azure con el Administrador de tráfico de Azure][].

> [WACOM.NOTE] Los procedimientos de esta tarea se aplican a los Sitios web de Azure; para los Servicios en la nube, consulte [Configuración de un nombre de dominio personalizado en Azure][].

> [WACOM.NOTE] Los pasos de esta tarea requieren que configure sus sitios web para el modo estándar o compartido, que puede cambiar la cantidad que se le cobrará por su suscripción. Consulte [Detalles de precios][] para obtener más información.

En este artículo:

-   [Descripción de los registros D y CNAME][]
-   [Configuración de los sitios web para el modo compartido o estándar][]
-   [Incorporación de sus sitios web al Administrador de tráfico][]
-   [Incorporación de un CNAME para el dominio personalizado][]
-   [Incorporación de un registro D para el dominio personalizado][]

## <a name="understanding-records"></a>Descripción de los registros D y CNAME

Los registros D y CNAME (o registros de alias) le permiten asociar un nombre de dominio a un sitio web. Sin embargo, cada uno funciona de manera distinta.

### Registro de CNAME o Alias

Un registro CNAME asigna un dominio *específico*, como **contoso.com** o **www.contoso.com**, a un nombre de dominio canónico. En este caso, el nombre de dominio canónico es el nombre de dominio **\<myapp\>.azurewebsites.net** del sitio web Azure o el nombre de dominio **\<myapp\>.trafficmgr.com** de su perfil del Administrador de tráfico. Una vez creado, CNAME genera un alias para el nombre de dominio **\<myapp\>.azurewebsites.net** o **\<myapp\>.trafficmgr.com**. La entrada de CNAME se resolverá en la dirección IP del servicio del nombre de dominio **\<myapp\>.azurewebsites.net** o **\<myapp\>.trafficmgr.com** de manera automática, por lo que si la dirección IP del sitio web cambia, no tiene que realizar ninguna acción.

> [WACOM.NOTE] Algunos registradores de dominios solo le permiten asignar subdominios cuando utilizan un registro CNAME (como www.contoso.com) y no nombres raíz (como contoso.com). Para obtener más información acerca de registros CNAME, consulte la documentación que proporciona su registrador, la [entrada de la Wikipedia sobre el registro CNAME][] o el documento [IETF Domain Names - Implementation and Specification][].

### Registro D

El registro D asigna un dominio, como **contoso.com** o **www.contoso.com**, *o un dominio con comodín* como **\*.contoso.com**, a una dirección IP. En el caso de un sitio web de Azure, la IP virtual del servicio o una dirección IP específica que haya adquirido para el sitio web. Por lo tanto, el principal beneficio de un registro D en relación con un registro CNAME es que puede disponer de una entrada que utilice un carácter comodín, como \***.contoso.com**, que administraría las solicitudes de varios subdominios como **mail.contoso.com**, **login.contoso.com** o **www.contoso.com**.

> [WACOM.NOTE] Puesto que un registro D se asigna a una dirección IP estática, no puede resolver automáticamente cambios en la dirección IP de su sitio web. Se proporciona una dirección IP para que se use con registros D cuando establezca la configuración del nombre de dominio personalizado para su sitio web; sin embargo, este valor podría cambiar si elimina y vuelve a crear su sitio web o cambia el modo del sitio web para que vuelva a ser gratuito.

> [WACOM.NOTE] Los registros D no se pueden utilizar para el equilibrio de carga con el Administrador de tráfico. Para obtener más información, consulte [Control del tráfico de Sitios web Azure con el Administrador de tráfico de Azure][Control del tráfico de Sitios web de Azure con el Administrador de tráfico de Azure].

<a name="bkmk_configsharedmode"></a>

## Configuración de los sitios web para el modo compartido o estándar

</p>
La configuración de un nombre de dominio personalizado en un sitio web solo está disponible para los modos estándar y compartido para los Sitios web Azure. Antes de cambiar un sitio web del modo gratuito al modo estándar o compartido, primero debe quitar los límites de gasto del sitio vigentes para la suscripción del sitio web. Para obtener más información acerca de los precios de los modos estándar y compartido, consulte la [Información sobre el precio][].

1.  En el explorador, abra el [Portal de administración][].
2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio.

    ![][]

3.  Haga clic en la pestaña **SCALE**.

    ![][1]

4.  En la sección **General**, establezca el modo del sitio web haciendo clic en **SHARED**.

    ![][2]

    > [WACOM.NOTE] Si va a utilizar el Administrador de tráfico con este sitio web, debe seleccionar el modo estándar en lugar del compartido.

5.  Haga clic en **Save**.
6.  Cuando se le solicite aumentar el coste para el modo compartido (o para el modo estándar si fue ese el que seleccionó), haga clic en **Sí** si está de acuerdo.

    <!--![][standardmode4]-->

    **Nota:**
     si recibe un error tipo "error al configurar escala del sitio web \<nombre del sitio web\> ", puede usar el botón de información para obtener más datos.

<a name="trafficmanager"></a>

## (Opcional) Incorporación de sus sitios web al Administrador de tráfico

</p>
Si desea utilizar su sitio web con el Administrador de tráfico, siga estos pasos.

1.  Si todavía no tiene un perfil de Administrador de tráfico, utilice la información que se muestra en [Crear un perfil de Traffic Manager mediante Creación rápida][] para crear uno. Anote el nombre de dominio **.trafficmgr.com** asociado a su perfil del Administrador de tráfico. Se utilizará en otro paso más adelante.

2.  Utilice la información de [Adición o eliminación de extremos][] para agregar su sitio web como un extremo en su perfil del Administrador de tráfico.

    > [WACOM.NOTE] Si su sitio web no aparece en la lista al agregar un extremo, compruebe que está configurado en modo estándar. Si desea trabajar con el Administrador de tráfico, debe utilizar el modo estándar para su sitio web.

3.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

4.  Ahora busque el lugar en el que puede seleccionar o especificar los registros CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdomains**.

5.  También debe proporcionar el alias del dominio o del subdominio para CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**.

6.  Debe proporcionar un nombre de host que sea el nombre de dominio canónico para ese alias de CNAME. Ese es el nombre **.trafficmgr.com** de su sitio web.

Por ejemplo, el siguiente registro CNAME desvía todo el tráfico de **www.contoso.com** a **contoso.trafficmgr.com**, el nombre de dominio de un sitio web:

|-------------------------------------|------------------------|
| **Alias/Nombre de host/Subdominio** | **Dominio canónico**   |
| www                                 | contoso.trafficmgr.com |

Los visitantes de **www.contoso.com** no verán nunca el verdadero host
(contoso.azurewebsite.net), por lo que el usuario final no percibirá el proceso de desvío.

> [WACOM.NOTE] Si está utilizando el Administrador de tráfico con un sitio web, no es necesario que siga los pasos de las secciones "**Incorporación de un CNAME para el dominio personalizado**" e "**Incorporación de un registro D para el dominio personalizado**". El registro CNAME creado en los pasos anteriores dirigirá el tráfico entrante al Administrador de tráfico, quien entonces dirigirá el tráfico a los extremos del sitio web.

<a name="bkmk_configurecname"></a>

## Incorporación de un CNAME para el dominio personalizado

</p>
Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro CNAME. Sin embargo, los conceptos son los mismos.

1.  Use uno de estos métodos para buscar el nombre de dominio **.azurewebsite.net** asignado a su sitio web.

    -   Inicie sesión en el [Portal de administración de Azure][Portal de administración], seleccione su sitio web, seleccione **Panel** y, a continuación, busque la entrada **Dirección URL del sitio** en la sección **Quick glance**.

    -   Instale y configure [Azure Powershell][] y, a continuación, use el siguiente comando:

            get-azurewebsite yoursitename | select hostnames

    -   Instale y configure la [Interfaz de la línea de comandos entre plataformas de Azure][] y, a continuación, use el siguiente comando:

            azure site domain list yoursitename

    Guarde este nombre **.azurewebsite.net**, puesto que se utilizará en los pasos siguientes.

2.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

3.  Ahora busque el lugar en el que puede seleccionar o especificar los registros CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdomains**.

4.  También debe proporcionar el alias del dominio o del subdominio para CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede ponerse en una lista como el símbolo <'**@**> en las herramientas de DNS de su registrador.

5.  Debe proporcionar un nombre de host que sea el nombre de dominio canónico para ese alias de CNAME. Ese es el nombre **.azurewebsite.net** de su sitio web.

Por ejemplo, el siguiente registro CNAME desvía todo el tráfico de **www.contoso.com** a **contoso.azurewebsite.net**, el nombre de dominio de un sitio web:

|-------------------------------------|--------------------------|
| **Alias/Nombre de host/Subdominio** | **Dominio canónico**     |
| www                                 | contoso.azurewebsite.net |

Los visitantes de **www.contoso.com** no verán nunca el verdadero host
(contoso.azurewebsite.net), por lo que el usuario final no percibirá el
proceso de desvío.

> [WACOM.NOTE] El ejemplo anterior solo se aplica al tráfico en el subdominio **www**. Puesto que no puede usar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio/subdominio. Si desea dirigir el tráfico desde subdominios, como \*.contoso.com, a su dirección azurewebsite.net, puede configurar una entrada **Redirección de URL** o **Desvío de URL** en la configuración de DNS o crear un registro D.

> [WACOM.NOTE] El CNAME tardará cierto tiempo en propagarse por el sistema DNS. No puede establecer el CNAME para el sitio web hasta que el CNAME se haya propagado. Puede utilizar un servicio como <http://www.digwebinterface.com/> para comprobar que el CNAME está disponible.

### Incorporación del nombre de dominio a su sitio web

Una vez que se haya propagado el registro CNAME para el nombre de dominio, debe asociarlo a su sitio web. Puede agregar el nombre de dominio personalizado definido por el registro CNAME a su sitio web mediante la Interfaz de la línea de comandos entre plataformas de Azure o con el Portal de administración de Azure.

**Para agregar un nombre de dominio con las herramientas de la línea de comandos**

Instale y configure la [Interfaz de la línea de comandos entre plataformas de Azure][] y, a continuación, use el siguiente comando:

    azure site domain add customdomain yoursitename

Por ejemplo, con el siguiente ejemplo se agregará un nombre de dominio personalizado de **www.contoso.com** al sitio web **contoso.azurewebsite.net**:

    azure site domain add www.contoso.com contoso

Puede confirmar que el nombre de dominio personalizado se agregó al sitio web mediante el siguiente comando:

    azure site domain list yoursitename

La lista que devuelve este comando debe contener el nombre de dominio personalizado y la entrada **.azurewebsite.net** predeterminada.

**Para agregar un nombre de dominio con el Portal de administración de Azure**

1.  En el explorador, abra el [Portal de administración de Azure][Portal de administración].

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** en la parte inferior de la página.

    ![][3]

3.  En el cuadro de texto **DOMAIN NAMES**, escriba el nombre de dominio que ha configurado.

    ![][4]

4.  Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **nombres de dominio** de la página **Configurar** del sitio web.

<a name="bkmk_configurearecord"></a>

## Incorporación de un registro D para el dominio personalizado

</p>
Para crear un registro D, primero debe buscar la dirección IP de su sitio web. A continuación, agregue una entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro D. Sin embargo, los conceptos son los mismos. Además de crear un registro D, debe crear también un registro CNAME que Azure utilizará para comprobar el registro D.

1.  En el explorador, abra el [Portal de administración de Azure][Portal de administración].

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y luego seleccione **Administrar dominios** en la parte inferior de la pantalla.

    ![][3]

3.  En el cuadro de diálogo **Manage custom domains**, busque **The IP Address to use when configuring A records**. Copie la dirección IP. Esta se usará cuando se cree el registro D.

4.  En el cuadro de diálogo **Manage custom domains**, escriba el nombre de dominio awverify al final del texto en la parte superior del cuadro de diálogo. Debe ser **awverify.mysite.azurewebsites.net**, donde **mysite** es el nombre de su sitio web. Copie esto, ya que es el nombre de dominio usado cuando se crea el registro CNAME de comprobación.

5.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

6.  Busque el lugar en el que puede seleccionar o especificar los registros D y CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada.

7.  Realice los siguientes pasos para crear el registro D:

    1.  Seleccione o especifique el dominio o subdominio que usará el registro D. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de comodín para todos los subdominios, especifique '\_\_\*\_\_'. De esta forma, se incluirán todos los subdominios como **mail.customdomain.com**, **login.customdomain.com** y **www.customdomain.com**.

        Si desea crear un registro D para el dominio raíz, debe aparecer como símbolo <'**@**> en las herramientas DNS del registrador.

    2.  Especifique la dirección IP del servicio en la nube en el campo proporcionado. De esta forma, se asocia la entrada del dominio utilizada en el registro D a la dirección IP de la implementación del servicio en la nube.

        Por ejemplo, el siguiente registro D desvía todo el tráfico de **contoso.com** a **137.135.70.239**, la dirección IP de nuestra aplicación implementada:

        |-------------------------------|------------------|
        | **Nombre de host/Subdominio** | **Dirección IP** |
        | @                             | 137.135.70.239   |

        En este ejemplo se crea un registro D para el dominio raíz. Si desea crear una entrada de comodín para incluir todos los subdominios, debe especificar '\_\_\*\_\_' como subdominio.

8.  A continuación, cree un registro CNAME que cuente con un alias de **awverify** y un dominio canónico de **awverify.mysite.azurewebsites.net** obtenido anteriormente.

    > [WACOM.NOTE] Es posible que un alias de awverify funcione con algunos registradores, pero puede que algunos requieran el nombre de dominio del alias completo de awverify.www.customdomainname.com o awverify.customdomainname.com.

    Por ejemplo, a continuación se crea un registro CNAME que Azure puede usar para comprobar la configuración del registro D.

    |-------------------------------------|------------------------------------|
    | **Alias/Nombre de host/Subdominio** | **Dominio canónico**               |
    | awverify                            | awverify.contoso.azurewebsites.net |

> [WACOM.NOTE] El CNAME de awverify tardará cierto tiempo en propagarse por el sistema DNS. No puede establecer el nombre de dominio personalizado definido por el registro D para el sitio web hasta que se haya propagado el CNAME de awverify. Puede utilizar un servicio como <http://www.digwebinterface.com/> para comprobar que el CNAME está disponible.

### Incorporación del nombre de dominio a su sitio web

Una vez que se haya propagado el registro CNAME de **awverify** para el nombre de dominio, puede asociar el dominio personalizado definido por el registro D al sitio web. Puede agregar el nombre de dominio personalizado definido por el registro D a su sitio web mediante la Interfaz de la línea de comandos entre plataformas de Azure o con el Portal de administración de Azure.

**Para agregar un nombre de dominio con las herramientas de la línea de comandos**

Instale y configure la [Interfaz de la línea de comandos entre plataformas de Azure][] y, a continuación, use el siguiente comando:

    azure site domain add customdomain yoursitename

Por ejemplo, con el siguiente ejemplo se agregará un nombre de dominio personalizado de **contoso.com** al sitio web **contoso.azurewebsite.net**:

    azure site domain add contoso.com contoso

Puede confirmar que el nombre de dominio personalizado se agregó al sitio web mediante el siguiente comando:

    azure site domain list yoursitename

La lista que devuelve este comando debe contener el nombre de dominio personalizado y la entrada **.azurewebsite.net** predeterminada.

**Para agregar un nombre de dominio con el Portal de administración de Azure**

1.  En el explorador, abra el [Portal de administración de Azure][Portal de administración].

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** en la parte inferior de la página.

    ![][3]

3.  En el cuadro de texto **DOMAIN NAMES**, escriba el nombre de dominio que ha configurado.

    ![][4]

4.  Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **nombres de dominio** de la página **Configurar** del sitio web.

> [WACOM.NOTE] Una vez que haya agregado el nombre de dominio personalizado definido por el registro D a su sitio web, puede quitar el registro CNAME de awverify mediante las herramientas proporcionadas por el registrador. Sin embargo, si desea agregar otro registro D más adelante, tendrá que volver a crear el registro awverify para poder asociar el nuevo nombre de dominio definido por el nuevo registro D al sitio web.

## Pasos siguientes

-   [Administración de sitios web][]

-   [Configuración de un certificado SSL para Sitios web][]

<!-- Bookmarks --> <!-- Links --> <!-- images --> <!-- images -->

  [http://\<mysite]: http://<mysite
  [Control del tráfico de Sitios web de Azure con el Administrador de tráfico de Azure]: /es-es/documentation/articles/web-sites-traffic-manager/
  [Configuración de un nombre de dominio personalizado en Azure]: /es-es/develop/net/common-tasks/custom-dns/
  [Detalles de precios]: /es-es/pricing/details/web-sites/
  [Descripción de los registros D y CNAME]: #understanding-records
  [Configuración de los sitios web para el modo compartido o estándar]: #bkmk_configsharedmode
  [Incorporación de sus sitios web al Administrador de tráfico]: #trafficmanager
  [Incorporación de un CNAME para el dominio personalizado]: #bkmk_configurecname
  [Incorporación de un registro D para el dominio personalizado]: #bkmk_configurearecord
  [entrada de la Wikipedia sobre el registro CNAME]: http://en.wikipedia.org/wiki/CNAME_record
  [IETF Domain Names - Implementation and Specification]: http://tools.ietf.org/html/rfc1035
  [Información sobre el precio]: /es-es/pricing/details/
  [Portal de administración]: http://manage.windowsazure.com
  []: ./media/custom-dns-web-site/dncmntask-cname-1.png
  [1]: ./media/custom-dns-web-site/dncmntask-cname-2.png
  [2]: ./media/custom-dns-web-site/dncmntask-cname-3.png
  [Crear un perfil de Traffic Manager mediante Creación rápida]: http://msdn.microsoft.com/es-es/library/windowsazure/dn339012.aspx
  [Adición o eliminación de extremos]: http://msdn.microsoft.com/es-es/library/windowsazure/hh744839.aspx
  [Azure Powershell]: /es-es/manage/install-and-configure-windows-powershell/
  [Interfaz de la línea de comandos entre plataformas de Azure]: /es-es/manage/install-and-configure-cli/
  [3]: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [4]: ./media/custom-dns-web-site/dncmntask-cname-7.png
  [Administración de sitios web]: /es-es/manage/services/web-sites/how-to-manage-websites/
  [Configuración de un certificado SSL para Sitios web]: /es-es/develop/net/common-tasks/enable-ssl-web-site/
