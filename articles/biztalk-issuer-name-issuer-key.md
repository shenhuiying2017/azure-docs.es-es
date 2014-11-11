<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Issuer name and issuer key" pageTitle="Issuer Name and Issuer Key in BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Learn how to retrieve Issuer Name and Issuer Key for either Service Bus or Access Control (ACS) in BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />

Servicios de BizTalk: Nombre de emisor y clave de emisor
========================================================

Los servicios de BizTalk de Azure usan el nombre y la clave de emisor del bus de servicio, además del nombre y la clave de emisor del servicio de control de acceso. Concretamente:


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Tarea</strong></td>
<td><strong>Nombre de emisor y clave de emisor</strong></td>
</tr>
<tr>
<td>Implementaci&oacute;n de la aplicaci&oacute;n desde Visual Studio</td>
<td>Nombre y clave de emisor del servicio de control de acceso</td>
</tr>
<tr>
<td>Configuraci&oacute;n del Portal de servicios de BizTalk de Azure</td>
<td>Nombre y clave de emisor del servicio de control de acceso</td>
</tr>
<tr>
<td>Creaci&oacute;n de rel&eacute;s de LOB con los servicios de adaptador de BizTalk en Visual Studio</td>
<td>Nombre de emisor y clave de emisor del bus de servicio</td>
</tr>
</table>

Este tema incluye los pasos necesarios para recuperar el nombre y la clave de emisor.

Nombre y clave de emisor del servicio de control de acceso
----------------------------------------------------------

Los siguientes elementos usan el nombre y la clave de emisor del servicio de control de acceso:

-   La aplicación de servicio de BizTalk de Azure creada en Visual Studio: para implementar correctamente la aplicación del servicio de BizTalk de Visual Studio en Azure, debe escribir el nombre y la clave de emisor del servicio de control de acceso.
-   Portal de los servicios de BizTalk de Azure: la primera vez que inicia sesión en el Portal de servicios de BizTalk, debe escribir el nombre de su servicio de BizTalk como proveedor de servicios, además de especificar el nombre y la clave de emisor del servicio de control de acceso.

### Recuperación del nombre y la clave de emisor del servicio de control de acceso

1.  Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3.  Seleccione su servicio de BizTalk.
4.  Seleccione **Connection Information** en la barra de tareas. El espacio de nombres del servicio de control de acceso, el emisor predeterminado (nombre de emisario) y la clave predeterminada (clave de emisor) se incluyen y se pueden copiar y pegar.

	Resumiendo:
	Nombre de emisor = Emisor predeterminado
	Clave de emisor = Clave predeterminada

También puede hacer clic en **Abrir Portal de administración de ACS** para recuperar los valores del servicio de control de acceso:

1.  Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3.  Seleccione su servicio de BizTalk.
4.  Seleccione el botón Connection Information y **Abrir Portal de administración de ACS**.
5.  En el Portal, debajo de **Service Settings**, haga clic en **Service Identities**. De este modo, se muestra su identidad de servicio, que es el valor del nombre de emisor del servicio de control de acceso. Haga clic en el vínculo de su identidad de servicio para ver la contraseña, que es el valor de la clave de emisor. Sus valores se pueden copiar.<br/>
	Por ejemplo, en **Service Identities**, podrá ver "owner". "Owner" es el nombre de emisor de su servicio de control de acceso. Cuando haga clic en el vínculo "owner", verá **Password**. Cuando haga clic en el vínculo "Password", verá el valor. Este valor de Password es su clave de emisor del servicio de control de acceso.

	Resumiendo:
	Nombre de emisor = Nombre de identidad de servicio
	Clave de emisor = Valor de contraseña

En el panel de navegación izquierdo también puede seleccionar **Active Directory** para recuperar los valores del servicio de control de acceso.

**Importante**

Cuando se crea un espacio de nombres del servicio de control de acceso usando **Active Directory**, **no** se crea automáticamente una identidad de servicio. Cuando aprovisiona un servicio de BizTalk, se crean automáticamente un espacio de nombres del servicio de control de acceso, una identidad de servicio llamada "owner" (nombre de emisor), una contraseña (clave de emisor) y una clave simétrica.

[Uso del servicio de administración de ACS para configurar las identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942) proporciona más información acerca de las identidades de servicio del control de acceso.

Nombre de emisor y clave de emisor del bus de servicio
------------------------------------------------------

Los servicios de adaptador de BizTalk usan el nombre y la clave de emisor del bus de servicio. En su proyecto de los servicios de BizTalk en Visual Studio, se usan los servicios de adaptador de BizTalk para conectarse a un sistema local de línea de negocio (LOB). Para conectarse, debe crear el relé de LOB y especificar los detalles de su sistema de LOB. Para ello, debe especificar el nombre y la clave de emisor del bus de servicio.

### Recuperación del nombre y la clave de emisor del bus de servicio

1.  Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  En el panel de navegación izquierdo, haga clic en **Bus de servicio**.
3.  Haga clic en su espacio de nombres. En la barra de tareas, haga clic en **Connection Information**. De este modo se muestra el **Emisor predeterminado** (Nombre de emisor) y la **Clave predeterminada** (Clave de emisor). Sus valores se pueden copiar.

	Resumiendo:
	Nombre de emisor = Emisor predeterminado
	Clave de emisor = Clave predeterminada

Pasos siguientes
----------------

Otros temas acerca de los servicios de BizTalk de Azure:

-   [Instalación del SDK de los servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)
-   [Tutoriales: Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)
-   [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

Otras referencias
-----------------

-   [Uso del servicio de administración de ACS para configurar las identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942)
-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servicios de BizTalk: Gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)

