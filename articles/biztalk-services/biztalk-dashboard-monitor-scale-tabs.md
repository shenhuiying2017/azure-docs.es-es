---
title: "Panel, Monitor, Escala, Configurar y Conexiones híbridas en BizTalk Services | Microsoft Docs"
description: "Obtenga información sobre los controles y la supervisión del rendimiento de BizTalk Services"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Revise las pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Después de crear su servicio de  BizTalk e implementar su aplicación, puede cambiar algunas de las configuraciones del servicio de  BizTalk y supervisar el rendimiento de la aplicación. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

De este modo se abre una nueva ventana con las pestañas siguientes. En el tema se describen estas pestañas.

## <a name="quickstart-quickstartquickstart"></a>Guía de inicio rápido![Guía de inicio rápido][Quickstart])
En función de la edición de BizTalk Services, puede que no estén disponibles todas las opciones mostradas. 

<table border="1">
    <tr>
        <td><strong>Obtener las herramientas</strong></td>
        <td>Descargue el SDK de BizTalk Services para instalar las plantillas del proyecto de Visual Studio en su equipo de desarrollo local. Estas plantillas crean los proyectos de Visual Studio <strong>BizTalk Services</strong> (puente) y <strong>Artefactos de BizTalk Services</strong> (transformación), que se implementan en el servicio de BizTalk Services.
        <br/><br/>En 
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Introducción al uso del SDK de Azure BizTalk Services</a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Instalación del SDK de Azure BizTalk Services</a>, se incluyen los pasos para empezar.
        </td>
    </tr>
    <tr>
        <td><strong>Crear acuerdos de asociados comerciales</strong></td>
        <td>Abre el Portal de Azure BizTalk Services hospedados en Azure, donde se agregan socios y se crean los acuerdos X12, AS2 y EDIFACT EDI.
        <br/><br/>En 
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuración de los componentes de mensajería EDI en el portal de BizTalk Services</a>, se incluyen los pasos para empezar.
        </td>
    </tr>

<tr>
        <td><strong>Más información sobre BizTalk Services</strong></td>
        <td>Para más información sobre Azure BizTalk Services, vaya al <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">centro de aprendizaje</a>.</td>
</tr>
</table>


En la barra de tareas de la parte inferior, puede:

<table border="1">

<tr>
<td><strong>Administrar</strong> la implementación de la aplicación</td>
<td>Abre el portal de Azure BizTalk Services. El Portal de BizTalk Services es la entrada a la configuración EDI, incluyendo la incorporación de socios y la creación de los acuerdos X12, AS2 y EDIFACT.
<br/><br/>
La operación es la misma que si selecciona <strong>Crear acuerdos de asociado</strong> en la pestaña <strong>Inicio rápido</strong>.
<br/><br/>En 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuración de los componentes de mensajería EDI en el portal de BizTalk Services</a>, encontrará más información sobre el portal de BizTalk Services.</td>
</tr>

<tr>
<td><strong>Información de conexión</strong> del espacio de nombres de Access Control</td>
<td>Al seleccionar Información de conexión, aparecen el espacio de nombres del servicio de control de acceso, el emisor predeterminado y la clave predeterminada. Estos valores se pueden copiar.
<br/><br/>
Además, puede abrir el Portal de control de acceso. En <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Creación de un espacio de nombres de Access Control</a>, encontrará más información sobre el portal de Access Control.</td>
</tr>

<tr>
<td><strong>Sincronizar claves</strong> en la cuenta de almacenamiento</td>
<td>Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves de cifrado controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza automáticamente la clave principal. <strong>Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.
<br/><br/>
Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos:
<br/><br/>
<ol>
<li>Seleccione el servicio de BizTalk y después <strong>Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li>Seleccione la cuenta de almacenamiento y regenere la clave principal. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li>Seleccione el servicio de BizTalk y después <strong>Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li>Seleccione la cuenta de almacenamiento y regenere la clave secundaria.</li>
</ol>
<br/>
Este proceso se llama "claves de sustitución". Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> la aplicación</td>
<td>Al seleccionar en Eliminar, se elimina su servicio de BizTalk y todos los elementos implementados en él.</td>
</tr>
</table>


## <a name="dashboard"></a>Panel
En función de la edición de BizTalk Services, puede que no estén disponibles todas las opciones mostradas. 

Al seleccionar el nombre de su servicio de BizTalk, aparece la pestaña Panel. En el Panel, existen las siguientes opciones:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Información general del uso: muestra el número de conexiones híbridas usadas
También muestra el uso de datos en GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Gráfico de métrica: muestra una lista fija de métricas de rendimiento
Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. También puede elegir los valores **Relativo** o **Absoluto** y el valor de **Intervalo** de las métricas que aparecen en el gráfico. 

Para ver una descripción de estas métricas de rendimiento, vaya a la sección [Métricas disponibles](#Metrics) de este tema.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Vista rápida: muestra las propiedades del servicio de BizTalk
<table border="1">

<tr>
<td><strong>Actualizar credenciales de la base de datos de seguimiento</strong></td>
<td>Cambia el nombre de usuario y la contraseña usados para iniciar sesión en la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Actualizar certificado SSL</strong></td>
<td>Puede actualizar el servicio de BizTalk para que use un certificado SSL diferente. Al <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">crear el servicio de BizTalk</a>, se genera automáticamente un certificado SSL autofirmado.</td>
</tr>
<tr>
<td><strong>Descarga del certificado</strong></td>
<td>Puede descargar el certificado SSL usado por su Servicio de BizTalk en una máquina local.</td>
</tr>
<tr>
<td><strong>Estado</strong></td>
<td>Muestra el estado actual del Servicio de BizTalk. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: gráfico del estado del servicio de BizTalk</a>. </td>
</tr>
<tr>
<td><strong>URL de servicio</strong></td>
<td>Dirección URL para el Servicio de BizTalk. Es la misma que la dirección <strong>URL de dominio</strong> que se especificó al crear el servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Dirección IP virtual (VIP) pública</strong></td>
<td>La dirección IP asignada al Servicio de BizTalk. Se usa para todos los extremos de entrada y es la dirección de origen para el tráfico saliente. Esta dirección IP pertenece al servicio de BizTalk mientras esté creado. Si elimina el servicio de BizTalk, la dirección IP se asigna a otro Servicios de BizTalk.</td>
</tr>
<tr>
<td><strong>ACS Namespace</strong></td>
<td>Se autentica con el servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Edición</strong></td>
<td>Muestra la edición especificada al crear el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Ubicación</strong></td>
<td>Muestra la región geográfica donde se hospeda el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Created</strong></td>
<td>Muestra la fecha y la hora en que se creó el Servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Tracking Database</strong></td>
<td>El nombre de Azure SQL Database que almacena las tablas de seguimiento usadas por su servicio de BizTalk. 
<br/><br/>En esta 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">explicación de los requisitos</a>, encontrará más información sobre la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Monitoring/Archiving Storage</strong></td>
<td>La cuenta de Azure Storage que almacena el resultado de supervisión del servicio de BizTalk.
<br/><br/>En esta 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">explicación de los requisitos</a>, encontrará más información sobre la cuenta de almacenamiento.</td>
</tr>
<tr>
<td><strong>Subscription Name</strong></td>
<td>Muestra la suscripción que hospeda el Servicio de BizTalk. La suscripción rige el acceso.</td>
</tr>
<tr>
<td><strong>Subscription ID</strong></td>
<td>Cuando se crea una suscripción, se genera automáticamente un identificador de suscripción. Cuando se usan las API REST, puede que tenga que especificar el identificador de suscripción.</td>
</tr>
</table>

[BizTalk Services: aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=302280) incluye los pasos para crear un servicio de BizTalk.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Administrar, Información de conexión, Sincronizar claves y Eliminar en la barra de tareas:
<table border="1">

<tr>
<td><strong>Administrar</strong> la implementación de la aplicación</td>
<td>Abre el portal de Azure BizTalk Services. El Portal de BizTalk Services es la entrada a la configuración EDI, incluyendo la incorporación de socios y la creación de los acuerdos X12, AS2 y EDIFACT.
<br/><br/>
La operación es la misma que si selecciona <strong>Crear acuerdos de asociado</strong> en la pestaña <strong>Inicio rápido</strong>.
<br/><br/>En 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuración de los componentes de mensajería EDI en el portal de BizTalk Services</a>, encontrará más información sobre el portal de BizTalk Services.</td>
</tr>
<tr>
<td><strong>Información de conexión</strong> del espacio de nombres de Access Control</td>
<td>Muestra los valores del espacio de nombres de control de acceso, el emisor predeterminado y la clave predeterminada, que se pueden copiar.
<br/><br/>
Además, puede abrir el Portal de control de acceso. Este portal de control de acceso es lo mismo que usar la opción de Active Directory en el panel de navegación izquierdo.
<br/><br/>En este artículo sobre la 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">administración del espacio de nombres ACS</a>, encontrará más información acerca del portal de Access Control.</td>
</tr>
<tr>
<td><strong>Sincronizar claves</strong> en la cuenta de almacenamiento</td>
<td>Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves de cifrado controlan el acceso a su cuenta de almacenamiento. Su servicio de BizTalk utiliza automáticamente la clave principal. <strong>Sincronizar claves</strong> permite al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.
<br/><br/>
Por ejemplo, en caso de que desee que el servicio de BizTalk use una nueva clave principal para la cuenta de almacenamiento. Para ello, siga estos pasos:
<br/><br/>
<ol>
<li>Seleccione el servicio de BizTalk y después <strong>Sincronizar claves</strong>. Seleccione la clave secundaria. Al hacer esto, el servicio de BizTalk se inicia usando la clave secundaria.</li>
<li>Seleccione la cuenta de almacenamiento y regenere la clave principal. Recuerde que su servicio de BizTalk usa la clave secundaria.</li>
<li>Seleccione el servicio de BizTalk y después <strong>Sincronizar claves</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que ha regenerado.</li>
<li>Seleccione la cuenta de almacenamiento y regenere la clave secundaria.</li>
</ol>
<br/>
Este proceso se llama "claves de sustitución". Su finalidad es permitir al usuario alternar entre la clave principal y la clave secundaria sin interrumpir el servicio de BizTalk.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> la aplicación</td>
<td>Se elimina su Servicio de BizTalk y todos los elementos implementados en él.</td>
</tr>
</table>


## <a name="monitor"></a>Supervisión
No se aplica a la versión gratuita.

Al seleccionar el nombre de su Servicio de BizTalk, la pestaña Supervisar se vuelve disponible y muestra lo siguiente:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Gráfico de métrica: muestra las métricas de rendimiento seleccionadas.
Estas métricas proporcionan valores en tiempo real relacionados con el estado de su servicio de BizTalk. Se pueden elegir las métricas de rendimiento que mostrar. Se pueden mostrar seis métricas de rendimiento a la vez como máximo. 

También puede elegir los valores **Relativo** o **Absoluto** y el valor de **Intervalo** de las métricas que aparecen. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Para eliminar o mostrar métricas en el gráfico:
1. Seleccione la pestaña **Monitor** .
2. Seleccione **Agregar métricas** en la barra de tareas:  
   ![Seleccione Agregar métricas][AddMetrics]
3. Compruebe las métricas de rendimiento que quiera mostrar.
4. Seleccione la marca de verificación para volver a la pestaña **Monitor** .
5. Seleccione el círculo situado junto a la métrica para mostrar el valor de dicha métrica en el gráfico.  
   
    Por ejemplo, la métrica de **Uso de CPU** está atenuada; su resultado no aparece en el gráfico:  
   ![La métrica Uso de CPU está atenuada][GrayedMetric]  
   
    Seleccione el círculo atenuado para habilitar la métrica de **Uso de CPU** y mostrar el resultado en el gráfico:  
   ![La métrica Uso de CPU está habilitada][EnabledMetric]
6. Para eliminar una métrica del gráfico mostrado y de la lista, seleccione **Eliminar métrica** en la barra de tareas. Si desea agregar de nuevo la métrica a la lista, haga clic en **Agregar métricas** en la barra de tareas, active la métrica y seleccione la marca de verificación para volver a la pestaña **Supervisar**. Seleccione el círculo gris para habilitar la métrica.

## <a name="Metrics"></a>Métricas disponibles
Están disponibles las siguientes métricas y contadores de rendimiento:

<table border="1">

<tr>
<td><strong>Latencia de recorrido de ida y vuelta</strong></td>
<td>Muestra el tiempo medio en milisegundos (ms) que se tarda en procesar un mensaje desde el momento en que se recibe hasta que el Servicio de BizTalk lo procesa por completo en todos los puentes. Solo se cuentan los mensajes correctamente procesados.<br/><br/>
Cuando se producen los eventos siguientes se crea una marca de tiempo:
<ul>
<li>El mensaje entra por la puerta de enlace</li>
<li>El mensaje se envía al destino</li>
<li>Se recibe la respuesta del destino</li>
<li>La respuesta de confirmación de destino se envía a la puerta de enlace</li>
</ul>
<br/>
Esta métrica muestra el resultado del cálculo siguiente:
<br/><br/>
[Respuesta de confirmación de destino enviada a la puerta de enlace] - [El mensaje entra por la puerta de enlace]</td>
</tr>
<tr>
<td><strong>Errores en origen</strong></td>
<td>Muestra el número total de mensajes que el Servicio de BizTalk no pudo extraer de los extremos de origen.</td>
</tr>
<tr>
<td><strong>Uso de CPU</strong></td>
<td>Incluye el porcentaje de tiempo medio del procesador de todas las instancias de rol.</td>
</tr>
<tr>
<td><strong>Latencia de procesamiento</strong></td>
<td>Muestra el tiempo medio en milisegundos (ms) que tarda el Servicio de BizTalk en procesar un mensaje en todos los puentes, excluyendo el tiempo empleado en los destinos. Solo se cuentan los mensajes correctamente procesados.<br/><br/>
Cuando se produce cada uno de los eventos siguientes se crea una marca de tiempo:

<ul>
<li>El mensaje entra por la puerta de enlace</li>
<li>El mensaje se envía al destino</li>
<li>Se recibe la respuesta del destino</li>
<li>La respuesta de confirmación de destino se envía a la puerta de enlace</li>
</ul>
<br/>Esta métrica muestra el resultado del cálculo siguiente:<br/><br/>
[Respuesta de confirmación de destino enviada a la puerta de enlace] - [Mensaje que entra por la puerta de enlace] - [Se recibe la respuesta del destino] + [El mensaje se envía al destino]</td>
</tr>
<tr>
<td><strong>Errores en proceso</strong></td>
<td>Muestra el número total de mensajes con error durante el procesamiento del Servicio BizTalk en todos los puentes dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes enviados</strong></td>
<td>Muestra el número total de mensajes enviados por el Servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando un mensaje enviado desde un proceso alcanza el destino de la ruta. Esta métrica no indica el correcto procesamiento del mensaje.<br/><br/>
En un escenario de respuesta de solicitud, la métrica se incrementa cuando el destino de la ruta envía una confirmación de recibo al proceso.</td>
</tr>
<tr>
<td><strong>Mensajes recibidos</strong></td>
<td>Muestra el número total de mensajes recibidos por el Servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando el proceso recibe un nuevo mensaje.</td>
</tr>
<tr>
<td><strong>Mensajes en proceso</strong></td>
<td>Muestra el número total de mensajes que el Servicio de BizTalk está procesando actualmente dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes procesados</strong></td>
<td>Muestra el número total de mensajes procesados correctamente por el Servicio de BizTalk en todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando el proceso recibe correctamente un mensaje y lo envía al destino.</td>
</tr>
</table>


## <a name="scale"></a>Escala
En la pestaña Escala puede sumar o restar el número de unidades usadas por su servicio de BizTalk. De forma predeterminada, hay una unidad configurada. Se pueden agregar unidades adicionales para escalar su servicio de BizTalk. Cuando se incrementa la escala, se aumenta el rendimiento. La cantidad de recursos también aumenta, incluyendo los puentes implementados, los acuerdos, las conexiones de LOB y la eficacia de procesamiento. Por ejemplo, si aumenta la escala de 1 unidad a 2. En esta situación, puede implementar el doble de puentes, acuerdos, conexiones de LOB y eficacia de procesamiento.

Algunas ediciones de BizTalk no ofrecen la opción de escala. En esta situación, se admite una unidad. A fin de determinar cuántas unidades puede escalar su edición, consulte [BizTalk Services: Gráfico de ediciones](biztalk-editions-feature-chart.md).

El aumento del número de unidades podría afectar al precio. Si aumenta las unidades, al seleccionar **Guardar** aparece un mensaje que le indica si la facturación se ve afectada. A continuación, seleccione Continue. Cuando aumente el número de unidades, el estado del servicio de BizTalk cambia de Activo a Actualizando. En el estado Actualizando, su servicio de BizTalk continúa ejecutándose.

[BizTalk Services: gráfico de ediciones](biztalk-editions-feature-chart.md) define una unidad.

## <a name="configure"></a>Configuración
No se aplica a conexiones híbridas.

Establezca Estado de copia de seguridad en Ninguno o Automático. Cuando se establezca en Ninguno, no se creará ninguna copia de seguridad automáticamente. Cuando se establece en Automático, configure la ubicación de la copia de seguridad, la frecuencia de la misma y cuánto tiempo quiere conservar los archivos de copia de seguridad. 

[BizTalk Services: copias de seguridad y restauración](biztalk-backup-restore.md) proporciona los detalles. 

## <a name="HybridConnections"></a>Conexiones híbridas
Las conexiones híbridas conectan una aplicación de Azure, como Web Apps o Mobile Apps de Azure App Service, a un recurso local que usa un puerto TCP estático (por ejemplo, SQL Server, MySQL, HTTP Web API y la mayoría de los servicios web personalizados). Las conexiones híbridas se administran en BizTalk Services.

Para crear o administrar conexiones híbridas en Azure BizTalk Services, consulte [Conexiones híbridas](integration-hybrid-connection-overview.md).

## <a name="next"></a>Pasos siguientes
Ahora que ya se ha familiarizado con las diferentes pestañas, puede obtener más información acerca de las características de Azure BizTalk Services:

* [BizTalk Services: limitaciones](biztalk-throttling-thresholds.md)  
* [BizTalk Services: nombre del emisor y clave del emisor](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: copias de seguridad y restauración](biztalk-backup-restore.md)

## <a name="see-also"></a>Otras referencias
* [Conexiones híbridas](integration-hybrid-connection-overview.md)  
* [BizTalk Services: gráfico de las ediciones Developer, Basic, Standard y Premium](biztalk-editions-feature-chart.md)  
* [BizTalk Services: aprovisionamiento](biztalk-provision-services.md)  
* [BizTalk Services: gráfico de estado del servicio de BizTalk](biztalk-service-state-chart.md)  
* [¿Cómo puedo comenzar a utilizar el SDK de Azure BizTalk Services?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

