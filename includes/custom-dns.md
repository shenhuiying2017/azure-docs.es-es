# Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure

> [WACOM.NOTE]
> Póngase en marcha más rápido: use el NUEVO [tutorial guiado][tutorial guiado] de Azure. Con este tutorial, asociar un nombre de dominio personalizado y proteger las comunicaciones (SSL) con los Servicios en la nube de Azure o Sitios web Azure es facilísimo.

Al crear una aplicación en Azure, Azure proporciona un subdominio en el dominio cloudapp.net de modo que sus usuarios pueden tener acceso a su aplicación en una URL como http://\<*myapp*\>.cloudapp.net. Sin embargo, también puede exponer su aplicación en su propio nombre de dominio, como por ejemplo contoso.com.

> [WACOM.NOTE]
> Los procedimientos de esta tarea se aplican a los Servicios en la nube de Azure. Para obtener información sobre las cuentas de almacenamiento, consulte [Configuración de un nombre de dominio personalizado para una cuenta de Almacenamiento de Azure][Configuración de un nombre de dominio personalizado para una cuenta de Almacenamiento de Azure]. Para obtener información sobre Sitios web, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure][Configuración de un nombre de dominio personalizado para un sitio web de Azure].

En este artículo:

-   [Descripción de los registros D y CNAME][Descripción de los registros D y CNAME]
-   [Incorporación de un CNAME al dominio personalizado][Incorporación de un CNAME al dominio personalizado]
-   [Incorporación de un registro D para el dominio personalizado][Incorporación de un registro D para el dominio personalizado]

## <a name="access-app"></a>Descripción de los registros D y CNAME

Los registros D y CNAME (o registros de alias) le permiten asociar un nombre de dominio a un servidor específico (o, en este caso, servicio). Sin embargo, cada uno funciona de manera distinta. Existen también algunas consideraciones específicas al usar los registros D con los Servicios en la nube de Azure que debe considerar antes de decidir cuál usar.

### Registro de CNAME o Alias

Un registro CNAME asigna un dominio *específico*, como **contoso.com** o **www.contoso.com**, a un nombre de dominio canónico. En este caso, el nombre de dominio canónico es el nombre de dominio **\<miaplicación\>.cloudapp.net** de su aplicación hospedada de Azure. Una vez creado, CNAME genera un alias para **\<miaplicación\>.cloudapp.net**. La entrada de CNAME se resolverá en la dirección IP del servicio de **\<miaplicación\>.cloudapp.net** de manera automática, por lo que si la dirección IP del servicio en la nube cambia, no tiene que realizar ninguna acción.

> [WACOM.NOTE]
> Algunos registradores de dominios solo le permiten asignar subdominios cuando utilizan un registro CNAME (como www.contoso.com) y no nombres raíz (como contoso.com). Para obtener más información acerca de registros CNAME, consulte la documentación que proporciona su registrador, la [entrada de la Wikipedia sobre el registro CNAME][entrada de la Wikipedia sobre el registro CNAME] o el documento sobre [nombres de dominio IETF; implementación y especificación][nombres de dominio IETF; implementación y especificación].

### Registro D

El registro D asigna un dominio, como **contoso.com** o **www.contoso.com**, *o un dominio con comodín* como **\*.contoso.com**, a una dirección IP. En el caso de un Servicio en la nube de Azure, la IP virtual del servicio. Por lo tanto, el principal beneficio de un registro D en relación con un registro CNAME es que puede disponer de una entrada que utilice un carácter comodín, como \***.contoso.com**, que administraría las solicitudes de varios subdominios como **mail.contoso.com**, **login.contoso.com** o **www.contoso.com**.

> [WACOM.NOTE]
> Puesto que un registro D se asigna a una dirección IP estática, no puede resolver automáticamente cambios en la dirección IP de su Servicio en la nube. La dirección IP que usa el Servicio en la nube se asigna la primera vez que se implementa en una ranura vacía (producción o ensayo). Si elimina la implementación para la ranura, Azure libera la dirección IP y a toda implementación posterior en la ranura se le podrá dar una dirección IP nueva.
>
> De manera conveniente, la dirección IP de una ranura de implementación dada (producción o ensayo) se mantiene cuando se realiza un intercambio entre las implementaciones de ensayo y producción o se realiza una actualización local de una implementación existente. Para obtener más información sobre la realización de estas acciones, consulte [Administración de servicios en la nube][Administración de servicios en la nube].

## <a name="add-cname"></a>Incorporación de un CNAME al dominio personalizado

Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro CNAME. Sin embargo, los conceptos son los mismos.

1.  Use uno de estos métodos para buscar el nombre de dominio **.cloudapp.net** asignado a su servicio en la nube.

-   Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], seleccione su servicio en la nube, seleccione **Panel** y luego busque la entrada **Site URL** en la sección **Quick Glance**.

          ![quick glance section showing the site URL][csurl]

-   Instale y configure [Azure Powershell][Azure Powershell] y, a continuación, use el siguiente comando:

    Get-AzureDeployment -ServiceName yourservicename | Select Url

Guarde el nombre de dominio que se usó en la URL devuelta por cualquier método, ya que la necesitará al crear un registro de CNAME.

1.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

2.  Busque ahora dónde puede seleccionar o escribir el de CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdomains**.

3.  Debe también proporcionar el alias del dominio o subdominio para el CNAME, como **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede ponerse en una lista como el símbolo '**@**' en las herramientas de DNS de su registrador.

4.  A continuación, debe proporcionar un nombre de host canónico que, en este caso, es el dominio **cloudapp.net** de su aplicación.

Por ejemplo, el siguiente registro de CNAME reenvía todo el tráfico de **www.contoso.com** a **contoso.cloudapp.net**, el nombre de dominio personalizado de su aplicación implementada:

|-------------------------------------|----------------------|
| **Alias/Nombre de host/Subdominio** | **Dominio canónico** |
| www                                 | contoso.cloudapp.net |

Los visitantes de **www.contoso.com** no verán nunca el verdadero host
(contoso.cloudapp.net), por lo que el usuario final no percibirá el
proceso de desvío.

> [WACOM.NOTE]
> El ejemplo anterior solo se aplica al tráfico en el subdominio **www**. Puesto que no puede usar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio/subdominio. Si desea dirigir el tráfico desde subdominios, como \*.contoso.com, a su dirección cloudapp.net, puede configurar una entrada **Redirección de URL** o **Desvío de URL** en la configuración de DNS o crear un registro D.

## <a name="add-aname"></a>Incorporación de un registro D para el dominio personalizado

Para crear un registro D, primero debe buscar la dirección IP virtual de su servicio en la nube. A continuación, agregue una entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro D. Sin embargo, los conceptos son los mismos.

1.  Use uno de los siguientes métodos para obtener la dirección IP de su servicio en la nube.

-   Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], seleccione su servicio en la nube, seleccione **Panel** y luego busque la entrada **Public Virtual IP (VIP) address** en la sección **Quick Glance**.

         ![quick glance section showing the VIP][vip]

-   Instale y configure [Azure Powershell][Azure Powershell] y, a continuación, use el siguiente comando:

    get-azurevm -servicename yourservicename | get-azureendpoint -VM {$\_.VM} | select Vip

    Si tiene varios extremos asociados con su servicio en la nube, recibirá varias líneas que contienen la dirección IP, pero todas deben mostrar la misma dirección.

Guarde la dirección IP, debido a que la necesitará cuando cree un registro D.

1.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetadas como **Domain Name**, **DNS** o **Name Server Management**.

2.  Busque ahora dónde puede seleccionar o escribir el de un registro D Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada.

3.  Seleccione o especifique el dominio o subdominio que usará el registro D. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de comodín para todos los subdominios, especifique '\_\_\*\_\_'. De esta forma, se incluirán todos los subdominios como **mail.customdomain.com**, **login.customdomain.com** y **www.customdomain.com**.

Si desea crear un registro D para el dominio raíz, debe aparecer con un símbolo **@** en las herramientas DNS del registrador.

1.  Especifique la dirección IP del servicio en la nube en el campo proporcionado. De esta forma, se asocia la entrada del dominio utilizada en el registro D a la dirección IP de la implementación del servicio en la nube.

Por ejemplo, el siguiente registro D desvía todo el tráfico de **contoso.com** a **137.135.70.239**, la dirección IP de su aplicación implementada:

|-------------------------------|------------------|
| **Nombre de host/Subdominio** | **Dirección IP** |
| @                             | 137.135.70.239   |

En este ejemplo se crea un registro D para el dominio raíz. Si desea crear una entrada de comodín para incluir todos los subdominios, debe especificar '\_\_\*\_\_' como subdominio.

## Pasos siguientes

-   [Administración de servicios en la nube][Administración de servicios en la nube]
-   [Asignación del contenido de la red CDN a un dominio personalizado][Asignación del contenido de la red CDN a un dominio personalizado]

  [tutorial guiado]: http://support.microsoft.com/kb/2990804
  [Configuración de un nombre de dominio personalizado para una cuenta de Almacenamiento de Azure]: ../storage-custom-domain-name/
  [Configuración de un nombre de dominio personalizado para un sitio web de Azure]: ../web-sites-custom-domain-name/
  [Descripción de los registros D y CNAME]: #access-app
  [Incorporación de un CNAME al dominio personalizado]: #add-cname
  [Incorporación de un registro D para el dominio personalizado]: #add-aname
  [entrada de la Wikipedia sobre el registro CNAME]: http://en.wikipedia.org/wiki/CNAME_record
  [nombres de dominio IETF; implementación y especificación]: http://tools.ietf.org/html/rfc1035
  [Administración de servicios en la nube]: ../cloud-services-how-to-manage/
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Azure Powershell]: ../install-configure-powershell/
  [Asignación del contenido de la red CDN a un dominio personalizado]: http://msdn.microsoft.com/es-es/library/windowsazure/gg680307.aspx
