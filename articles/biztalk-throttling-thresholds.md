<properties linkid="manage-services-biztalk-services-throttling" urlDisplayName="Throttling" pageTitle="Throttling thresholds in BizTalk Services | Azure" metaKeywords="BizTalk Services, throttling, Azure" description="Learn about throttling thresholds and resulting runtime behaviors for BizTalk Services. Throttling is based on memory usage and number of simultaneous messages." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Throttling" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />

Servicios de BizTalk: Limitaciones
==================================

Los servicios de BizTalk de Azure implementan limitaciones del servicio según dos condiciones: uso de memoria y el número de mensajes simultáneos que se procesan. En este tema se enumeran los umbrales de limitación y se describe el comportamiento del tiempo de ejecución cuando se produce una condición de limitación.

Umbrales de limitación
----------------------

La siguiente tabla muestra los orígenes y umbrales de limitación:

<table data-morhtml="true" border="1">

<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <td data-morhtml="true"><strong data-morhtml="true">Descripci&oacute;n</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Umbral bajo</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Umbral alto</strong></td>
</tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Memoria</td>
        <td data-morhtml="true">% de memoria total del sistema disponible/PageFileBytes. <br data-morhtml="true" /><br data-morhtml="true" /> La cantidad total disponible de PageFileBytes es de aproximadamente 2 veces la memoria RAM del sistema.</td>
        <td data-morhtml="true">60%</td>
        <td data-morhtml="true">70%</td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Procesamiento de mensajes</td>
        <td data-morhtml="true">N&uacute;mero de mensajes que se procesan simult&aacute;neamente</td>
        <td data-morhtml="true">40 * n&uacute;mero de n&uacute;cleos</td>
        <td data-morhtml="true">100 * n&uacute;mero de n&uacute;cleos</td>
    </tr>
</table>

Cuando se alcanza un umbral alto, los servicios de BizTalk de Azure empiezan a limitarse. La limitación se detiene cuando se alcanza el umbral bajo. Por ejemplo, el servicio está utilizando un 65 % de la memoria del sistema. En esta situación, el servicio no se limita. El servicio empieza a usar un 70 % de la memoria del sistema. En esta situación, el servicio se limita y sigue limitándose hasta que el servicio utiliza un 60 % de la memoria del sistema (umbral bajo).

Los servicios de BizTalk de Azure realizan un seguimiento del estado de las limitaciones (estado normal frente a estado limitado) y de la duración de la limitación.

Comportamiento del tiempo de ejecución
--------------------------------------

Cuando los servicios de BizTalk de Azure entran en un estado de limitación, ocurre lo siguiente:

-   La limitación es por instancia de rol. Por ejemplo:
     RoleInstanceA se está limitando. RoleInstanceB no se está limitando. En esta situación, los mensajes en RoleInstanceB se procesan según lo esperado. Los mensajes en RoleInstanceA se descartan y producen el siguiente error:
     El servidor está ocupado. Vuelva a intentarlo.
-   Ningún origen de extracción sondea ni descarga un mensaje. Por ejemplo:
     Una canalización extrae los mensajes desde un origen FTP externo. La instancia de rol que realiza la extracción entra en un estado de limitación. En esta situación, la canalización deja de descargar mensajes adicionales hasta que la instancia de rol detiene la limitación.
-   Se envía una respuesta al cliente para que el cliente pueda volver a enviar el mensaje.
-   Debe esperar a que se resuelva la limitación. Específicamente, debe esperar hasta que se alcance el umbral bajo.

Notas importantes
-----------------

-   La limitación no se puede deshabilitar.
-   Los umbrales de limitación no se pueden modificar.
-   La limitación está implementada en todo el sistema.
-   El servidor de Base de datos SQL de Azure también tiene limitaciones integradas.

Otros temas acerca de los servicios de BizTalk de Azure
-------------------------------------------------------

-   [Instalación del SDK de los servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)
-   [Tutoriales: Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)
-   [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

Otras referencias
-----------------

-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servicios de BizTalk: Gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)

