<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Issuer name and issuer key" pageTitle="Issuer Name and Issuer Key in BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Learn how to retrieve Issuer Name and Issuer Key for either Service Bus or Access Control (ACS) in BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="dwrede" editor="susanjo" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Servicios de BizTalk: Nombre de emisor y clave de emisor

Los servicios de BizTalk de Azure usan el nombre y la clave de emisor del bus de servicio, además del nombre y la clave de emisor del servicio de control de acceso. Concretamente:


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Tarea</strong></td>
<td><strong>Nombre de emisor y clave de emisor</strong></td>
</tr>
<tr>
<td>Implementación de la aplicación desde Visual Studio</td>
<td>Nombre y clave de emisor del servicio de control de acceso</td>
</tr>
<tr>
<td>Configuración del Portal de servicios de BizTalk de Azure</td>
<td>Nombre y clave de emisor del servicio de control de acceso</td>
</tr>
<tr>
<td>Creación de relés de LOB con los servicios de adaptador de BizTalk en Visual Studio</td>
<td>Nombre de emisor y clave de emisor del bus de servicio</td>
</tr>
</table>

Este tema incluye los pasos necesarios para recuperar el nombre y la clave de emisor.

## Nombre y clave de emisor del servicio de control de acceso

Los siguientes elementos usan el nombre y la clave de emisor del servicio de control de acceso:

-   La aplicación de servicio de BizTalk de Azure creada en Visual Studio: para implementar correctamente la aplicación del servicio de BizTalk de Visual Studio en Azure, debe escribir el nombre y la clave de emisor del servicio de control de acceso.
-   Portal de los servicios de BizTalk de Azure: la primera vez que inicia sesión en el Portal de servicios de BizTalk, debe escribir el nombre de su servicio de BizTalk como proveedor de servicios, además de especificar el nombre y la clave de emisor del servicio de control de acceso.

### Recuperación del nombre y la clave de emisor del servicio de control de acceso

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3.  Seleccione su servicio de BizTalk.
4.  Seleccione **Connection Information** en la barra de tareas. El espacio de nombres del servicio de control de acceso, el emisor predeterminado (nombre de emisario) y la clave predeterminada (clave de emisor) se incluyen y se pueden copiar y pegar.<br/><br/>
    Resumiendo:
    Nombre de emisor = Emisor predeterminado
    Clave de emisor = Clave predeterminada

También puede hacer clic en **Abrir Portal de administración de ACS** para recuperar los valores del servicio de control de acceso:

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3.  Seleccione su servicio de BizTalk.
4.  Seleccione el botón Connection Information y **Abrir Portal de administración de ACS**.
5.  En el Portal, debajo de **Service Settings**, haga clic en **Service Identities**. De este modo, se muestra su identidad de servicio, que es el valor del nombre de emisor del servicio de control de acceso. Haga clic en el vínculo de su identidad de servicio para ver la contraseña, que es el valor de la clave de emisor. Sus valores se pueden copiar.<br/><br/>
    Por ejemplo, en **Identidades de servicio**, se ve "propietario". "Owner" es el nombre de emisor de su servicio de control de acceso. Cuando haga clic en el vínculo "owner", verá **Password**. Cuando haga clic en el vínculo "Password", verá el valor. Este valor de Password es su clave de emisor del servicio de control de acceso.
   <br/><br/> Resumiendo:
    Nombre de emisor = Nombre de identidad de servicio
    Clave de emisor = Valor de contraseña

En el panel de navegación izquierdo también puede seleccionar **Active Directory** para recuperar los valores del servicio de control de acceso.

<div class="dev-callout"> 
<b>Importante</b> 
<p>Cuando se crea un espacio de nombres del servicio de control de acceso usando <strong>Active Directory</strong>, <strong>no</strong> se crea autom&aacute;ticamente una identidad de servicio. Cuando aprovisiona un servicio de BizTalk, se crean autom&aacute;ticamente un espacio de nombres del servicio de control de acceso, una identidad de servicio llamada &quot;owner&quot; (nombre de emisor), una contrase&ntilde;a (clave de emisor) y una clave sim&eacute;trica.</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">Direccionamiento del servicio de administraci&oacute;n de ACS para configurar las identidades de servicio</a> proporciona m&aacute;s informaci&oacute;n acerca de las identidades de servicio del control de acceso.</p>
</div>

## Nombre de emisor y clave de emisor del bus de servicio

Los servicios de adaptador de BizTalk usan el nombre y la clave de emisor del bus de servicio. En su proyecto de los servicios de BizTalk en Visual Studio, se usan los servicios de adaptador de BizTalk para conectarse a un sistema local de línea de negocio (LOB). Para conectarse, debe crear el relé de LOB y especificar los detalles de su sistema de LOB. Para ello, debe especificar el nombre y la clave de emisor del bus de servicio.

### Recuperación del nombre y la clave de emisor del bus de servicio

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  En el panel de navegación izquierdo, haga clic en **Bus de servicio**.
3.  Haga clic en su espacio de nombres. En la barra de tareas, haga clic en **Connection Information**. De este modo se muestra el **Emisor predeterminado** (Nombre de emisor) y la **Clave predeterminada** (Clave de emisor). Sus valores se pueden copiar.<br/><br/>
    Resumiento:
    Nombre de emisor = Emisor predeterminado
    Clave de emisor = Clave predeterminada

## Pasos siguientes

Otros temas acerca de los servicios de BizTalk de Azure:

-   [Instalación del SDK de los servicios de BizTalk de Azure][]
-   [Tutoriales: Servicios de BizTalk de Azure][]
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?][]
-   [Servicios de BizTalk de Azure][]

## Otras referencias

-   [Direccionamiento del servicio de administración de ACS para configurar las identidades de servicio][]
-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium][]
-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure][]
-   [Servicios de BizTalk: Gráfico del estado de aprovisionamiento][]
-   [Servicios de BizTalk: Pestañas Panel, Monitor y Escala][]
-   [Servicios de BizTalk: Copia de seguridad y restauración][]
-   [Servicios de BizTalk: Limitaciones][]

  [Portal de administración de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Direccionamiento del servicio de administración de ACS para configurar las identidades de servicio]: http://go.microsoft.com/fwlink/p/?LinkID=303942
  [Instalación del SDK de los servicios de BizTalk de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [Tutoriales: Servicios de BizTalk de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [Servicios de BizTalk de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Servicios de BizTalk: Gráfico del estado de aprovisionamiento]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Servicios de BizTalk: Pestañas Panel, Monitor y Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Servicios de BizTalk: Copia de seguridad y restauración]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Servicios de BizTalk: Limitaciones]: http://go.microsoft.com/fwlink/p/?LinkID=302282
