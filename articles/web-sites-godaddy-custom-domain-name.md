<properties 
	pageTitle="Configuración de un nombre de dominio de GoDaddy para un sitio web de Azure" 
	description="Aprenda a usar un nombre de dominio de GoDaddy con Sitios web Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth"/>

#Configuración de un nombre de dominio personalizado para un sitio web de Azure (GoDaddy).

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Dominio personalizado</a><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/es-es/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/es-es/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/es-es/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/es-es/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/es-es/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/es-es/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/es-es/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">Sitio web</a> | <a href="/es-es/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Sitio web utilizando el Administrador de tráfico</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido en [Go Daddy](https://godaddy.com) con Sitios web Azure.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

En este artículo:

-   [Descripción de los registros DNS](#understanding-records)
-   [Incorporación de un registro DNS para el dominio personalizado](#bkmk_configurecname)
-   [Habilitación del dominio en su sitio web](#enabledomain)

<h2><a name="understanding-records"></a>Descripción de los registros DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Incorporación de un registro DNS para el dominio personalizado</h2>

Para asociar el domino personalizado a un sitio web de Azure, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por GoDaddy. Utilice los pasos siguientes para localizar las herramientas DNS de GoDaddy.com.

1. Inicie sesión en la cuenta con GoDaddy.com, seleccione **My Account** y, a continuación, **Manage my domains**. Finalmente, seleccione el nombre del dominio que desee usar con el sitio web de Azure en el menú desplegable y elija **Manage DNS**.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. En la página **Domain details**, desplácese hasta la pestaña **DNS Zone File**. Esta es la sección que se utiliza para agregar y modificar los registros DNS para el nombre de dominio. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Seleccione **Add Record** para agregar un registro existente.

	Seleccione el icono de lápiz y papel junto al registro para **editar** un registro existente.

	> [AZURE.NOTE]  Antes de agregar nuevos registros, tenga en cuenta que GoDaddy ya ha creado registros DNS de subdominios populares (llamados **Host** en el editor), como **email****, files****, mail** y otros. Si el nombre que desea utilizar ya existe, modifique el registro actual en lugar de crear uno nuevo.

4. Al agregar un registro, primero debe seleccionar el tipo de registro.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	A continuación, proporcione el **Host** (dominio o subdominio personalizado) y a qué **Points to**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* Al agregar un **registro A (host)**, se debe establecer el campo **Host** en **@** (que representa el nombre de dominio raíz, como **contoso.com**), * (un comodín para que corresponda con varios subdominios) o el subdominio que desee utilizar (por ejemplo, **www**). Debe establecer el campo **Points to** en la dirección IP de su sitio web de Azure.
	
		> [AZURE.NOTE] Si utiliza registros A (host), tiene que agregar también un registro CNAME con una de las configuraciones siguientes:
		> 
		> * Un valor **Host** de **awverify** que **Apunta a** un valor de **awverify.&lt;nombredesusitioweb&gt;.azurewebsites.net**.
		> 
		> Azure utiliza este registro CNAME para comprobar que el dominio descrito por el registro D es efectivamente suyo.

	* Al agregar un **registro CNAME (alias)** debe configurar el campo **Host** con el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Points to** con el nombre de dominio **.azurewebsites.net** de su sitio web de Azure. Por ejemplo, **contoso.azurewebsites.net**.


5. Cuando haya terminado de agregar o modificar los registros, haga clic en **Finish** para guardar los cambios.

<h2><a name="enabledomain"></a>Habilitación del nombre de dominio en su sitio web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]





<!--HONumber=42-->
