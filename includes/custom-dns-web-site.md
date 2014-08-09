
# Configuración de un nombre de dominio personalizado para un Sitio web Azure

Cuando crea un sitio web, Azure proporciona un subdominio fácil de usar en el dominio azurewebsites.net para que los usuarios puedan obtener acceso a su sitio web mediante una URL como http://&lt;mysite&gt;.azurewebsites.net. Sin embargo, si configura sitios web para el modo estándar o compartido, puede asignar su sitio web a su propio nombre de dominio.

También puede utilizar el Administrador de tráfico de Azure para equilibrar la carga del tráfico entrante en su sitio web. Para obtener más información acerca de cómo trabaja el Administrador de tráfico con Sitios web, consulte [Control del tráfico de Sitios web Azure con el Administrador de tráfico de Azure](/es-es/documentation/articles/web-sites-traffic-manager/).

> \[WACOM.NOTE\] Los procedimientos de esta tarea se aplican a los Sitios web Azure; para los Servicios en la nube, consulte [Configuración de un nombre de dominio personalizado en Azure][1]

> \[WACOM.NOTE\] Los pasos de esta tarea requieren que configure sus sitios web para el modo estándar o compartido, que puede cambiar la cantidad que se le cobrará por su suscripción. Consulte [Información sobre el precio de Sitios web][2] para obtener más información.

En este artículo:

* [Descripción de los registros D y CNAME](#understanding-records)
* [Configuración de los sitios web para el modo compartido o estándar](#bkmk_configsharedmode)
* [Incorporación de sus sitios web al Administrador de
  tráfico](#trafficmanager)
* [Incorporación de un CNAME para el dominio
  personalizado](#bkmk_configurecname)
* [Incorporación de un registro D para el dominio
  personalizado](#bkmk_configurearecord)

<h2><a name="understanding-records" ></a>Descripción de los registros D y CNAME</h2>


Los registros D y CNAME (o registros de alias) le permiten asociar un nombre de dominio a un sitio web. Sin embargo, cada uno funciona de manera distinta.

### Registro de alias o CNAME

Un registro CNAME asigna un dominio *específico*, como **contoso.com** o **www.contoso.com**, a un nombre de dominio canónico. En este caso, el nombre de dominio canónico es el nombre de dominio **&lt;myapp&gt;.azurewebsites.net** del sitio web Azure o el nombre de dominio **&lt;myapp&gt;.trafficmgr.com** de su perfil del Administrador de tráfico. Una vez creado, CNAME genera un alias para el nombre de dominio **&lt;myapp&gt;.azurewebsites.net** o **&lt;myapp&gt;.trafficmgr.com**. La entrada de CNAME se resolverá en la dirección IP del servicio del nombre de dominio **&lt;myapp&gt;.azurewebsites.net** o **&lt;myapp&gt;.trafficmgr.com** de manera automática, por lo que si la dirección IP del sitio web cambia, no tiene que realizar ninguna acción.

> \[WACOM.NOTE\] Algunos registradores de dominios solo le permiten asignar subdominios cuando utilizan un registro CNAME (como www.contoso.com) y no nombres raíz (como contoso.com). Para obtener más información acerca de registros CNAME, consulte la documentación que proporciona su registrador, la [entrada de la Wikipedia sobre el registro CNAME][3] o el documento [IETF Domain Names - Implementation and Specification][4].

### Registro D

El registro D asigna un dominio, como **contoso.com** o **www.contoso.com**, *o un dominio con comodín* como **\*.contoso.com**, a una dirección IP. En el caso de un Sitio web Azure, la IP virtual del servicio o una dirección IP específica que haya adquirido para el sitio web. Por lo tanto, el principal beneficio de un registro D en relación con un registro CNAME es que puede disponer de una entrada que utilice un carácter comodín, como **\*.contoso.com**, que administraría las solicitudes de varios subdominios como **mail.contoso.com**, **login.contoso.com** o **www.contoso.com**.

> \[WACOM.NOTE\] Puesto que un registro D se asigna a una dirección IP estática, no puede resolver automáticamente cambios en la dirección IP de su sitio web. Se proporciona una dirección IP para que se use con registros D cuando establezca la configuración del nombre de dominio personalizado para su sitio web; sin embargo, este valor podría cambiar si elimina y vuelve a crear su sitio web o cambia el modo del sitio web para que vuelva a ser gratuito.

> \[WACOM.NOTE\] Los registros D no se pueden utilizar para el equilibrio de carga con el Administrador de tráfico. Para obtener más información, consulte [Control del tráfico de Sitios web Azure con el Administrador de tráfico de Azure](/es-es/documentation/articles/web-sites-traffic-manager/).

<a name="bkmk_configsharedmode" ></a><h2>Configuración de los sitios web para el modo compartido o estándar</h2>

La configuración de un nombre de dominio personalizado en un sitio web solo está disponible para los modos estándar y compartido para los Sitios web Azure. Antes de cambiar un sitio web del modo gratuito al modo estándar o compartido, primero debe quitar los límites de gasto del sitio vigentes para la suscripción del sitio web. Para obtener más información acerca de los precios de los modos estándar y compartido, consulte la [Información sobre el precio][5].

1.  En el explorador, abra el [Portal de administración][6]. 
2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-1.png)

3.  Haga clic en la pestaña **SCALE**.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-2.png)

4.  En la sección **General**, establezca el modo del sitio web haciendo clic en **SHARED**.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-3.png)
    
    > \[WACOM.NOTE\] Si va a utilizar el Administrador de tráfico con este sitio web, debe seleccionar el modo estándar en lugar del compartido.

5.  Haga clic en **Save**. 6.  Cuando se le solicite aumentar el coste para el modo compartido (o para el modo estándar si fue ese el que seleccionó), haga clic en **Sí** si está de acuerdo.
    
    <!--![][standardmode4]-->
    
    **Nota:**  
     si recibe un error tipo "error al configurar escala del sitio web &lt;nombre del sitio web&gt; ", puede usar el botón de información para obtener más datos.

<a name="trafficmanager" ></a><h2>(Opcional) Incorporación de sus sitios web al Administrador de tráfico</h2>

Si desea utilizar su sitio web con el Administrador de tráfico, siga estos pasos.

1.  Si todavía no tiene un perfil en el Administrador de tráfico, utilice la información en [Creación de un perfil del Administrador de tráfico utilizando Quick Create][7] para crear uno. Anote el nombre de dominio **.trafficmgr.com** asociado a su perfil del Administrador de tráfico. Se utilizará en otro paso más adelante.

2.  Utilice la información de [Adición o eliminación de extremos][8] para agregar su sitio web como un extremo en su perfil del Administrador de tráfico.
    
    > \[WACOM.NOTE\] Si su sitio web no aparece en la lista al agregar un extremo, compruebe que está configurado en modo estándar. Si desea trabajar con el Administrador de tráfico, debe utilizar el modo estándar para su sitio web.

3.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

4.  Ahora busque el lugar en el que puede seleccionar o especificar los registros CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdomains**.

5.  También debe proporcionar el alias del dominio o del subdominio para CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**.

6.  Debe proporcionar un nombre de host que sea el nombre de dominio canónico para ese alias de CNAME. Ese es el nombre **.trafficmgr.com** de su sitio web.

Por ejemplo, el siguiente registro CNAME desvía todo el tráfico de **www.contoso.com** a **contoso.trafficmgr.com**, el nombre de dominio de un sitio web:

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nombre de host/Subdominio</strong>
</td>

<td><strong>Dominio can&oacute;nico</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.trafficmgr.com</td>

</tr>

</table>

Los visitantes de **www.contoso.com** no verán nunca el verdadero host
(contoso.azurewebsite.net), por lo que el usuario final no percibirá el
proceso de desvío.

> \[WACOM.NOTE\] Si está utilizando el Administrador de tráfico con un sitio web, no es necesario que siga los pasos de las secciones "**Incorporación de un CNAME para el dominio personalizado**" e "**Incorporación de un registro D para el dominio personalizado**". El registro CNAME creado en los pasos anteriores dirigirá el tráfico entrante al Administrador de tráfico, quien entonces dirigirá el tráfico a los extremos del sitio web.

<a name="bkmk_configurecname" ></a><h2 data-morhtml="true">Incorporación de un CNAME para el dominio personalizado</h2>

Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro CNAME. Sin embargo, los conceptos son los mismos.

1.  Use uno de estos métodos para buscar el nombre de dominio **.azurewebsite.net** asignado a su sitio web.
    
    * Inicie sesión en el [Portal de administración de Azure][6], seleccione su sitio web, seleccione **Panel** y, a continuación, busque la entrada **Dirección URL del sitio** en la sección **Quick glance**.
    
    * Instale y configure [Azure Powershell][9] y, a continuación, use el siguiente comando:
      
            get-azurewebsite yoursitename | select hostnames
    
    * Instale y configure la [Interfaz de la línea de comandos entre plataformas de Azure][10] y, a continuación, use el siguiente comando:
      
            azure site domain list yoursitename
    
    Guarde este nombre **.azurewebsite.net**, puesto que se utilizará en los pasos siguientes.

2.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

3.  Ahora busque el lugar en el que puede seleccionar o especificar los registros CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdomains**.

4.  También debe proporcionar el alias del dominio o del subdominio para CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede ponerse en una lista como el símbolo '**@**' en las herramientas de DNS de su registrador.

5.  Debe proporcionar un nombre de host que sea el nombre de dominio canónico para ese alias de CNAME. Ese es el nombre **.azurewebsite.net** de su sitio web.

Por ejemplo, el siguiente registro CNAME desvía todo el tráfico de **www.contoso.com** **contoso.azurewebsite.net**, el nombre de dominio de un sitio web:

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nombre de host/Subdominio</strong>
</td>

<td><strong>Dominio can&oacute;nico</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.azurewebsite.net</td>

</tr>

</table>

Los visitantes de **www.contoso.com** no verán nunca el verdadero host
(contoso.azurewebsite.net), por lo que el usuario final no percibirá el
proceso de desvío.

> \[WACOM.NOTE\] El ejemplo anterior solo se aplica al tráfico en el subdominio **www**. Puesto que no puede usar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio/subdominio. Si desea dirigir el tráfico desde subdominios, como \*.contoso.com, a su dirección azurewebsite.net, puede configurar una entrada **Redirección de URL** o **Desvío de URL** en la configuración de DNS o crear un registro D.

> \[WACOM.NOTE\] El CNAME tardará cierto tiempo en propagarse por el sistema DNS. No puede establecer el CNAME para el sitio web hasta que el CNAME se haya propagado. Puede utilizar un servicio como [http://www.digwebinterface.com/][11] para comprobar que el CNAME está disponible.

### Incoporación del nombre de dominio a su sitio web

Una vez que se haya propagado el registro CNAME para el nombre de dominio, debe asociarlo a su sitio web. Puede agregar el nombre de dominio personalizado definido por el registro CNAME a su sitio web mediante la Interfaz de la línea de comandos entre plataformas de Azure o con el Portal de administración de Azure.

**Para agregar un nombre de dominio con las herramientas de la línea de comandos**

Instale y configure la [Interfaz de la línea de comandos entre plataformas de Azure][10] y, a continuación, use el siguiente comando:

    azure site domain add customdomain yoursitename

Por ejemplo, con el siguiente ejemplo se agregará un nombre de dominio personalizado de **www.contoso.com** al sitio web **contoso.azurewebsite.net**\:

    azure site domain add www.contoso.com contoso

Puede confirmar que el nombre de dominio personalizado se agregó al sitio web mediante el siguiente comando:

    azure site domain list yoursitename

La lista que devuelve este comando debe contener el nombre de dominio personalizado y la entrada **.azurewebsite.net** predeterminada.

**Para agregar un nombre de dominio con el Portal de administración de
Azure**

1.  En el explorador, abra el [Portal de administración de Azure][6].

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Manage Domains** en la parte inferior de la página.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  En el cuadro de texto **DOMAIN NAMES**, escriba el nombre de dominio que ha configurado.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **domain names** de la página **Configure** del sitio web.

<a name="bkmk_configurearecord" ></a><h2>Incorporación de un registro D para el dominio personalizado</h2>

Para crear un registro D, primero debe buscar la dirección IP de su sitio web. A continuación, agregue una entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro D. Sin embargo, los conceptos son los mismos. Además de crear un registro D, debe crear también un registro CNAME que Azure utilizará para comprobar el registro D.

1.  En el explorador, abra el [Portal de administración de Azure][6].

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Manage Domains** en la parte inferior de la pantalla.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  En el cuadro de diálogo **Manage custom domains**, busque **The IP Address to use when configuring A records**. Copie la dirección IP. Esta se usará cuando se cree el registro D.

4.  En el cuadro de diálogo **Manage custom domains**, escriba el nombre de dominio awverify al final del texto en la parte superior del cuadro de diálogo. Debe ser **awverify.mysite.azurewebsites.net**, donde **mysite** es el nombre de su sitio web. Copie esto, ya que es el nombre de dominio usado cuando se crea el registro CNAME de comprobación.

5.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

6.  Busque el lugar en el que puede seleccionar o especificar los registros D y CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada.

7.  Realice los siguientes pasos para crear el registro D:
    
    1.  Seleccione o especifique el dominio o subdominio que usará el registro D. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de comodín para todos los subdominios, especifique **\***. De esta forma, se incluirán todos los subdominios como **mail.customdomain.com**, **login.customdomain.com** y **www.customdomain.com**.
        
        Si desea crear un registro D para el dominio raíz, debe aparecer con un símbolo **@** en las herramientas DNS del registrador.
    
    2.  Especifique la dirección IP del servicio en la nube en el campo proporcionado. De esta forma, se asocia la entrada del dominio utilizada en el registro D a la dirección IP de la implementación del servicio en la nube.
        
        Por ejemplo, el siguiente registro D desvía todo el tráfico de **contoso.com** a **137.135.70.239**, la dirección IP de nuestra aplicación implementada:
        
        <table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
         <tr>
         <td><strong>Nombre de host/Subdominio</strong>
        </td>
        
         <td><strong>Direcci&oacute;n IP</strong>
        </td>
        
         </tr>
        
         <tr>
         <td>@</td>
        
         <td>137.135.70.239</td>
        
         </tr>
        
         </table>
        
        En este ejemplo se crea un registro D para el dominio raíz. Si desea crear una entrada de comodín para incluir todos los subdominios, debe especificar **\*** como el subdominio.

8.  A continuación, cree un registro CNAME que cuente con un alias de **awverify** y un dominio canónico de **awverify.mysite.azurewebsites.net** obtenido anteriormente.
    
    > \[WACOM.NOTE\] Es posible que un alias de awverify funcione con algunos registradores, pero puede que algunos requieran el nombre de dominio del alias completo de awverify.www.customdomainname.com o awverify.customdomainname.com.
    
    Por ejemplo, a continuación se crea un registro CNAME que Azure puede usar para comprobar la configuración del registro D.
    
    <table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
     <tr>
     <td><strong>Alias/Nombre de host/Subdominio</strong>
    </td>
    
     <td><strong>Dominio can&oacute;nico</strong>
    </td>
    
     </tr>
    
     <tr>
     <td>awverify</td>
    
     <td>awverify.contoso.azurewebsites.net</td>
    
     </tr>
    
     </table>

> \[WACOM.NOTE\] El CNAME de awverify tardará cierto tiempo en propagarse por el sistema DNS. No puede establecer el nombre de dominio personalizado definido por el registro D para el sitio web hasta que se haya propagado el CNAME de awverify. Puede utilizar un servicio como [http://www.digwebinterface.com/][11] para comprobar que el CNAME está disponible.

### Incoporación del nombre de dominio a su sitio web

Una vez que se haya propagado el registro CNAME de **awverify** para el nombre de dominio, puede asociar el dominio personalizado definido por el registro D al sitio web. Puede agregar el nombre de dominio personalizado definido por el registro D a su sitio web mediante la Interfaz de la línea de comandos entre plataformas de Azure o con el Portal de administración de Azure.

**Para agregar un nombre de dominio con las herramientas de la línea de comandos**

Instale y configure la [Interfaz de la línea de comandos entre plataformas de Azure][10] y, a continuación, use el siguiente comando:

    azure site domain add customdomain yoursitename

Por ejemplo, con el siguiente ejemplo se agregará un nombre de dominio personalizado de **contoso.com** al sitio web **contoso.azurewebsite.net**:

    azure site domain add contoso.com contoso

Puede confirmar que el nombre de dominio personalizado se agregó al sitio web mediante el siguiente comando:

    azure site domain list yoursitename

La lista que devuelve este comando debe contener el nombre de dominio personalizado y la entrada **.azurewebsite.net** predeterminada.

**Para agregar un nombre de dominio con el Portal de administración de Azure**

1.  En el explorador, abra el [Portal de administración de Azure][6].

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Manage Domains** en la parte inferior de la página.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  En el cuadro de texto **DOMAIN NAMES**, escriba el nombre de dominio que ha configurado.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **domain names** de la página **Configure** del sitio web.

> \[WACOM.NOTE\] Una vez que haya agregado el nombre de dominio personalizado definido por el registro D a su sitio web, puede quitar el registro CNAME de awverify mediante las herramientas proporcionadas por el registrador. Sin embargo, si desea agregar otro registro D más adelante, tendrá que volver a crear el registro awverify para poder asociar el nuevo nombre de dominio definido por el nuevo registro D al sitio web.

## Pasos siguientes

* [Administración de sitios web][12]

* [Configuración de un certificado SSL para Sitios web][13]

<!-- Bookmarks -->

<!-- Links -->

<!-- images -->

<!-- images -->



[1]: http://www.windowsazure.com/es-es/develop/net/common-tasks/custom-dns/
[2]: http://www.windowsazure.com/es-es/pricing/details/web-sites/
[3]: http://en.wikipedia.org/wiki/CNAME_record
[4]: http://tools.ietf.org/html/rfc1035
[5]: https://www.windowsazure.com/en-us/pricing/details/
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/es-es/library/windowsazure/dn339012.aspx
[8]: http://msdn.microsoft.com/es-es/library/windowsazure/hh744839.aspx
[9]: http://www.windowsazure.com/es-es/manage/install-and-configure-windows-powershell/
[10]: http://www.windowsazure.com/es-es/manage/install-and-configure-cli/
[11]: http://www.digwebinterface.com/
[12]: http://www.windowsazure.com/es-es/manage/services/web-sites/how-to-manage-websites/
[13]: http://www.windowsazure.com/es-es/develop/net/common-tasks/enable-ssl-web-site/