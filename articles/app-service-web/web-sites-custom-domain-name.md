<properties
	pageTitle="Asignación de un nombre de dominio personalizado a una aplicación de Azure"
	description="Obtenga información acerca de cómo asignar un nombre de dominio personalizado (dominio personal) a su aplicación en el Servicio de aplicaciones de Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="cephalin"/>

# Asignación de un nombre de dominio personalizado a una aplicación de Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

En este artículo se muestra cómo asignar manualmente un nombre de dominio personalizado a su aplicación web, back-end de aplicación móvil o aplicación de API en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

La aplicación ya incluye un único subdominio de azurewebsites.net. Por ejemplo, si la aplicación se llama **contoso**, su nombre de dominio será **contoso.azurewebsites.net**. Sin embargo, puede asignar un nombre de dominio personalizado a la aplicación para que la dirección URL, por ejemplo, `www.contoso.com`, refleje su marca.

>[AZURE.NOTE] Obtenga ayuda de expertos de Azure en los [foros de Azure](https://azure.microsoft.com/support/forums/). Para obtener soporte técnico más especializado, vaya al [sitio de Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Compra de un nuevo dominio personalizado en el Portal de Azure

Si aún no ha adquirido un nombre de dominio personalizado, puede comprar uno y administrarlo directamente en la configuración de la aplicación, en el [Portal de Azure](https://portal.azure.com). Esta opción facilita asignar un dominio personalizado a la aplicación, si su aplicación usa el [Administrador de tráfico de Azure](web-sites-traffic-manager-custom-domain-name.md) o no.

Para obtener instrucciones, consulte [Compra de un nombre de dominio personalizado para el Servicio de aplicaciones](custom-dns-web-site-buydomains-web-app.md).

## Asignación de un dominio personalizado adquirido externamente

Si ya compró un dominio personalizado a [DNS de Azure](https://azure.microsoft.com/services/dns/) o a un proveedor de terceros, debe seguir tres pasos para asignar el dominio personalizado a la aplicación:

1. [*(Solo registro A)* Obtención de la dirección IP de la aplicación](#vip).
2. [Crear los registros DNS que asignan el dominio a la aplicación](#createdns).
    - **Dónde**: en la herramienta de administración propia del registrador de dominios (por ejemplo, DNS de Azure, GoDaddy, etc.).
    - **Por qué**: para que el registrador de dominios sepa resolver el dominio personalizado deseado en su aplicación de Azure.
1. [Habilitación del nombre de dominio personalizado para la aplicación de Azure](#enable).
    - **Dónde**: en el [Portal de Azure](https://portal.azure.com).
    - **Por qué**: para que la aplicación sepa responder a las solicitudes realizadas al nombre de dominio personalizado.
3. [Comprobación de la propagación de DNS](#verify).

### Tipos de dominios que puede asignar

El Servicio de aplicaciones de Azure le permite asignar las siguientes categorías de dominios personalizados a la aplicación.

- **Dominio raíz**: el nombre de dominio que reservó con el registrador de dominios (normalmente representado por el registro de host `@`). Por ejemplo, **contoso.com**.
- **Subdominio**: cualquier dominio que se encuentra debajo del dominio raíz. Por ejemplo, **www.contoso.com** (representado por el registro de host `www`). Puede asignar distintos subdominios del mismo dominio raíz a diferentes aplicaciones de Azure.
- **Dominio comodín**: [cualquier subdominio cuya etiqueta DNS situada más a la izquierda sea `*`](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por ejemplo, los registros de host `*` y `*.blogs`). Por ejemplo, ***.contoso.com**.

### Tipos de registros DNS que puede usar

Según sus necesidades, puede usar dos tipos diferentes de registros DNS estándar para asignar su dominio personalizado:

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A): asigna el nombre de dominio personalizado directamente a la dirección IP virtual de la aplicación de Azure.
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record): asigna el nombre de dominio personalizado al nombre de dominio de Azure de la aplicación, **&lt;*appname*>. azurewebsites.net**.

La ventaja de CNAME es que se conserva durante los cambios de dirección IP. La dirección IP virtual de la aplicación puede cambiar al eliminar y volver a crear la aplicación, o al cambiar de un plan de tarifa más alto a **Compartido**. Si se realiza este cambio, los registros CNAME siguen siendo válidos, mientras que los registros A deben actualizarse.

El tutorial muestra los pasos para usar el registro y también para usar el registro CNAME.

>[AZURE.IMPORTANT] No cree un registro CNAME para el dominio raíz (es decir, el "registro raíz"). Para más información, consulte [Why can't a CNAME record be used at the root domain](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain) (Por qué no se puede usar un registro CNAME en el dominio raíz). Para asignar un dominio raíz a la aplicación de Azure, utilice en su lugar un registro A.

<a name="vip"></a>
## Paso 1. *(Solo registro A)* Obtención de la dirección IP de la aplicación
Para asignar un nombre de dominio personalizado mediante un registro A, necesita la dirección IP de la aplicación de Azure. Si se asigna usando un registro CNAME en su lugar, omita este paso y pase a la siguiente sección.

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2.	Haga clic en **Servicios de aplicaciones** en el menú izquierdo.

4.	Haga clic en la aplicación y, a continuación haga clic en **Dominios personalizados**.

6.  Tome nota de la dirección IP encima de la sección Nombres de host...

    ![Asignación de un nombre de dominio personalizado con un registro A: obtener una dirección IP para una aplicación del Servicio de aplicaciones de Azure](./media/web-sites-custom-domain-name/virtual-ip-address.png)

7.  Mantenga esta hoja del portal abierta. Volveremos a ella después de crear los registros DNS.

<a name="createdns"></a>
## Paso 2: Creación de registros DNS

Inicie sesión en el registrador de dominios y use su herramienta para agregar un registro a o un registro CNAME. La interfaz de usuario de cada registrador es ligeramente diferente, por lo que debe consultar la documentación del proveedor. Sin embargo, estas son algunas directrices generales.

1.	Busque la página de administración de registros DNS. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**. A menudo, puede encontrar el vínculo al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**.
2.	Busque un vínculo que le permita agregar o editar registros DNS. Podría aparecer como **Archivo de zona** o **Registros DNS**, o bien como un vínculo de configuración **Avanzada**.
3.  Cree el registro y guarde los cambios.
    - [Las instrucciones para un registro A se encuentran aquí](#a).
    - [Las instrucciones para un registro CNAME se encuentran aquí](#cname).

<a name="a"></a>
### Creación de un registro A

Para usar un registro A para asignar a la dirección IP de su aplicación de Azure, necesitará crear tanto un registro A como un registro TXT. El registro A se usa para la resolución de DNS, y Azure usa el registro TXT para comprobar que posee el nombre de dominio personalizado.

Configure el registro A de la siguiente forma ("@" normalmente representa el dominio raíz):
 
<table cellspacing="0" border="1">
  <tr>
    <th>Ejemplo de FQDN</th>
    <th>Host A</th>
    <th>Valor A</th>
  </tr>
  <tr>
    <td>contoso.com (raíz)</td>
    <td>@</td>
    <td>Dirección IP del <a href="#vip">Paso 1</a></td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>Dirección IP del <a href="#vip">Paso 1</a></td>
  </tr>
  <tr>
    <td>*.contoso.com (comodín)</td>
    <td>*</td>
    <td>Dirección IP del <a href="#vip">Paso 1</a></td>
  </tr>
</table>

El registro TXT adicional adopta la convención que asigna desde &lt;*subdomain*>.&lt;*rootdomain*> a &lt;*appname*>.azurewebsites.net. Configure el registro TXT de la siguiente forma:

<table cellspacing="0" border="1">
  <tr>
    <th>Ejemplo de FQDN</th>
    <th>Host TXT</th>
    <th>Valor TXT</th>
  </tr>
  <tr>
    <td>contoso.com (raíz)</td>
    <td>@</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (comodín)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
### Creación de un registro CNAME

Si usa un registro CNAME para asignar al nombre de dominio predeterminado de su aplicación de Azure, no es necesario un registro TXT adicional, que sí necesitaría con un registro A.

>[AZURE.IMPORTANT] No cree un registro CNAME para el dominio raíz (es decir, el "registro raíz"). Para más información, consulte [Why can't a CNAME record be used at the root domain](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain) (Por qué no se puede usar un registro CNAME en el dominio raíz). Para asignar un dominio raíz a la aplicación de Azure, utilice en su lugar un [registro A](#a).

Configure el registro CNAME de la siguiente forma ("@" normalmente representa el dominio raíz):

<table cellspacing="0" border="1">
  <tr>
    <th>Ejemplo de FQDN</th>
    <th>Host CNAME</th>
    <th>Valor CNAME</th>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (comodín)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>
## Paso 3: Habilitación del nombre de dominio personalizado para la aplicación

En la hoja **Dominios personalizados** del Portal de Azure (consulte el [paso 1](#vip)), debe agregar a la lista el nombre de dominio completo (FQDN) del dominio personalizado.

1.	Si no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com).

2.	En el Portal de Azure, haga clic en **Servicios de aplicaciones** en el menú izquierdo.

3.	Haga clic en la aplicación y, a continuación haga clic en **Dominios personalizados** > **Agregar nombre de host**.

4.	Agregue el FQDN del dominio personalizado a la lista (por ejemplo, **www.contoso.com**).

    ![Asignación de un nombre de dominio personalizado a una aplicación de Azure: agregar a la lista de nombres de dominio](./media/web-sites-custom-domain-name/add-custom-domain.png)

    >[AZURE.NOTE] Azure tratará de comprobar el nombre de dominio que se utiliza aquí. Asegúrese de que se trata del mismo nombre de dominio para el que creó un registro DNS en el [paso 2](#createdns).

5.  Haga clic en **Validar**.

6.  Al hacer clic en **Validar**, Azure iniciará el flujo de trabajo Verificación de dominio. Este flujo de trabajo comprobará la propiedad del dominio y la disponibilidad del nombre de host, e informará del éxito o del error de forma detallada y con instrucciones para solucionar el error.

7.  Tras una validación correcta, se activará el botón **Agregar nombre de host** y podrá para asignar el nombre del host.

8.  Cuando Azure termine de configurar el nuevo nombre de dominio personalizado, vaya al nombre de dominio personalizado en un explorador. El explorador debería abrir la aplicación de Azure, lo que significa que el nombre de dominio personalizado está configurado correctamente.

> [AZURE.NOTE] Si ya está usando el registro DNS (escenario de dominio activo sirviendo tráfico) y necesita enlazarlo de forma preventiva a la aplicación web para la comprobación de dominio, basta con crear registros TXT como los ejemplos que se muestran en la tabla siguiente. El registro TXT adicional adopta la convención que asigna desde &lt;*subdomain*>.&lt;*rootdomain*> a &lt;*appname*>.azurewebsites.net.
> <table cellspacing="0" border="1">
>   <tr>
>     <th>Ejemplo de FQDN</th>
>     <th>Host TXT</th>
>     <th>Valor TXT</th>
>   </tr>
>   <tr>
>     <td>contoso.com (raíz)</td>
>     <td>awverify.contoso.com</td>
>     <td>&lt;<i>appname</i>>.azurewebsites.net</td>
>   </tr>
>   <tr>
>     <td>www.contoso.com (sub)</td>
>     <td>awverify.www.contoso.com</td>
>     <td>&lt;<i>appname</i>>.azurewebsites.net</td>
>   </tr>
>     <tr>
>     <td>*.contoso.com (sub)</td>
>     <td>awverify.*.contoso.com</td>
>     <td>&lt;<i>appname</i>>.azurewebsites.net</td>
>   </tr>
> </table>
Una vez creado este registro DNS, vuelva al Portal de Azure y agregue el nombre de dominio personalizado a la aplicación web.
 

<a name="verify"></a>
## Comprobación de la propagación de DNS

Después de finalizar los pasos de configuración, es posible que los cambios tarden un tiempo en propagarse, dependiendo del proveedor de DNS. Puede comprobar que la propagación de DNS funciona correctamente mediante el uso de [http://digwebinterface.com/](http://digwebinterface.com/). Después de examinar el sitio, especifique los nombres de host en el cuadro de texto y haga clic en **Profundizar**. Compruebe los resultados para confirmar si los cambios recientes han surtido efecto.

![Asignación de un nombre de dominio personalizado a una aplicación de Azure: verificar la propagación de DNS](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] La propagación de las entradas DNS puede tardar hasta 48 horas (en ocasiones más). Si ha configurado todo correctamente, deberá esperar a que la propagación se complete correctamente.

## Pasos siguientes
Aprenda a proteger su nombre de dominio personalizado con HTTPS [comprando un certificado SSL en Azure](web-sites-purchase-ssl-web-site.md) o [utilizando un certificado SSL desde otro lugar](web-sites-configure-ssl-certificate.md).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

[Creación de una zona DNS mediante PowerShell](../dns/dns-getstarted-create-dnszone.md) [Creación de registros DNS para una aplicación web en un dominio personalizado](../dns/dns-web-sites-custom-domain.md) [Delegación de un dominio en DNS de Azure](../dns/dns-domain-delegation.md)


<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0928_2016-->