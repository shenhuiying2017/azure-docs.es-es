<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor, Scale, Configure and Hybrid Connections tabs" pageTitle="Dashboard, Monitor, and Scale in BizTalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale, wabs, mabs" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas

La primera vez que abra el Portal de administración de Azure, entrará automáticamente en la pestaña **ALL ITEMS**. Las columnas de la pestaña **ALL ITEMS** se pueden ordenar. Para ver su servicio de BizTalk, selecciónelo en la pestaña **TODOS LOS ELEMENTOS** o seleccione la pestaña **SERVICIOS DE BIZTALK** y, a continuación, haga clic en el nombre de su servicio de BizTalk.

De este modo se abre una nueva ventana con las pestañas siguientes. El tema describe estas pestañas.

-   ![Inicio rápido][] [Inicio rápido][1]

-   [Panel][]

-   [Monitor][]

-   [Escala][]

-   [Configurar][]

-   [Conexiones híbridas][]

## <a name="QuickStart"></a>Inicio rápido (![Inicio rápido][])

En función de la edición de Servicios de BizTalk, puede que no estén disponibles todas las opciones mostradas.

<table border="1">

<tr>

<td>
<strong>Obtener las herramientas</strong>

</td>
</p>
        <td>Download the BizTalk Services SDK to install the Visual Studio project templates on your on-premises development computer. These templates create the <strong>BizTalk Services</strong> (bridge) and the <strong>BizTalk Service Artifacts</strong> (Transform) Visual Studio projects that are deployed to your BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> How do I Start Using the Azure BizTalk Services SDK </a> and <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installing the Azure BizTalk Services SDK</a> lists the steps to get started.
        </td>
    </tr>

    <tr>
        <td><strong>Create partner agreements</strong></td>

        <td>Opens the Azure BizTalk Services Portal hosted on Azure where you add partners and create X12, AS2, and EDIFACT EDI agreements.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal</a> lists the steps to get started.
        </td>
    </tr>

<tr>
<td>
<strong>Más información acerca de los servicios de BizTalk</strong>

</td>
<td>
Vaya al <a HREF="http://azure.microsoft.com/es-es/documentation/services/biztalk-services/">centro de aprendizaje</a> para obtener más información acerca de los servicios de BizTalk de Azure.

</td>
</tr>
</table>
En la barra de tareas de la parte inferior, puede:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Administrar</strong> su implementación de aplicación</td>
<td align="left">Abre el portal de Servicios de BizTalk de Azure. El portal de servicios de BizTalk es la entrada a la configuración EDI, incluyendo la incorporación de socios y la creación de los acuerdos X12, AS2 y EDIFACT.<br /><br /> Esto es lo mismo que <strong>Crear acuerdos de asociado</strong> en la pestaña <strong>Inicio rápido</strong>.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuración de los componentes para la mensajería EDI en el portal de los servicios de BizTalk</a> proporciona más información acerca del portal de servicios de BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Información de conexión</strong> del espacio de nombres de control de acceso</td>
<td align="left">Al seleccionar Información de conexión, aparecen el espacio de nombres del servicio de control de acceso, el emisor predeterminado y la clave predeterminada. Estos valores se pueden copiar.<br /><br /> Además, puede abrir el Portal de administración del servicio de control de acceso. Este portal del servicio de control de acceso es lo mismo que usar la opción de <strong>Active Directory</strong> en el panel de navegación izquierdo.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administración del espacio de nombres del ACS</a> proporciona más información acerca del Portal de administración del servicio de control de acceso.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Sincronizar claves</strong> de la cuenta de almacenamiento.</td>
<td align="left">Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves de cifrado controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza automáticamente la clave principal. <strong>Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.<br /><br /> Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos:<br /><br />
<ol>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li>En el Portal de administración de Azure, seleccione la cuenta de almacenamiento y Regenerar la clave principal. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li>En el Portal de administración de Azure, seleccione la cuenta de almacenamiento y Regenerar la clave secundaria.</li>
</ol>
<br /> Este proceso se llama &quot;claves de sustitución&quot;. Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Eliminar</strong> su aplicación</td>
<td align="left">Al seleccionar en Eliminar, se elimina su servicio de BizTalk y todos los elementos implementados en él.</td>
</tr>
</tbody>
</table>

## <a name="Dashboard"></a>Panel

En función de la edición de Servicios de BizTalk, puede que no estén disponibles todas las opciones mostradas.

Al seleccionar el nombre de su Servicio de BizTalk, aparece la pestaña Panel. El Panel muestra lo siguiente:

##### Información general del uso: Muestra el número de conexiones híbridas usadas

También muestra el uso de datos en GB.

##### Gráfico de métrica: Muestra una lista fija de métricas de rendimiento

Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. También puede especificar los valores **Relativo** o **Absoluto** y el intervalo de tiempo **Intervalo** de las métricas que se muestran en el gráfico.

Para ver una descripción de estas métricas de rendimiento, vaya a la sección [Métricas disponibles][] de este tema.

##### Vista rápida: Mostrar las propiedades del Servicio de BizTalk

<table border="1">

<tr>
<td><strong>Actualizar credenciales de la base de datos de seguimiento</strong></td>
<td>Cambia el nombre de usuario y la contraseña usados para iniciar sesión en la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Actualizar certificado SSL</strong></td>
<td>Puede modificar el servicio de BizTalk para que use un certificado SSL diferente. Al [crear el servicio de BizTalk][], se crea automáticamente un certificado SSL autofirmado.                                                                                                                                             </td>
</tr>
<tr>
<td><strong>Download Certificate</strong></td>
<td>Puede descargar el certificado SSL usado por su Servicio de BizTalk en una máquina local.                                                                                                                                                                                                                                  </td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Muestra el estado actual del Servicio de BizTalk. Consulte [Servicios de BizTalk: Gráfico del estado de servicio][]. </td>
</tr>
<tr>
<td><strong>URL de servicio</strong></td>
<td>Dirección URL para el Servicio de BizTalk. Esto es lo mismo que la <strong>URL de dominio</strong> especificada al crear el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Dirección IP virtual (VIP) pública</strong></td>
<td>La dirección IP asignada al Servicio de BizTalk. Se usa para todos los extremos de entrada y es la dirección de origen para el tráfico saliente. Esta dirección IP pertenece al servicio de BizTalk mientras esté aprovisionado. Si elimina el servicio de BizTalk, la dirección IP se asigna a otro Servicios de BizTalk.</td>
</tr>
<tr>
<td><strong>ACS Namespace</strong></td>
<td>Se autentica con el servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Muestra la edición especificada al crear el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Location</strong></td>
<td>Muestra la región geográfica donde se hospeda el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Creado</strong></td>
<td>Muestra la fecha y la hora en que se creó el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Base de datos de seguimiento</strong></td>
<td>El nombre de la base de datos SQL de Azure que almacena las tablas de seguimiento usadas por su servicio de BizTalk.                                                                                                                                                                                                       
                                                                   [Requisitos explicados][crear el servicio de BizTalk] proporciona detalles en la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Monitoring/Archiving Storage</strong></td>
<td>La cuenta de almacenamiento de Azure que almacena el resultado de supervisión del servicio de BizTalk.                                                                                                                                                                                                                     
                                                                   [Requisitos explicados][crear el servicio de BizTalk] proporciona detalles en la cuenta de almacenamiento.</td>
</tr>
<tr>
<td><strong>Subscription Name</strong></td>
<td>Muestra la suscripción que hospeda el Servicio de BizTalk. La suscripción rige el acceso al Portal de administración de Azure.                                                                                                                                                                                             </td>
</tr>
<tr>
<td><strong>Subscription ID</strong></td>
<td> Cuando se crea una suscripción, se genera automáticamente un id. de suscripción. Cuando se usan las API de REST, puede que tenga que especificar el Id. de suscripción.</td>
</tr>
</table>

[Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure][crear el servicio de BizTalk] incluye los pasos para crear un servicio de BizTalk.

##### Administrar, Información de conexión, Sincronizar claves y Eliminar en la barra de tareas:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Administrar</strong> su implementación de aplicación</td>
<td align="left">Abre el portal de Servicios de BizTalk de Azure. El portal de servicios de BizTalk es la entrada a la configuración EDI, incluyendo la incorporación de socios y la creación de los acuerdos X12, AS2 y EDIFACT.<br /><br /> Esto es lo mismo que <strong>Crear acuerdos de asociado</strong> en la pestaña <strong>Inicio rápido</strong>.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuración de los componentes para la mensajería EDI en el portal de los servicios de BizTalk</a> proporciona más información acerca del portal de servicios de BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Información de conexión</strong> del espacio de nombres de control de acceso</td>
<td align="left">Muestra los valores del espacio de nombres de control de acceso, el emisor predeterminado y la clave predeterminada, que se pueden copiar.<br /><br /> Además, puede abrir el Portal de administración del servicio de control de acceso. Este portal del servicio de control de acceso es lo mismo que usar la opción de Active Directory en el panel de navegación izquierdo.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administración del espacio de nombres del ACS</a> proporciona más información acerca del Portal de administración del servicio de control de acceso.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Sincronizar claves</strong> de la cuenta de almacenamiento.</td>
<td align="left">Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves de cifrado controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza automáticamente la clave principal. <strong>Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.<br /><br /> Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos:<br /><br />
<ol>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li>En el Portal de administración de Azure, seleccione la cuenta de almacenamiento y Regenerar la clave principal. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li>Seleccione su Servicio de BizTalk y seleccione <strong>Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li>En el Portal de administración de Azure, seleccione la cuenta de almacenamiento y Regenerar la clave secundaria.</li>
</ol>
<br /> Este proceso se llama &quot;claves de sustitución&quot;. Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Eliminar</strong> su aplicación</td>
<td align="left">Se elimina su Servicio de BizTalk y todos los elementos implementados en él.</td>
</tr>
</tbody>
</table>

## <a name="Monitor"></a>Monitor

No se aplica a la versión gratuita.

Al seleccionar el nombre de su Servicio de BizTalk, la pestaña Supervisar se vuelve disponible y muestra lo siguiente:

##### Gráfico de métrica: Muestra las métricas de rendimiento seleccionadas.

Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. Se pueden elegir las métricas de rendimiento que mostrar. Se pueden mostrar seis métricas de rendimiento a la vez como máximo.

También puede especificar los valores **Relativo** o **Absoluto** y el intervalo de tiempo **Intervalo** de las métricas que se muestran.

##### Para eliminar o mostrar métricas en el gráfico:

1.  Seleccione la pestaña **Supervisar**.
2.  Seleccione **Agregar métricas** en la barra de tareas:
    ![Seleccionar métricas adicionales][]
3.  Compruebe las métricas de rendimiento que quiera mostrar.
4.  Seleccione la marca de verificación para volver a la pestaña **Supervisar**.
5.  Seleccione el círculo que se encuentra junto a la métrica para visualizar el valor de la métrica en el gráfico.
    Por ejemplo, la métrica **Uso de CPU** aparecerá atenuada; su resultado no se mostrará en el gráfico:
    ![La métrica Uso de CPU está en gris][]
    Seleccione el círculo gris para habilitar la métrica **Uso de CPU** para que muestre su resultado en el gráfico:
    ![La métrica Uso de CPU está habilitada][]

6.  Para eliminar una métrica del gráfico mostrado y de la lista, seleccione **Eliminar métrica** en la barra de tareas. Para agregar la métrica a lista otra vez, seleccione **Agregar métricas** en la barra de tareas, compruebe la métrica y seleccione la marca de verificación para volver a la pestaña **Supervisar**. Seleccione el círculo gris para habilitar la métrica.

## <a name="Metrics"></a>Métricas disponibles

Están disponibles las siguientes métricas y contadores de rendimiento:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Latencia de recorrido de ida y vuelta</strong></td>
<td align="left">Muestra el tiempo medio en milisegundos (ms) que se tarda en procesar un mensaje desde el momento en que se recibe hasta que el Servicio de BizTalk lo procesa por completo en todos los puentes. Solo se cuentan los mensajes correctamente procesados.<br /><br /> Cuando se producen los eventos siguientes se crea una marca de tiempo:
<ul>
<li>El mensaje entra por la puerta de enlace</li>
<li>El mensaje se envía al destino</li>
<li>Se recibe la respuesta del destino</li>
<li>La respuesta de confirmación de destino se envía a la puerta de enlace</li>
</ul>
<br /> Esta métrica muestra el resultado del cálculo siguiente:<br /><br /> [Respuesta de confirmación de destino enviada a la puerta de enlace] - [El mensaje entra por la puerta de enlace]</td>
</tr>
<tr class="even">
<td align="left"><strong>Errores en origen</strong></td>
<td align="left">Muestra el número total de mensajes que el Servicio de BizTalk no pudo extraer de los extremos de origen.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Uso de CPU</strong></td>
<td align="left">Incluye el porcentaje de tiempo medio del procesador de todas las instancias de rol.</td>
</tr>
<tr class="even">
<td align="left"><strong>Latencia de procesamiento</strong></td>
<td align="left">Muestra el tiempo medio en milisegundos (ms) que tarda el Servicio de BizTalk en procesar un mensaje en todos los puentes, excluyendo el tiempo empleado en los destinos. Solo se cuentan los mensajes correctamente procesados.<br /><br /> Cuando se produce cada uno de los eventos siguientes se crea una marca de tiempo:
<ul>
<li>El mensaje entra por la puerta de enlace</li>
<li>El mensaje se envía al destino</li>
<li>Se recibe la respuesta del destino</li>
<li>La respuesta de confirmación de destino se envía a la puerta de enlace</li>
</ul>
<br />Esta métrica muestra el resultado del cálculo siguiente:<br /><br /> [Respuesta de confirmación de destino enviada a la puerta de enlace] - [Mensaje que entra por la puerta de enlace] - [Se recibe la respuesta del destino] + [El mensaje se envía al destino]</td>
</tr>
<tr class="odd">
<td align="left"><strong>Errores en proceso</strong></td>
<td align="left">Muestra el número total de mensajes con error durante el procesamiento del Servicio BizTalk en todos los puentes dentro de un intervalo de tiempo.</td>
</tr>
<tr class="even">
<td align="left"><strong>Mensajes enviados</strong></td>
<td align="left">Muestra el número total de mensajes enviados por el Servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando un mensaje enviado desde un proceso alcanza el destino de la ruta. Esta métrica no indica el correcto procesamiento del mensaje.<br /><br /> En un escenario de respuesta de solicitud, la métrica se incrementa cuando el destino de la ruta envía una confirmación de recibo al proceso.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Mensajes recibidos</strong></td>
<td align="left">Muestra el número total de mensajes recibidos por el Servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando el proceso recibe un nuevo mensaje.</td>
</tr>
<tr class="even">
<td align="left"><strong>Mensajes en proceso</strong></td>
<td align="left">Muestra el número total de mensajes que el Servicio de BizTalk está procesando actualmente dentro de un intervalo de tiempo.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Mensajes procesados</strong></td>
<td align="left">Muestra el número total de mensajes procesados correctamente por el Servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando el proceso recibe correctamente un mensaje y lo envía al destino.</td>
</tr>
</tbody>
</table>

## <a name="Scale"></a>Escala

En la pestaña Escala puede sumar o restar el número de unidades usadas por su servicio de BizTalk. De forma predeterminada, hay una unidad configurada. Se pueden agregar unidades adicionales para escalar su servicio de BizTalk. Cuando se incrementa la escala, se aumenta el rendimiento. La cantidad de recursos también aumenta, incluyendo los puentes implementados, los acuerdos, las conexiones de LOB y la eficacia de procesamiento. Por ejemplo, si aumenta la escala de 1 unidad a 2. En esta situación, puede implementar el doble de puentes, acuerdos, conexiones de LOB y eficacia de procesamiento.

Algunas ediciones de BizTalk no ofrecen la opción de escala. En esta situación, se admite una unidad. A fin de determinar cuántas unidades puede escalar su edición, consulte [Servicios de BizTalk: Tabla de ediciones][].

El aumento del número de unidades podría afectar al precio. Si aumenta las unidades, al seleccionar **Guardar** aparece un mensaje que le indica si la facturación se ve afectada. A continuación, seleccione Continue. Cuando aumente el número de unidades, el estado del servicio de BizTalk cambia de Activo a Actualizando. En el estado Actualizando, su servicio de BizTalk continúa ejecutándose.

[Servicios de BizTalk: Tabla de ediciones][] define una "Unidad".

## <a name="Configure"></a>Configurar

No se aplica a conexiones híbridas.

Establezca el Estado de copia de seguridad en Ninguno o Automático. Cuando se estableza en Ninguno, no se creará ninguna copia de seguridad automáticamente. Cuando se establece en Automática, configure la ubicación de la copia de seguridad, la frecuencia de la misma y cuánto tiempo quiere conservar los archivos de copia de seguridad.

[Servicios de BizTalk: Copia de seguridad y restauración][] proporciona los detalles.

## <a name="HybridConnections"></a>Conexiones híbridas

Las conexiones híbridas conectan una aplicación de Azure, como sitios web o Servicios móviles, a un recurso local que usa un puerto TCP estático, como SQL Server, MySQL, API web HTTP, Servicio móviles y la mayoría de los servicios web personalizados. Las conexiones híbridas se administran en los Servicios de BizTalk en el Portal de administración de Azure.

Para crear conexiones híbridas en sitios web de Azure, vea [Conexión híbrida: Conexión de un stio web de Azure a un recurso local][].

Para usar conexiones híbridas en Servicios móviles de Azure, vea [Servicios móviles de Azure y conexiones híbridas][].

Para crear o administrar conexiones híbridas en Servicios de BizTalk de Azure, vea [Conexiones híbridas][2].

## Pasos siguientes

Ahora que ya se ha familiarizado con las diferentes pestañas, puede obtener más información acerca de las características de los servicios de BizTalk de Azure:

-   [Servicios de BizTalk: Limitaciones][]
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor][]
-   [Servicios de BizTalk: Copia de seguridad y restauración][]

## Otras referencias

-   [Conexiones híbridas][2]
-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium][Servicios de BizTalk: Tabla de ediciones]
-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure][crear el servicio de BizTalk]
-   [Servicios de BizTalk: Gráfico del estado de servicio de BizTalk][Servicios de BizTalk: Gráfico del estado de servicio]
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?][]

  [Inicio rápido]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
  [1]: #QuickStart
  [Panel]: #Dashboard
  [Monitor]: #Monitor
  [Escala]: #Scale
  [Configurar]: #Configure
  [Conexiones híbridas]: #HybridConnections
  [centro de aprendizaje]: http://azure.microsoft.com/es-es/documentation/services/biztalk-services/
  [Configuración de los componentes para la mensajería EDI en el portal de los servicios de BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=303653
  [Administración del espacio de nombres del ACS]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [Métricas disponibles]: #Metrics
  [crear el servicio de BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Servicios de BizTalk: Gráfico del estado de servicio]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Seleccionar métricas adicionales]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
  [La métrica Uso de CPU está en gris]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
  [La métrica Uso de CPU está habilitada]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
  [Servicios de BizTalk: Tabla de ediciones]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Servicios de BizTalk: Copia de seguridad y restauración]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Conexión híbrida: Conexión de un stio web de Azure a un recurso local]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Servicios móviles de Azure y conexiones híbridas]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [2]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Servicios de BizTalk: Limitaciones]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Servicios de BizTalk: Nombre de emisor y clave de emisor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?]: http://go.microsoft.com/fwlink/p/?LinkID=302335
