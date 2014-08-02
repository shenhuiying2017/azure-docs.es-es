<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor and Scale tabs" pageTitle="Dashboard, Monitor, and Scale in Biztalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

Servicios de BizTalk: Pestañas Panel, Monitor y Escala
======================================================

La primera vez que abra el Portal de administración de Azure, entrará automáticamente en la pestaña **ALL ITEMS**. Las columnas de la pestaña **ALL ITEMS** se pueden ordenar. Para ver su servicio de BizTalk, selecciónelo en la pestaña **ALL ITEMS** o haga clic en la pestaña **SERVICIOS DE BIZTALK** y, a continuación, haga clic en el nombre de su servicio de BizTalk.

De este modo se abre una nueva ventana con las opciones siguientes:

-   [Inicio rápido](#QuickStart)

-   [Panel](#Dashboard)

-   [Monitor](#Monitor)

-   [Escala](#Scale)

El tema describe estas pestañas.

Inicio rápido
-------------

En la pestaña Inicio rápido, puede hacer lo siguiente:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opci&oacute;n</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td>
</tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Obtener las herramientas</td>

        <td data-morhtml="true">Descargue el SDK de los servicios de BizTalk para instalar las plantillas del proyecto de Visual Studio en su equipo de desarrollo local. Estas plantillas crean los <strong data-morhtml="true">Servicios de BizTalk</strong> (puente) y los proyecto de Visual Studio de <strong data-morhtml="true">BizTalk Service Artifacts</strong> (transformaci&oacute;n) que se han implementado en su servicio de BizTalk.

        <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302335"> Introducci&oacute;n al uso del SDK de los servicios de BizTalk de Azure </a> e <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=241589">Instalaci&oacute;n del SDK de los servicios de BizTalk de Azure</a> incluyen los pasos para empezar.
        </td>
    </tr>

    <tr data-morhtml="true">
        <td data-morhtml="true">Crear acuerdos de socios comerciales</td>

        <td data-morhtml="true">Abre el Portal de los servicios de BizTalk de Azure hospedados en Azure, donde se agregan socios y se crean los acuerdos EDI X12 y AS2.

        <br data-morhtml="true" /><br data-morhtml="true" />

        <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuraci&oacute;n de los componentes para la mensajer&iacute;a EDI en el portal de los servicios de BizTalk</a> incluye los pasos para empezar.
        </td>
    </tr>

<tr data-morhtml="true">
        <td data-morhtml="true">M&aacute;s informaci&oacute;n acerca de los servicios de BizTalk</td>

        <td data-morhtml="true">Vaya al centro de aprendizaje para obtener m&aacute;s informaci&oacute;n acerca de los servicios de BizTalk de Azure.</td>
</tr>

</table>

En la barra de tareas de la parte inferior, puede **administrar** el servicio de BizTalk, ver la **información de conexión** del espacio de nombres del servicio de control de acceso, **sincronizar las claves** de la cuenta de almacenamiento o **eliminar** el servicio de BizTalk:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opci&oacute;n</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Manage</td>
<td data-morhtml="true">Al hacer clic en Manage, se abre el portal de servicios de BizTalk de Azure. El portal de servicios de BizTalk es la entrada a la configuraci&oacute;n EDI, incluyendo la incorporaci&oacute;n de socios y la creaci&oacute;n de los acuerdos AS2 y X12. <br data-morhtml="true" /><br data-morhtml="true" /> Esto es lo mismo que <strong data-morhtml="true">Crear acuerdos de asociado</strong> en la pesta&ntilde;a <strong data-morhtml="true">Inicio r&aacute;pido</strong>. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuraci&oacute;n de los componentes para la mensajer&iacute;a EDI en el portal de los servicios de BizTalk</a> proporciona m&aacute;s informaci&oacute;n acerca del portal de servicios de BizTalk.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Connection Information</td>
<td data-morhtml="true">Al hacer clic en Connection Information, aparecen el espacio de nombres del servicio de control de acceso, el emisor predeterminado y la clave predeterminada. Estos valores se pueden copiar. <br data-morhtml="true" /><br data-morhtml="true" /> Adem&aacute;s, puede abrir el Portal de administraci&oacute;n del servicio de control de acceso. Este portal del servicio de control de acceso es lo mismo que usar la opci&oacute;n de Active Directory en el panel de navegaci&oacute;n izquierdo. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administraci&oacute;n del espacio de nombres del ACS</a> proporciona m&aacute;s informaci&oacute;n acerca del Portal de administraci&oacute;n del servicio de control de acceso.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Sincronizar claves</td>
<td data-morhtml="true">Cuando crea una cuenta de almacenamiento, se crean autom&aacute;ticamente una clave primaria y una clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza autom&aacute;ticamente la clave principal. <strong data-morhtml="true">Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk. <br data-morhtml="true" /><br data-morhtml="true" /> Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos: <br data-morhtml="true" /><br data-morhtml="true" />
<ol data-morhtml="true">
<li data-morhtml="true">Haga clic en su servicio de BizTalk y en <strong data-morhtml="true">Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li data-morhtml="true">En el Portal de administraci&oacute;n de Azure, haga clic en su cuenta de almacenamiento y en Regenerate the Primary Key. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li data-morhtml="true">Haga clic en su servicio de BizTalk y en <strong data-morhtml="true">Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li data-morhtml="true">En el Portal de administraci&oacute;n de Azure, haga clic en su cuenta de almacenamiento y en Regenerate the Secondary Key.</li>
</ol>
<br data-morhtml="true" /> Este proceso se llama &quot;claves de sustituci&oacute;n&quot;. Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Delete</td>
<td data-morhtml="true">Al hacer clic en Delete, se elimina su servicio de BizTalk y todos los elementos implementados en &eacute;l.</td>
</tr>
</table>

Panel
-----

Al hacer clic en el nombre de su servicio de BizTalk, aparece la pestaña Panel. El Panel muestra la siguiente información:

#### Gráfico de métrica

Una gráfico que muestra una lista fija de métricas de rendimiento. Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. Las métricas incluyen:

-   Uso de CPU
-   Errores en origen
-   Errores en proceso
-   Mensajes procesados
-   Mensajes recibidos
-   Latencia de procesamiento

Para ver una descripción de estas métricas de rendimiento, vaya a la sección [Métricas disponibles](#Metrics) de este tema.

##### Relative o Absolute

El gráfico muestra las tendencias, que indican solo el valor real de cada métrica, es decir, la opción **Relative**. Para mostrar un eje Y con el fin de visualizar los valores absolutos, seleccione **Absolute**.

##### Interval

Modifica el intervalo de tiempo y las métricas se muestran en el gráfico. Las opciones incluyen:

-   1 hora
-   1 día
-   7 días

#### Vista rápida

Incluye las propiedades del servicio de BizTalk, entre las que se incluyen las siguientes:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opci&oacute;n</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Actualizar credenciales de la base de datos de seguimiento</td>
<td data-morhtml="true">Cambia el nombre de usuario y la contrase&ntilde;a usados para iniciar sesi&oacute;n en la base de datos de seguimiento.<br data-morhtml="true" /><br data-morhtml="true" /> Al aprovisionar el servicio de BizTalk, se especifica un nombre de usuario y una contrase&ntilde;a para iniciar sesi&oacute;n en la base de datos de seguimiento. Al usar esta opci&oacute;n, puede modificar su servicio de BizTalk para que use un nombre de usuario y una contrase&ntilde;a diferentes para iniciar sesi&oacute;n en la base de datos de seguimiento.<br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">Servicios de BizTalk: Aprovisionamiento con el Portal de administraci&oacute;n de Azure</a> incluye los pasos para aprovisionar un servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Actualizar certificado SSL</td>
<td data-morhtml="true">Se puede especificar un certificado SSL diferente.<br data-morhtml="true" /><br data-morhtml="true" /> Al aprovisionar un servicio de BizTalk, se crea autom&aacute;ticamente un certificado SSL autofirmado. Si usa esta opci&oacute;n, puede modificar su servicio de BizTalk para que use un certificado SSL diferente.<br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">Servicios de BizTalk: Aprovisionamiento con el Portal de administraci&oacute;n de Azure</a> incluye los pasos para aprovisionar un servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Download Certificate</td>
<td data-morhtml="true">Al usar esta opci&oacute;n, puede descargar el certificado SSL que usa su servicio de BizTalk.<br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">Servicios de BizTalk: Aprovisionamiento con el Portal de administraci&oacute;n de Azure</a> incluye los pasos para aprovisionar un servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Status</td>
<td data-morhtml="true">Muestra el estado actual del servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">URL de servicio</td>
<td data-morhtml="true">Esta URL le lleva al servicio de BizTalk. Esto es lo mismo que la <strong data-morhtml="true">URL de dominio</strong> especificada al aprovisionar el servicio de BizTalk. </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Direcci&oacute;n IP virtual (VIP) p&uacute;blica</td>
<td data-morhtml="true">Esta direcci&oacute;n IP se asigna al servicio de BizTalk. Se usa para todos los extremos de entrada y es la direcci&oacute;n de origen para el tr&aacute;fico saliente. Esta direcci&oacute;n IP pertenece al servicio de BizTalk mientras est&eacute; aprovisionado. Si elimina el servicio de BizTalk, la direcci&oacute;n IP se asigna a otra implementaci&oacute;n de servicio.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ACS Namespace</td>
<td data-morhtml="true">Se autentica con el servicio de BizTalk. Esto es lo mismo que el <strong data-morhtml="true">espacio de nombres ACS</strong> especificado al aprovisionar el servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Edition</td>
<td data-morhtml="true">Incluye la edici&oacute;n. Entre las opciones se incluyen Developer, Basic, Standard y Premium. Esta es la misma edici&oacute;n especificada al aprovisionar el servicio de BizTalk. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302281">Servicios de BizTalk: Gr&aacute;fico de las ediciones Developer, Basic, Standard y Premium</a> muestra las diferencias entre las ediciones.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Ubicaci&oacute;n</td>
<td data-morhtml="true">Muestra la regi&oacute;n geogr&aacute;fica donde se hospeda el servicio de BizTalk. Esto es lo mismo que la <strong data-morhtml="true">regi&oacute;n</strong> especificada al aprovisionar el servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Creado</td>
<td data-morhtml="true">Muestra la fecha y la hora del aprovisionamiento del servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Base de datos de seguimiento</td>
<td data-morhtml="true">El nombre de la base de datos SQL de Azure que almacena las tablas de seguimiento usadas por su servicio de BizTalk. Esto es lo mismo que la <strong data-morhtml="true">base de datos de seguimiento</strong> especificada al aprovisionar el servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Monitoring/Archiving Storage</td>
<td data-morhtml="true">La cuenta de almacenamiento de Azure que almacena el resultado de supervisi&oacute;n del servicio de BizTalk. Esto es lo mismo que la <strong data-morhtml="true">cuenta de almacenamiento de supervisi&oacute;n/archivado</strong> especificada al aprovisionar el servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Subscription Name</td>
<td data-morhtml="true">La suscripci&oacute;n rige el acceso al Portal de administraci&oacute;n de Azure. Esto es lo mismo que el nombre de la <strong data-morhtml="true">suscripci&oacute;n</strong> seleccionado al aprovisionar el servicio de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Subscription ID</td>
<td data-morhtml="true">La suscripci&oacute;n rige el acceso al Portal de administraci&oacute;n de Azure. Cuando se crea una suscripci&oacute;n, se genera autom&aacute;ticamente un id. de suscripci&oacute;n. Cuando se usan las API de REST, puede que tenga que especificar el Id. de suscripci&oacute;n.</td>
</tr>
</table>

[Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) incluye los pasos para aprovisionar un servicio de BizTalk.

#### Manage, Connection Information, Sincronizar claves y Delete

En la barra de tareas de la parte inferior, puede **administrar** el servicio de BizTalk, ver la **información de conexión** del espacio de nombres del servicio de control de acceso, **sincronizar las claves** de la cuenta de almacenamiento o **eliminar** el servicio de BizTalk:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opci&oacute;n</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Manage</td>
<td data-morhtml="true">Al hacer clic en Manage, se abre el portal de servicios de BizTalk de Azure. El portal de servicios de BizTalk es la entrada a la configuraci&oacute;n EDI, incluyendo la incorporaci&oacute;n de socios y la creaci&oacute;n de los acuerdos AS2 y X12. <br data-morhtml="true" /><br data-morhtml="true" /> Esto es lo mismo que <strong data-morhtml="true">Crear acuerdos de asociado</strong> en la pesta&ntilde;a <strong data-morhtml="true">Inicio r&aacute;pido</strong>. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuraci&oacute;n de los componentes para la mensajer&iacute;a EDI en el portal de los servicios de BizTalk</a> proporciona m&aacute;s informaci&oacute;n acerca del portal de servicios de BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Connection Information</td>
<td data-morhtml="true">Al hacer clic en Connection Information, aparecen el espacio de nombres del servicio de control de acceso, el emisor predeterminado y la clave predeterminada. Estos valores se pueden copiar. <br data-morhtml="true" /><br data-morhtml="true" /> Adem&aacute;s, puede abrir el Portal de administraci&oacute;n del servicio de control de acceso. Este portal del servicio de control de acceso es lo mismo que usar la opci&oacute;n de Active Directory en el panel de navegaci&oacute;n izquierdo. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administraci&oacute;n del espacio de nombres del ACS</a> proporciona m&aacute;s informaci&oacute;n acerca del Portal de administraci&oacute;n del servicio de control de acceso.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Sincronizar claves</td>
<td data-morhtml="true">Cuando crea una cuenta de almacenamiento, se crean autom&aacute;ticamente una clave primaria y una clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza autom&aacute;ticamente la clave principal. <strong data-morhtml="true">Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk. <br data-morhtml="true" /><br data-morhtml="true" /> Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos: <br data-morhtml="true" /><br data-morhtml="true" />
<ol data-morhtml="true">
<li data-morhtml="true">Haga clic en su servicio de BizTalk y en <strong data-morhtml="true">Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li data-morhtml="true">En el Portal de administraci&oacute;n de Azure, haga clic en su cuenta de almacenamiento y en Regenerate the Primary Key. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li data-morhtml="true">Haga clic en su servicio de BizTalk y en <strong data-morhtml="true">Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li data-morhtml="true">En el Portal de administraci&oacute;n de Azure, haga clic en su cuenta de almacenamiento y en Regenerate the Secondary Key.</li>
</ol>
<br data-morhtml="true" /> Este proceso se llama &quot;claves de sustituci&oacute;n&quot;. Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Delete</td>
<td data-morhtml="true">Al hacer clic en Delete, se elimina su servicio de BizTalk y todos los elementos implementados en &eacute;l.</td>
</tr>
</table>

Monitor
-------

La pestaña Monitor muestra la siguiente información:

#### Gráfico de métrica

Un gráfico que muestra las métricas de rendimiento seleccionadas. Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. Se pueden elegir las métricas de rendimiento que mostrar. Se pueden mostrar seis métricas de rendimiento a la vez como máximo.

##### Relative o Absolute

El gráfico muestra las tendencias, que indican solo el valor real de cada métrica, es decir, la opción **Relative**. Para mostrar un eje Y con el fin de visualizar los valores absolutos, seleccione **Absolute**.

##### Interval

Modifica el intervalo de tiempo y las métricas se muestran en el gráfico. Las opciones incluyen:

-   1 hora
-   1 día
-   7 días

#### Eliminación de métricas mostradas en el gráfico

> 1.  Haga clic en la pestaña **Monitor**.
> 2.  Haga clic en **Agregar métricas** en la barra de tareas:  
![Haga clic en Agregar métricas](./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png)
> 3.  Compruebe las métricas de rendimiento que quiera mostrar en la pestaña **Monitor**.
> 4.  Haga clic en la marca de verificación para volver a la pestaña **Monitor**.
> 5.  Haga clic en el círculo situado junto a la métrica para mostrar el valor de dicha métrica en el gráfico.<br/>
>      Por ejemplo, la métrica de **Uso de CPU** está en gris; su salida no se muestra en el gráfico:<br/>
>      ![La métrica Uso de CPU está en gris](./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png)
>      <br/>
>      Haga clic en el círculo gris para que se muestre la salida de la métrica **Uso de CPU** en el gráfico:<br/>
>      ![La métrica Uso de CPU está habilitada](./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png)

> 1.  Para eliminar una métrica del gráfico mostrado y de la lista, haga clic en **Eliminar métrica** en la barra de tareas. Al hacer clic en **Eliminar métrica** se eliminan las métricas de la pestaña Monitor. Para agregar la métrica a lista otra vez, haga clic en **Agregar métricas** en la barra de tareas, compruebe la métrica y haga clic en la marca de verificación para volver a la pestaña **Monitor**. Haga clic en el círculo gris para habilitar la métrica en el gráfico.

Métricas disponibles
--------------------

Están disponibles las siguientes métricas y contadores de rendimiento:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
<td data-morhtml="true"><strong data-morhtml="true">M&eacute;trica</strong></td>
<td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Latencia de recorrido de ida y vuelta</td>
<td data-morhtml="true">Expresada en milisegundos (ms), esta m&eacute;trica de rendimiento muestra el tiempo medio que se tarda en procesar un mensaje desde el momento en que se recibe hasta que el servicio de BizTalk lo procesa por completo en todos los puentes. Solo se cuentan los mensajes correctamente procesados.<br data-morhtml="true" /><br data-morhtml="true" /> Cuando se producen los eventos siguientes se crea una marca de tiempo:
<ul data-morhtml="true">
<li data-morhtml="true">El mensaje entra por la puerta de enlace</li>
<li data-morhtml="true">El mensaje se env&iacute;a al destino</li>
<li data-morhtml="true">Se recibe la respuesta del destino</li>
<li data-morhtml="true">La respuesta de confirmaci&oacute;n de destino se env&iacute;a a la puerta de enlace</li>
</ul>
<br data-morhtml="true" /> Esta m&eacute;trica muestra el resultado del c&aacute;lculo siguiente: <br data-morhtml="true" /><br data-morhtml="true" /> [Respuesta de confirmaci&oacute;n de destino enviada a la puerta de enlace] - [El mensaje entra por la puerta de enlace]</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Errores en origen</td>
<td data-morhtml="true">M&eacute;trica de rendimiento que muestra el n&uacute;mero total de mensajes que el servicio de BizTalk no pudo extraer de los extremos de origen.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Uso de CPU</td>
<td data-morhtml="true">Incluye el porcentaje de tiempo medio del procesador de todas las instancias de rol.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Latencia de procesamiento</td>
<td data-morhtml="true">Expresada en milisegundos (ms), esta m&eacute;trica de rendimiento muestra el tiempo medio que tarda el servicio de BizTalk en procesar un mensaje en todos los puentes, excluyendo el tiempo empleado en los destinos. Solo se cuentan los mensajes correctamente procesados.<br data-morhtml="true" /><br data-morhtml="true" /> Cuando se produce cada uno de los eventos siguientes se crea una marca de tiempo:

<ul data-morhtml="true">
<li data-morhtml="true">El mensaje entra por la puerta de enlace</li>
<li data-morhtml="true">El mensaje se env&iacute;a al destino</li>
<li data-morhtml="true">Se recibe la respuesta del destino</li>
<li data-morhtml="true">La respuesta de confirmaci&oacute;n de destino se env&iacute;a a la puerta de enlace</li>
</ul>
<br data-morhtml="true" />Esta m&eacute;trica muestra el resultado del c&aacute;lculo siguiente:<br data-morhtml="true" /><br data-morhtml="true" /> [Respuesta de confirmaci&oacute;n de destino enviada a la puerta de enlace] - [Mensaje que entra por la puerta de enlace] - [Se recibe la respuesta del destino] + [El mensaje se env&iacute;a al destino]</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Errores en proceso</td>
<td data-morhtml="true">M&eacute;trica de rendimiento que muestra el n&uacute;mero total de mensajes con error durante el procesamiento del servicio BizTalk en todos los puentes dentro de un intervalo de tiempo.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Mensajes enviados</td>
<td data-morhtml="true">M&eacute;trica de rendimiento que muestra el n&uacute;mero total de mensajes enviados por el servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta m&eacute;trica se incrementa cuando un mensaje enviado desde un proceso alcanza el destino de la ruta. Esta m&eacute;trica no indica el correcto procesamiento del mensaje.<br data-morhtml="true" /><br data-morhtml="true" /> En un escenario de respuesta de solicitud, la m&eacute;trica se incrementa cuando el destino de la ruta env&iacute;a una confirmaci&oacute;n de recibo al proceso.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Mensajes recibidos</td>
<td data-morhtml="true">M&eacute;trica de rendimiento que muestra el n&uacute;mero total de mensajes recibidos por el servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta m&eacute;trica se incrementa cuando el proceso recibe un nuevo mensaje.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Mensajes en proceso</td>
<td data-morhtml="true">M&eacute;trica de rendimiento que muestra el n&uacute;mero total de mensajes que el servicio de BizTalk est&aacute; procesando actualmente dentro de un intervalo de tiempo.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Mensajes procesados</td>
<td data-morhtml="true">M&eacute;trica de rendimiento que muestra el n&uacute;mero total de mensajes procesados correctamente por el servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta m&eacute;trica se incrementa cuando el proceso recibe correctamente un mensaje y lo env&iacute;a al destino.</td>
</tr>
</table>

Escala
------

En la pestaña Escala puede sumar o restar el número de unidades usadas por su servicio de BizTalk. De forma predeterminada, hay una unidad configurada. Se pueden agregar unidades adicionales para escalar su servicio de BizTalk. Cuando se incrementa la escala, se aumenta el rendimiento. La cantidad de recursos también aumenta, incluyendo los puentes implementados, los acuerdos, las conexiones de LOB y la eficacia de procesamiento. Por ejemplo, si aumenta la escala de 1 unidad a 2. En esta situación, puede implementar el doble de puentes, acuerdos, conexiones de LOB y eficacia de procesamiento.

Algunas ediciones de BizTalk no ofrecen la opción de escala. En esta situación, se admite una unidad. A fin de determinar cuántas unidades puede escalar su edición, consulte [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279).

El aumento del número de unidades podría afectar al precio. Si aumenta las unidades, al hacer clic en **Save** aparece un mensaje que le indica si la factura se ve afectada. A continuación, seleccione Continue. Cuando aumente el número de unidades, el estado del servicio de BizTalk cambia de Activo a Actualizando. En el estado Actualizando, su servicio de BizTalk continúa ejecutándose.

Pasos siguientes
----------------

Ahora que ya se ha familiarizado con las diferentes pestañas, puede obtener más información acerca de las características de los servicios de BizTalk de Azure:

-   [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)

Otras referencias
-----------------

-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servicios de BizTalk: Gráfico del estado de servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

