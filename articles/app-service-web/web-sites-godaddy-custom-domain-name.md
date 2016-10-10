<properties
	pageTitle="Configuración de un nombre de dominio personalizado en el Servicio de aplicaciones de Azure (GoDaddy)"
	description="Obtenga información acerca de cómo usar un nombre de dominio de GoDaddy con aplicaciones web de Azure"
	services="app-service"
	documentationCenter=""
	authors="erikre"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>

# Configuración de un nombre de dominio personalizado en el Servicio de aplicaciones de Azure (adquirido directamente de GoDaddy)

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Si ha adquirido el dominio a través de las aplicaciones web del Servicio de aplicaciones de Azure, consulte el paso final de [Comprar dominio para Aplicaciones web](custom-dns-web-site-buydomains-web-app.md).

Este artículo ofrece instrucciones acerca del uso de un nombre de dominio personalizado adquirido directamente en [Go Daddy](https://godaddy.com) con [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
##Descripción de los registros DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>
## Incorporación de un registro DNS para el dominio personalizado

Para asociar el dominio personalizado a una aplicación web del Servicio de aplicaciones, debe agregar una nueva entrada en la tabla DNS para su dominio personalizado usando las herramientas proporcionadas por GoDaddy. Utilice los pasos siguientes para localizar las herramientas DNS de GoDaddy.com.

1. Inicie sesión en su cuenta con GoDaddy.com, seleccione **My Account** (Mi cuenta) y, a continuación, **Manage my domains** (Administrar mis dominios). Finalmente, seleccione el nombre de dominio que desee usar con la aplicación web de Azure en el menú desplegable y seleccione **Manage DNS** (Administrar DNS).

	![Página de dominio personalizado para GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. En la página **Domain details** (Detalles de dominio), desplácese a la pestaña **DNS Zone File** (Archivo de zona DNS). Esta es la sección que se utiliza para agregar y modificar los registros DNS para el nombre de dominio.

	![Pestaña de archivo de zona DNS](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	Seleccione **Add Record** (Agregar registro) para agregar un registro existente.

	Seleccione el icono de lápiz y papel junto al registro para **editar** un registro existente.

	> [AZURE.NOTE] Antes de agregar nuevos registros, tenga en cuenta que GoDaddy ya ha creado registros DNS de subdominios populares (llamados **Host** en el editor), como **email**, **files**, **mail** y otros. Si el nombre que desea utilizar ya existe, modifique el registro actual en lugar de crear uno nuevo.

4. Al agregar un registro, primero debe seleccionar el tipo de registro.

	![seleccione el tipo de registro](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	A continuación, debe proporcionar el **Host** (el subdominio o dominio personalizado) y a qué **apunta**.

	![agregue un registro de zona.](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* Al agregar un **registro A (host)**, se debe establecer el campo **Host** en **@** (esto representa el nombre de dominio raíz, como **contoso.com**), ** (un comodín para que se corresponda con varios subdominios) o el subdominio que desea usar (por ejemplo, **www**). Debe establecer el campo **Points to** (Apunta a) en la dirección IP de aplicación web de Azure.

	* Al agregar un **registro CNAME (alias)**, debe configurar el campo **Host** en el subdominio que desee usar. Por ejemplo, **www**. Debe definir el campo **Points to** en el nombre de dominio **.azurewebsites.net** de su aplicación web de Azure. Por ejemplo, **contoso.azurewebsites.net**.

5. Haga clic en **Agregar otro**.
6. Seleccione **TXT** como el tipo de registro, a continuación, especifique un valor de **Host** de **@** y un valor de **Points to** (Apunta a) de **&lt;yourwebappname&gt;.azurewebsites.net**.

	> [AZURE.NOTE] Azure usa este registro TXT para comprobar que el dominio descrito por el registro A o el primer registro TXT es en efecto suyo. Una vez que el dominio se haya asignado a la aplicación web en el Portal de Azure, se podrá eliminar la entrada del registro TXT.

5. Cuando haya terminado de agregar o modificar los registros, haga clic en **Finish** (Finalizar) para guardar los cambios.

<a name="enabledomain"></a>
## Habilitación del nombre de dominio en su aplicación web

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).

<!---HONumber=AcomDC_0928_2016-->