<properties linkid="provisioning-biztalk-service" urlDisplayName="Provision BizTalk Services in management portal" pageTitle="Provision BizTalk Services in management portal | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Learn how to provision a BizTalk service in the Azure Management Portal, as well as create an optional SQL database server and Storage account." metaCanonical="http://www.windowsazure.com/es-es/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk Services: Provisioning Using Azure Management Portal" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure
================================================================================

**Sugerencia**

Para iniciar sesión en el Portal de administración de Azure, necesita una suscripción y una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

Un servicio de Azure BizTalk consta de los siguientes componentes:

<table  border="1">
<tr  bgcolor="FAF9F9">
        <td><strong>Requisito</strong>
</td>

        <td><strong>Descripción</strong>
</td>

</tr>

<tr>
<td>Suscripción de Azure</td>

<td><p>La suscripción rige el acceso al Portal de administración de Azure y la crea el titular de la cuenta de Azure en <a href="https://account.windowsazure.com/Subscriptions">Suscripciones de Azure</a>
.</p>

<p>La cuenta de Azure puede tener varias suscripciones y la puede administrar el titular de la cuenta de Azure o distintas personas o grupos. Por ejemplo, el titular de la cuenta de Azure crea una suscripción con el nombre <em>BizTalkServiceSubscription</em>
 y concede acceso a la misma a los administradores de BizTalk dentro de su empresa (por ejemplo, ContosoBTSAdmins@live.com). En este escenario, los administradores de BizTalk inician sesión en el Portal de administración de Azure y tienen derechos de administrador a todos los servicios hospedados en la suscripción, incluidos Servicios de BizTalk de Azure. Los administradores de BizTalk no son los titulares de la cuenta de Azure y, por lo tanto, no tienen acceso a información de facturación alguna.</p>

<a href="http://go.microsoft.com/fwlink/p/?LinkID=267577"> Administrar cuentas, suscripciones y almacenamiento en el Portal de administración de Azure</a>
 proporciona más información sobre las suscripciones y cuentas de Azure.
</td>

</tr>

<tr>
<td>Base de datos SQL de Azure</td>

<td><p>Una Base de datos SQL almacena las tablas, vistas y procedimientos almacenados que utilizan los Servicios de BizTalk de Azure.</p>

<p>Cuando aprovisione un servicio de BizTalk, puede utilizar un SQL Server de Azure o una Base de datos SQL de Azure existente, o bien, crear un nuevo servidor o base de datos. Cuando elige crear una nueva Base de datos SQL o SQL Server de Azure, se habilita automáticamente Servicios de Azure.</p>

<p>Si crea una Base de datos SQL de Azure nueva en un SQL Server de Azure existente, no se modifican las reglas de firewall del servidor. Como resultado, es posible que otros servicios de Azure no puedan tener acceso a las bases de datos del servidor.</p>

No hay requisitos mínimos de escala para la configuración de la Base de datos SQL.</td>

</tr>

<tr>
<td>Espacio de nombres del servicio de control de acceso de Azure</td>

<td>El espacio de nombres del servicio de control de acceso se autentica con Servicios de BizTalk de Azure. Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso. Cuando aprovisiona un servicio de BizTalk, se crea automáticamente el espacio de nombres del servicio de control de acceso.</td>

</tr>


<tr>
<td>Cuenta de almacenamiento de Azure</td>

<td><p>La cuenta de almacenamiento de Azure brinda acceso a tablas, blobs y colas. Cuando aprovisiona un servicio de BizTalk, puede utilizar una cuenta de almacenamiento existente o puede crear automáticamente una nueva. Su servicio de BizTalk utiliza las tablas, blobs y colas para realizar lo siguiente:</p>

<ul>
<li>Se almacenan los archivos de registro que supervisan el servicio de BizTalk. El resultado de la supervisión también aparece en la pestaña Monitoring en el Portal de administración de Azure.</li>

<li>Cuando se crea un contrato X12 o AS2 entre asociados, puede habilitar la característica de archivado para almacenar propiedades de mensaje. Estos datos de seguimiento se guardan en esta cuenta de almacenamiento.</li>

</ul>

</td>

</tr>


<tr>
<td>Certificado privado de SSL</td>

<td><p>Cuando aprovisiona los Servicios de BizTalk de Azure, puede crear una dirección URL que incluya el nombre de su servicio de BizTalk. Este certificado privado SSL (.pfx) se utiliza como el certificado de autenticación de servidor HTTPS cuando se hacen solicitudes a su URL de servicio de BizTalk. Cuando aprovisiona un servicio de BizTalk, se crea automáticamente un certificado autofirmado. </p>

<strong>Información importante sobre el certificado SSL</strong>


<ul>
<li>La fecha de caducidad del certificado debe ser menor a cinco años.</li>

<li>Todos los certificados privados requieren una contraseña. Apréndase esta contraseña y, como procedimiento recomendado, comparta esta contraseña con sus administradores.</li>

<li>Los certificados autofirmados se pueden utilizar en un entorno de prueba o desarrollo. Cuando utilice certificados autofirmados, importe el certificado a su almacén personal de certificados y al almacén de certificados de las entidades de certificación raíz de confianza.</li>

</ul>

<br  />
Cuando envíe la solicitud de certificado de producción a su entidad de certificación, especifique las siguientes propiedades del certificado: <br  />


<ul>
<li><p><strong>Uso mejorado de clave</strong>
: Es posible habilitar los usos de claves adicionales de autenticación de servidor. Como mínimo, Servicios de BizTalk de Azure requiere autenticación de servidor.</p>
</li>

<li><p><strong>Nombre común</strong>
: Escriba el nombre de dominio completo (FQDN) de la URL de sus Servicios de BizTalk de Azure, que se crea cuando aprovisiona el servicio de BizTalk en <a href="#BizTalk">Aprovisionamiento de un servicio de BizTalk</a>
, en este tema.</p>

<p>De ese modo, necesita saber cuál será su URL cuando envíe la solicitud de certificado a su entidad de certificación. Es posible agregar un certificado nuevo o distinto después de aprovisionar el servicio de BizTalk.</p>
</li>

</ul>

<br  />

</td>

</tr>

</table>

En este tema aparecen los pasos para aprovisionar Servicios de BizTalk de Azure, que son los siguientes:

-   [Paso 1: Aprovisionamiento de un servicio de BizTalk](#BizTalk)
-   [Paso 2: Pasos posteriores al aprovisionamiento](#PostProv)
-   [Opcional: Creación del servidor de Base de datos SQL](#SQLDB)
-   [Opcional: Creación de una cuenta de almacenamiento](#Storage)

Paso 1: Aprovisionamiento de un servicio de BizTalk
---------------------------------------------------

El servicio de BizTalk hospeda las aplicaciones del servicio de BizTalk de Azure. Cuando aprovisiona un servicio de BizTalk, se crean automáticamente el espacio de nombres del servicio de control de acceso y el certificado SSL autofirmado. Puede optar por crear una nueva Base de datos SQL de Azure y una nueva cuenta de almacenamiento. Una vez que se aprovisiona el servicio de BizTalk, es posible actualizar algunos de estos requisitos.

Los siguientes pasos aprovisionan un nuevo servicio de Azure BizTalk:

1.  Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).

2.  En la parte inferior del panel de navegación, seleccione **+NEW**:

    ![Seleccione el botón New](./media/biztalk-provision-services/WABS_New.png)

3.  Seleccione **SERVICIOS DE APLICACIONES**, seleccione **BIZTALK SERVICE** y, a continuación, seleccione **CUSTOM CREATE**:

    ![Seleccione Servicio de BizTalk y seleccione Custom Create](./media/biztalk-provision-services/WABS_NewBizTalkService.png)

4.  Escriba la siguiente configuración de Servicio de BizTalk:

    <table  border="1">
     <tr>
     <td><strong>BizTalk Service Name</strong>
    </td>
    
     <td>Escriba un nombre para el servicio de BizTalk. ".biztalk.windows.net" se agregará automáticamente al nombre que escriba. Esto genera una dirección URL que se utiliza para tener acceso a su servicio de BizTalk. Puede escribir cualquier nombre, pero es recomendable que sea específico. Estos son algunos ejemplos:<br  />
    <br  />
     <em>miempresa</em>
    .biztalk.windows.net<br  />
     <em>miempresamiaplicación</em>
    .biztalk.windows.net<br  />
     <em>miaplicación</em>
    .biztalk.windows.net
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Domain URL</strong>
    </td>
    
     <td><strong>Opcional</strong>
    . De manera predeterminada, la dirección URL de dominio es <em>SuNombreDeServicioDeBizTalk</em>
    .biztalk.windows.net. También se puede escribir un dominio personalizado. Por ejemplo, si el dominio es <em>contoso</em>
    , puede escribir: <br  />
    <br  />
     <em>MiEmpresa</em>
    .contoso.com<br  />
     <em>MiEmpresaMiAplicación</em>
    .contoso.com<br  />
     <em>MiAplicación</em>
    .contoso.com<br  />
     <em>SuNombreDeServicioDeBizTalk</em>
    .contoso.com<br  />
    
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Edition</strong>
    </td>
    
     <td>Las opciones incluyen:
     <ul>
     <li>Developer</li>
    
     <li>Standard</li>
    
     <li>Basic</li>
    
     <li>Premium</li>
    
     </ul>
    
     <a href="http://go.microsoft.com/fwlink/p/?LinkID=302279">Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium</a>
     muestra las diferencias entre las ediciones. Si está en la fase de prueba/desarrollo, elija <strong>Developer</strong>
    . Si está en la fase de producción, utilice el gráfico para determinar si la opción correcta para su escenario de negocios es Premium, Standard o Basic.
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Region</strong>
    </td>
    
     <td>Seleccione la región geográfica donde hospedar su servicio de BizTalk.</td>
    
     </tr>
    
     <tr>
     <td><strong>Tracking Database</strong>
    </td>
    
     <td><p>Seleccione su Base de datos SQL para almacenar las tablas que utiliza su servicio de BizTalk. Elija entre las siguientes opciones:</p>
    
     <ul>
     <li><strong>Use an existing SQL Database instance</strong>
    : Seleccione esta opción para utilizar una Base de datos SQL de Azure existente. Es posible utilizar una Base de datos SQL de Azure existente si no la utiliza otro servicio de BizTalk. Necesita el nombre de inicio de sesión y la contraseña que se especificaron cuando se creó ese servidor de Base de datos SQL de Azure.</li>
    
     <li><p><strong>Create a new SQL Database instance</strong>
    : Seleccione esta opción para crear una Base de datos SQL nueva.</p>
    </li>
    
     <p><strong>Nota:</strong>
    </p>
    
     <p>Cuando crea una nueva Base de datos SQL o SQL Server de Azure, Servicios de Azure se habilita automáticamente en la Base de datos SQL. El servicio de BizTalk requiere que los servicios de Azure estén habilitados en la Base de datos SQL de Azure.</p>
    
     <p><strong>Sugerencia</strong>
    </p>
    
     Cree la base de datos de seguimiento y la cuenta de almacenamiento de supervisión/archivado en la misma región que el servicio de BizTalk.
     </ul>
    
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Subscription</strong>
    </td>
    
     <td><strong>Opcional</strong>
    . Solo disponible cuando hay más de una suscripción. Seleccione la suscripción para hospedar su servicio de BizTalk.</td>
    
     </tr>
    
     </table>

    Seleccione la flecha NEXT.

5.  Escriba la configuración de la base de datos:

    <table  border="1">
     <tr>
     <td><strong>Subscription</strong>
    </td>
    
     <td><strong>Opcional</strong>
    . Solo disponible cuando hay más de una suscripción. Seleccione la suscripción para hospedar la Base de datos SQL de Azure.</td>
    
     </tr>
    
     <tr>
     <td><strong>Database</strong>
    </td>
    
     <td><p>Disponible cuando se selecciona <strong>Use an existing SQL Database instance</strong>
     en la pantalla anterior.</p>
    
     Seleccione su Base de datos SQL para almacenar las tablas que utiliza su servicio de BizTalk.
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Name</strong>
    </td>
    
     <td><p>Disponible cuando se selecciona <strong>Create a new SQL Database instance</strong>
     en la pantalla anterior.</p>
    
     Escriba el nombre de Base de datos SQL que utilizará su servicio de BizTalk. De manera predeterminada, se ingresa <em>SuNombreDeServicioDeBizTalk</em>
    _db.</td>
    
     </tr>
    
     <tr>
     <td><strong>Server</strong>
    </td>
    
     <td><p>Disponible cuando se selecciona <strong>Create a new SQL Database instance</strong>
     en la pantalla anterior.</p>
    
     Seleccione un servidor de Base de datos SQL existente. O bien, seleccione <strong>New SQL database server</strong>
     para crear un nuevo servidor de Base de datos SQL.</td>
    
     </tr>
    
     <tr>
     <td><strong>Server Login Name</strong>
    </td>
    
     <td>Escriba el nombre de usuario de inicio de sesión.</td>
    
     </tr>
    
     <tr>
     <td><strong>Server Login Password</strong>
    </td>
    
     <td>Escriba la contraseña de inicio de sesión.</td>
    
     </tr>
    
     <tr>
     <td><strong>Region</strong>
    </td>
    
     <td>Disponible cuando se selecciona <strong>Create a new SQL Database instance</strong>
    . Seleccione la región geográfica donde hospedar su Base de datos SQL.</td>
    
     </tr>
    
     </table>

    Seleccione la flecha NEXT.

6.  Escriba la configuración de supervisión de Azure:

    <table  border="1">
     <tr>
     <td><strong>Monitoring/Archiving Storage Account</strong>
    </td>
    
     <td>Seleccione una cuenta de almacenamiento existente o seleccione <strong>Create a new storage account</strong>
    .</td>
    
     </tr>
    <tr>
     <td><strong>Storage Account Name</strong>
    </td>
    
     <td>Disponible cuando se selecciona <strong>Create a new storage account</strong>
    . Escriba un nombre para la cuenta de almacenamiento que utiliza su servicio de BizTalk.</td>
    
     </tr>
    
     </table>

Seleccione la marca de verificación para completar el asistente. Cuando vez que se complete, aparecerá el icono de progreso:

![El icono de progreso aparece cuando se completa](./media/biztalk-provision-services/WABS_ProgressComplete.png)

Una vez que se completa, un servicio de BizTalk de Azure se aprovisiona y queda listo para las aplicaciones.

La configuración predeterminada es suficiente. Si desea modificar la configuración predeterminada, seleccione **BIZTALK SERVICES** en el panel de navegación que se encuentra a la izquierda y seleccione su servicio de BizTalk. La configuración adicional aparece en las pestañas Panel, Monitor y Scale.

Según el estado del servicio de BizTalk, hay algunas operaciones que no se pueden completar. Para ver una lista de estas operaciones, consulte [Servicios de BizTalk: Gráfico del estado de servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870).

Paso 2: Pasos posteriores al aprovisionamiento
----------------------------------------------

En esta sección aparecen los siguientes pasos:

-   [Incorporación de un certificado privado](#AddCert)
-   [Recuperación del espacio de nombres del servicio de control de acceso](#ACS)

#### Incorporación de un certificado privado

Cuando aprovisiona un servicio de Azure BizTalk, se crea una URL que incluye su nombre de servicio de BizTalk. Un certificado privado SSL (.pfx) se utiliza como el certificado de autenticación de servidor HTTPS cuando se hacen solicitudes a su URL de servicio de BizTalk.

Un certificado autofirmado se crea automáticamente para su servicio de BizTalk. Este certificado se puede descargar o reemplazar en el panel del servicio de BizTalk. Los certificados autofirmados se utilizan en entornos de desarrollo.

Para agregar un certificado listo para producción:

1.  Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  Seleccione **BIZTALK SERVICES** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione su servicio de BizTalk.
3.  Seleccione la pestaña **Panel**.
4.  Seleccione **Update SSL Certificate**:
     ![Modificar certificado SSL](./media/biztalk-provision-services/WABS_QuickGlance.png)

5.  Vaya al certificado SSL privado (*NombreCertificado*.pfx) que incluye su nombre de servicio de BizTalk, escriba la contraseña y seleccione la marca de verificación.

#### Recuperación del espacio de nombres del servicio de control de acceso

El espacio de nombres del servicio de control de acceso se autentica con Servicios de BizTalk de Azure. Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso.

El espacio de nombres del servicio de control de acceso se crea automáticamente para el servicio de BizTalk. Para recuperar el espacio de nombres del servicio de control de acceso, el emisor predeterminado y la clave del emisor, seleccione el botón **Connection Information** en el panel del servicio de BizTalk.

Para recuperar el espacio de nombres del servicio de control de acceso:

1.  Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  Seleccione **BIZTALK SERVICES** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione su servicio de BizTalk.
3.  En la barra de tareas, seleccione **Connection Information**:

	![Seleccione Connection Information](./media/biztalk-provision-services/WABS_ACSConnectInformation.png)

Puede copiar y pegar los valores de control de acceso.

Cuando se crea el espacio de nombres del servicio de control de acceso, cualquier aplicación puede utilizar los valores del control de acceso. Una vez que se aprovisionan los Servicios de BizTalk de Azure, el espacio de nombres del servicio de control de acceso controla la autenticación con su implementación del servicio de BizTalk. Si desea cambiar la suscripción o administrar el espacio de nombres, seleccione **ACTIVE DIRECTORY** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione el espacio de nombres. Sus opciones aparecen en el panel de navegación inferior.

Haga clic en **Manage** para abrir el Portal de administración del sistema de control de acceso. En el Portal de administración del sistema de control de acceso, el servicio de BizTalk utiliza **Service identities**:
 
![Identidades de servicio de ACS en el Portal de administración del sistema de control de acceso](./media/biztalk-provision-services/WABS_ACSServiceIdentities.png)

La identidad de servicio del control de acceso es un conjunto de credenciales que permiten que las aplicaciones o los clientes se autentiquen directamente con el control de acceso.

**Importante**
 El servicio de BizTalk utiliza el valor **Owner** para la identidad de servicio predeterminada y el valor **Password**. Si utiliza el valor de clave simétrica en lugar del valor de contraseña, puede producirse el siguiente error:

*Could not connect to the Access Control Management Service account with the specified credentials*

[Administración del espacio de nombres ACS](http://go.microsoft.com/fwlink/p/?LinkID=285670) muestra algunas directrices y recomendaciones.

Opcional: Creación del servidor de Base de datos SQL
----------------------------------------------------

Cuando aprovisiona Servicios de BizTalk de Azure, se crea automáticamente un nuevo servidor de Base de datos SQL. Si prefiere crear un servidor de Base de datos SQL independiente del servicio de BizTalk, consulte [Uso de Base de datos SQL de Azure en aplicaciones .NET](http://go.microsoft.com/fwlink/p/?LinkID=251285).

Cuando se complete, habrá una Base de datos SQL de Azure nueva en la que puede iniciar sesión y donde podrá crear tablas, vistas y procedimientos almacenados.

De manera predeterminada, la escala de Base de datos SQL se configura con los siguientes elementos:

-   Web Edition
-   Tamaño de base de datos de 1 GB

La configuración predeterminada es suficiente para un servicio de BizTalk. Si desea modificar los ajustes de configuración de la escala, seleccione **SQL DATABASES** en el panel de navegación que se encuentra a la izquierda, seleccione dos veces su Base de datos SQL y, a continuación, seleccione la pestaña **Configure**. La modificación de la escala puede afectar al precio. [Cuentas y facturación en Base de datos SQL de Azure](http://go.microsoft.com/fwlink/p/?LinkID=234930) proporciona información sobre las ediciones y la facturación.

Opcional: Creación de una cuenta de almacenamiento
--------------------------------------------------

Cuando aprovisione un servicio de Azure BizTalk, se creará automáticamente una cuenta de almacenamiento de Azure. Si prefiere crear una cuenta de almacenamiento de Azure independiente del servicio de BizTalk, consulte [Creación de una cuenta de almacenamiento](http://go.microsoft.com/fwlink/p/?LinkID=279823).

Cuando finalice, habrá una cuenta de almacenamiento de Azure nueva que le brinda acceso a tablas, blobs o colas.

La configuración predeterminada es suficiente para un servicio de BizTalk. Si desea modificar la configuración predeterminada, seleccione **STORAGE** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione su cuenta de almacenamiento. La configuración aparece en las pestañas Panel, Monitor, Configure y Containers.

Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. El servicio de BizTalk utiliza automáticamente la clave principal.

La página [Almacenamiento](http://go.microsoft.com/fwlink/p/?LinkID=285671) proporciona información sobre su cuenta de almacenamiento

Pasos siguientes
----------------

Ahora que se aprovisionó un servicio de BizTalk, puede familiarizarse con las distintas pestañas en [Servicios de BizTalk: Pestañas Panel, Monitor y Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281). Su servicio de BizTalk está listo para las aplicaciones. Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

Otras referencias
-----------------

-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servicios de BizTalk: Gráfico del estado de servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

