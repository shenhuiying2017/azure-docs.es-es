<properties linkid="provisioning-biztalk-service" urlDisplayName="Provision BizTalk Services in management portal" pageTitle="Create BizTalk Services in management portal | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Learn how to provision a BizTalk service in the Azure Management Portal, as well as create an optional SQL database server and Storage account." metaCanonical="http://www.windowsazure.com/es-es/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk Services: Provisioning Using Azure Management Portal" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Crear un Servicio de BizTalk mediante el portal de administración de Azure

En este tema se muestran los pasos para crear un Servicio de BizTalk de Azure en el Portal de administración de Azure. Concretamente:

-   [Crear un Servicio de BizTalk][Crear un Servicio de BizTalk]
-   [Pasos posteriores al aprovisionamiento][Pasos posteriores al aprovisionamiento]
-   [Requisitos explicados][Requisitos explicados]
-   [Conexiones híbridas, novedad][Conexiones híbridas, novedad]

<div class="dev-callout"> 
<b>Sugerencia</b> 
<p>Para iniciar sesi&oacute;n en el Portal de administraci&oacute;n de Azure, necesita una suscripci&oacute;n y una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Vea <a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Evaluaci&oacute;n gratuita de Azure</a>.</p> 
</div>

## <a name="BizTalk"></a>Crear un Servicio de BizTalk

En función de la edición que elija, puede que no estén disponibles todos los valores del Servicios de BizTalk.

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  En el panel de navegación inferior, seleccione **NUEVO**:
    ![Seleccione el botón Nuevo][Seleccione el botón Nuevo]

3.  Seleccione **SERVICIOS DE APLICACIONES** \> **SERVICIO DE BIZTALK** \> **CREACIÓN PERSONALIZADA**:
    ![Seleccione Servicio de BizTalk y seleccione Creación personalizada][Seleccione Servicio de BizTalk y seleccione Creación personalizada]

4.  Escriba la configuración del Servicio de BizTalk.

    <table border="1">
	<tr>
	<td><strong>BizTalk Service Name</strong></td>
	<td>Puede escribir cualquier nombre pero sea específico. Estos son algunos ejemplos:                                                                                                                                                                                   
                                 <br/><br/>
	<em>miempresa</em>.biztalk.windows.net                                                                                                                                                                                                                                    
                                <br/>
	<em>miempresamiaplicación</em>.biztalk.windows.net                                                                                                                                                                                                                        
                                 *miaplicación*.biztalk.windows.net <br/><br/>                               ".biztalk.windows.net" se agregará automáticamente al nombre que escriba. Esto crea una dirección URL que se utiliza para tener acceso a su Servicio de BizTalk, como <strong>https://<em>miaplicación</em>.biztalk.windows.net</strong>.
	</td>
	</tr>
	<tr>
	<td><strong>Edition</strong></td>
	<td>Si está en la fase de prueba/desarrollo, elija <strong>Developer</strong>. Si está en la fase de producción, utilice [Servicios de BizTalk: Tabla de ediciones][Servicios de BizTalk: Tabla de ediciones] para determinar si la opción correcta para su escenario de negocios es <strong>Premium</strong>, <strong>Standard</strong> o <strong>Basic</strong>.	 
	</td>
	</tr>
	<tr>
	<td><strong>Region</strong></td>
	<td>Seleccione la región geográfica donde hospedar su servicio de BizTalk. </td>
	</tr>
	<tr>
	<td><strong>Domain URL</strong></td>
	<td><strong>Opcional</strong>. De manera predeterminada, la dirección URL de dominio es <em>SuNombreDeServicioDeBizTalk</em>.biztalk.windows.net. También se puede escribir un dominio personalizado. Por ejemplo, si el dominio es <em>contoso</em>, puede escribir:                             
                                 <br/><br/>
	<em>MiEmpresa</em>.contoso.com                                                                                                                                                                                                                                            
                                 <br/>
	<em>MiEmpresaMiAplicación</em>.contoso.com                                                                                                                                                                                                                                
                                 <br/>
	<em>MiAplicación</em>.contoso.com<br/>
	<em>SuNombreDeServicioDeBizTalk</em>.contoso.com<br/>
	</td>
	</tr>
	</table>
    Seleccione la flecha SIGUIENTE.

5.  
6.   y almacenamiento:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Monitoring/Archiving Storage Account</strong></td>
    <td align="left">Seleccione una cuenta de almacenamiento existente o seleccione Crear una nueva cuenta de almacenamiento.<br /><br /> Si crea una nueva cuenta de almacenamiento, escriba el <strong>Nombre de cuenta de almacenamiento</strong>.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Base de datos de seguimiento</strong></td>
    <td align="left">Si usa una Base de datos SQL de Azure existente, no puede usarla otro Servicio de BizTalk. Necesita el nombre de inicio de sesión y la contraseña especificados cuando se creó ese servidor de Base de datos SQL de Azure.<br /><br />
    <div class="dev-callout"> 
<b>Sugerencia</b> 
<p>Cree la base de datos de seguimiento y la cuenta de almacenamiento de supervisi&oacute;n/archivado en la misma regi&oacute;n que el servicio de BizTalk.</p> 
</div></td>
    </tr>
    </tbody>
    </table>

Seleccione la flecha SIGUIENTE.

6. Escriba la configuración de la base de datos:

    <table border="1">
	<tr>
	<td><strong>Nombre</strong></td>
	<td>Disponible cuando se selecciona <strong>Create a new SQL Database instance</strong> en la pantalla anterior.                                              
    <br/><br/>                              Escriba un nombre de Base de datos SQL que utilizará su Servicio de BizTalk.</td>
	</tr>
	<tr>
	<td><strong>Server</strong></td>
	<td>Disponible cuando se selecciona <strong>Create a new SQL Database instance</strong> en la pantalla anterior.                                              
     <br/><br/>                             Seleccione un servidor de Base de datos SQL o cree un nuevo servidor de Base de datos SQL.</td>
	</tr>
	<tr>
	<td><strong>                                                   Server Login Name</strong></td>
	<td>Escriba el nombre de usuario de inicio de sesión.                                                                                            </td>
	</tr>
	<tr>
	<td><strong>Server Login Password</strong></td>
	<td>Escriba la contraseña de inicio de sesión.                                                                                                   </td>
	</tr>
	<tr>
	<td><strong>Region</strong></td>
	<td>Disponible cuando se selecciona <strong>Create a new SQL Database instance</strong>. Seleccione la región geográfica donde hospedar su Base de datos SQL.</td>
	</tr>
	</table>

Seleccione la marca de verificación para completar el asistente. Aparecerá el icono de progreso:
![El icono de progreso aparece cuando se completa][El icono de progreso aparece cuando se completa]

Una vez que se completa, se crea un Servicio de BizTalk de Azure y queda listo para las aplicaciones. La configuración predeterminada es suficiente. Si desea cambiar la configuración predeterminada, seleccione **SERVICIOS DE BIZTALK** en el panel de navegación que se encuentra a la izquierda y seleccione su Servicio de BizTalk. La configuración adicional aparece en las [pestañas Panel, Monitor y Escala][pestañas Panel, Monitor y Escala] de la parte superior.

Según el estado del servicio de BizTalk, hay algunas operaciones que no se pueden completar. Para ver una lista de estas operaciones, vaya al [gráfico del estado de Servicios de BizTalk][gráfico del estado de Servicios de BizTalk].

## <a name="PostProv"></a>Pasos posteriores al aprovisionamiento

-   [Instalar el certificado en un equipo local][Instalar el certificado en un equipo local]
-   [Agregar un certificado listo para producción][Agregar un certificado listo para producción]
-   [Obtener el espacio de nombres de Access Control][Obtener el espacio de nombres de Access Control]

#### <a name="InstallCert"></a>Instalar el certificado en un equipo local

Como parte del aprovisionamiento del Servicio de BizTalk, se crea un certificado autofirmado y se asocia a su suscripción del Servicios de BizTalk. Debe descargar este certificado e instalarlo en equipos desde los que implemente aplicaciones del Servicio de BizTalk o envíe mensajes a un extremo del Servicio de BizTalk.

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en **SERVICIOS DE BIZTALK** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione su suscripción de BizTalk.
3.  Haga clic en la pestaña **Panel**.
4.  Haga clic en **Descargar certificado SSL**.
    ![Modificar certificado SSL][Modificar certificado SSL]
5.  Haga doble clic en el certificado y ejecute el asistente para instalar el certificado. Asegúrese de instalar el certificado en el almacén **Entidades de certificación raíz de confianza**.

#### <a name="AddCert"></a>Agregar un certificado listo para producción

El certificado autofirmado que se crea automáticamente al aprovisionar Servicios de BizTalk está pensado únicamente para entornos de desarrollo. Para escenarios de producción, debe reemplazarla por un certificado listo para producción.

1.  En la pestaña **Panel**, haga clic en **Actualizar certificado SSL**.
2.  Vaya al certificado SSL privado (*NombreCertificado*.pfx) que incluye su nombre de servicio de BizTalk, escriba la contraseña y seleccione la marca de verificación.

#### <a name="ACS"></a>Obtener el espacio de nombres de Access Control

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Seleccione **BIZTALK SERVICES** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione su servicio de BizTalk.
3.  En la barra de tareas, seleccione **Información de conexión**:
    ![Seleccionar Información de conexión][Seleccionar Información de conexión]

4.  Copie los valores de control de acceso.

Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso. El espacio de nombres del servicio de control de acceso se crea automáticamente para el servicio de BizTalk.

Los valores de control de acceso se pueden utilizar con cualquier aplicación. Una vez que se crean los Servicios de BizTalk de Azure, el espacio de nombres del servicio de control de acceso controla la autenticación con su implementación del servicio de BizTalk. Si desea cambiar la suscripción o administrar el espacio de nombres, seleccione **ACTIVE DIRECTORY** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione el espacio de nombres. La barra de tareas muestra sus opciones.

Haga clic en **Manage** para abrir el Portal de administración del sistema de control de acceso. En el Portal de administración del sistema de control de acceso, el servicio de BizTalk utiliza **Identidades de servicio**:
![Identidades de servicio de ACS en el Portal de administración del sistema de control de acceso][Identidades de servicio de ACS en el Portal de administración del sistema de control de acceso]

La identidad de servicio del control de acceso es un conjunto de credenciales que permiten que las aplicaciones o los clientes se autentiquen directamente con el control de acceso.

**Importante**
 El servicio de BizTalk utiliza el valor **Owner** para la identidad de servicio predeterminada y el valor **Password**. Si utiliza el valor de clave simétrica en lugar del valor de contraseña, puede producirse el siguiente error:

*Could not connect to the Access Control Management Service account with the specified credentials*

[Administración del espacio de nombres ACS][Administración del espacio de nombres ACS] muestra algunas directrices y recomendaciones.

## <a name="Requirements"></a>Requisitos explicados

Estos requisitos no se aplican a la versión gratuita.

<table border="1">

<tr bgcolor="FAF9F9">

<td>
<strong>Lo que necesita</strong>

</td>

<td>
<strong>Por qué lo necesita</strong>

</td>

</tr>

<tr>

<td>
Suscripción de Azure

</td>

<td>
La suscripción determina quién puede iniciar sesión en el Portal de administración de Azure. El titular de la cuenta crea la suscripción en las [Suscripciones a Azure][Suscripciones a Azure].<br/><br/>
La cuenta de Azure puede tener varias suscripciones y la puede administrar cualquier persona a la que se le permita. Por ejemplo, el titular de la cuenta de Azure crea una suscripción con el nombre <em>BizTalkServiceSubscription</em>  y concede acceso a la misma a los administradores de BizTalk dentro de su empresa (por ejemplo, <ContosoBTSAdmins@live.com>). En este escenario, los administradores de BizTalk inician sesión en el Portal de administración de Azure y tienen derechos de administrador a todos los servicios hospedados en la suscripción, incluidos Servicios de BizTalk de Azure. Los administradores de BizTalk no son los titulares de la cuenta de Azure y, por lo tanto, no tienen acceso a información de facturación alguna.<br/><br/>
[Administrar suscripciones y cuentas de almacenamiento en el Portal de administración de Azure][Administrar suscripciones y cuentas de almacenamiento en el Portal de administración de Azure] ofrece más información.

</td>

</tr>

<tr>

<td>
Base de datos SQL de Azure

</td>

<td>
Almacena las tablas, las vistas y los procedimientos almacenados usados por el Servicios de BizTalk, incluidos los datos de seguimiento.<br/><br/>
Cuando cree un servicio de BizTalk, puede usar un Azure SQL Server existente, Base de datos SQL de Azure existente o bien, crear automáticamente un nuevo servidor o base de datos.<br/><br/>
La escala de la base de datos de SQL se configura automáticamente. Normalmente, la escala predeterminada es suficiente para un Servicio de BizTalk. El cambio de la escala puede afectar al precio. Vea [Cuentas y facturación en Base de datos SQL de Azure][Cuentas y facturación en Base de datos SQL de Azure]
<br/><br/
<strong>Notas</strong>

<br/>
<ul>
<li>Cuando crea una nueva Base de datos SQL o SQL Server de Azure, Servicios de Azure se habilita automáticamente. El Servicio de BizTalk requiere que los servicios de Azure estén habilitados.</li>
<li>Si crea una Base de datos SQL de Azure nueva en un SQL Server de Azure existente, no se cambian las reglas de firewall del servidor. Como resultado, es posible que otros servicios de Azure no puedan tener acceso a las bases de datos del servidor.</li>

</td>

</tr>

<tr>

<td>
Espacio de nombres del servicio de control de acceso de Azure

</td>

<td>
Autentica con los Servicios de BizTalk de Azure. Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso. Cuando cree un Servicio de BizTalk, se crea automáticamente el espacio de nombres del servicio de control de acceso.

</td>

</tr>
</p>
<tr>
<td>
Cuenta de almacenamiento de Azure

</td>
<td>
Proporciona acceso a tablas, blobs y colas usados por su servicio de BizTalk para guardar lo siguiente:

<ul>
<li>Archivos de registro que supervisan el Servicio de BizTalk. El resultado de la supervisión también aparece en la pestaña Monitoring en el Portal de administración de Azure.</li>
<li>Cuando se crea un contrato X12 o AS2 entre asociados, puede habilitar la característica de archivado para almacenar propiedades de mensaje. Estos datos se guardan en la cuenta de almacenamiento.
</li>
</ul>
<br/>
 Cuando crea un servicio de BizTalk, puede utilizar una cuenta de almacenamiento existente o puede crear automáticamente una nueva.<br/><br/>
 La configuración de almacenamiento predeterminada es suficiente para un Servicio de BizTalk.<br/><br/>
 Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. El servicio de BizTalk utiliza automáticamente la clave principal.
 Vea [Almacenamiento][Almacenamiento] para obtener más información.

</td>
</tr>
<tr>
<td>
Certificado privado de SSL

</td>
<td>
Cuando se crea un servicio de Azure BizTalk, también se crea una URL HTTPS que incluye su nombre de Servicio de BizTalk. Esta URL se configura automáticamente para usar un certificado solo de desarrollo autofirmado. Para producción, necesita un certificado SSL privado.<br/><br/>
 <strong>Información importante sobre el certificado SSL</strong>

<ul>
<li>La fecha de caducidad del certificado debe ser menor a cinco años.</li>
<li>Todos los certificados privados requieren una contraseña. Apréndase esta contraseña y, como procedimiento recomendado, comparta esta contraseña con sus administradores.</li>
<li> Los certificados autofirmados se utilizan en un entorno de prueba o de desarrollo. Cuando utilice certificados autofirmados, importe el certificado a su almacén personal de certificados y al almacén de certificados de las entidades de certificación raíz de confianza.
</li>
</ul>
<br/>
Cuando envíe la solicitud de certificado de producción a su entidad de certificación, proporcione las siguientes propiedades del certificado:
<br/>

<ul>
<li><strong>Uso mejorado de clave</strong>: Como mínimo, Servicios de BizTalk de Azure requiere autenticación de servidor.</li>
<li><strong>Nombre común</strong>: Escriba el nombre de dominio completo (FQDN) de la URL de su Servicios de BizTalk de Azure. Vea [Crear un Servicio de BizTalk][Crear un Servicio de BizTalk] en este tema.

 Es posible agregar un certificado nuevo o distinto después de crear el servicio de BizTalk.

</td>
</tr>
</table>
## <a name="HC"></a>Conexiones híbridas

Cuando cree un Servicio de BizTalk de Azure, la pestaña **Conexiones híbridas** estará disponible:

![Pestaña Conexiones híbridas][Pestaña Conexiones híbridas]

Las conexiones híbridas se usan para conectar un sitio web de Azure o un servicio móvil de Azure a cualquier recurso local que usa un puerto TCP estático, como SQL Server, MySQL, API web HTTP, Servicios móviles y la mayoría de los servicios web personalizados. Las conexiones híbridas y el servicio de adaptador de BizTalk son diferentes. El Servicio de adaptador de BizTalk se usa para conectar los Servicios de BizTalk de Azure a un sistema de línea de negocio (LOB) local.

Vea [Conexiones híbridas][Conexiones híbridas] para obtener más información, incluida la creación y la administración de conexiones híbridas.

## Pasos siguientes

Ahora que se crea un Servicio de BizTalk, familiarícese con las distintas pestañas en [Servicios de BizTalk: Pestañas Panel, Monitor y Scale][pestañas Panel, Monitor y Escala]. Su servicio de BizTalk está listo para las aplicaciones. Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure][Servicios de BizTalk de Azure].

## Otras referencias

-   [Servicios de BizTalk: Tabla de ediciones][Servicios de BizTalk: Tabla de ediciones]
-   [Servicios de BizTalk: Gráfico del estado][gráfico del estado de Servicios de BizTalk]
-   [Servicios de BizTalk: Copia de seguridad y restauración][Servicios de BizTalk: Copia de seguridad y restauración]
-   [Servicios de BizTalk: Limitaciones][Servicios de BizTalk: Limitaciones]
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor][Servicios de BizTalk: Nombre de emisor y clave de emisor]
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?][¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?]
-   [Conexiones híbridas][Conexiones híbridas]

  [Crear un Servicio de BizTalk]: #BizTalk
  [Pasos posteriores al aprovisionamiento]: #PostProv
  [Requisitos explicados]: #Requirements
  [Conexiones híbridas, novedad]: #HC
  [Evaluación gratuita de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=239738
  [Portal de administración de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Seleccione el botón Nuevo]: ./media/biztalk-provision-services/WABS_New.png
  [Seleccione Servicio de BizTalk y seleccione Creación personalizada]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
  [Servicios de BizTalk: Tabla de ediciones]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [El icono de progreso aparece cuando se completa]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
  [pestañas Panel, Monitor y Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [gráfico del estado de Servicios de BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Instalar el certificado en un equipo local]: #InstallCert
  [Agregar un certificado listo para producción]: #AddCert
  [Obtener el espacio de nombres de Access Control]: #ACS
  [Modificar certificado SSL]: ./media/biztalk-provision-services/WABS_QuickGlance.png
  [Seleccionar Información de conexión]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
  [Identidades de servicio de ACS en el Portal de administración del sistema de control de acceso]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
  [Administración del espacio de nombres ACS]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [Suscripciones a Azure]: https://account.windowsazure.com/Subscriptions
  [Administrar suscripciones y cuentas de almacenamiento en el Portal de administración de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=267577
  [Cuentas y facturación en Base de datos SQL de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=234930
  [Almacenamiento]: http://go.microsoft.com/fwlink/p/?LinkID=285671
  [Pestaña Conexiones híbridas]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
  [Conexiones híbridas]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Servicios de BizTalk de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [Servicios de BizTalk: Copia de seguridad y restauración]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Servicios de BizTalk: Limitaciones]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Servicios de BizTalk: Nombre de emisor y clave de emisor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?]: http://go.microsoft.com/fwlink/p/?LinkID=302335
