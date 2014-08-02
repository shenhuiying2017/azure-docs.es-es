<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

Servicios de BizTalk: Gráfico del estado de servicio
====================================================

En función del estado real del servicio de BizTalk, habrá operaciones que pueda realizar o no en el servicio de BizTalk.

Por ejemplo, supongamos que aprovisiona un servicio de BizTalk nuevo en el Portal de administración de Azure. Cuando finalice correctamente, el servicio de BizTalk se encuentra en estado Active. Puede detener el servicio de BizTalk si está en estado Active. Si se detiene correctamente, el servicio de BizTalk pasa al estado Stopped. Si no se detiene correctamente, el servicio de BizTalk pasa al estado StopFailed. Puede reiniciar el servicio de BizTalk si se encuentra en el estado StopFailed. Si intenta realizar una operación no permitida, como reanudar el servicio de BizTalk, se produce el error siguiente:

**Operation not allowed**

Para aprovisionar un servicio de BizTalk, consulte [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Las tablas siguientes incluyen las operaciones que se pueden realizar cuando el servicio de BizTalk se encuentra en un estado concreto. La marca de verificación indica que la operación se puede realizar mientras se encuentre en el estado en cuestión; y la entrada vacía indica que la operación no se puede realizar en ese estado.

#### Operaciones de inicio, detención, reinicio, suspensión, reanudación y eliminación

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Operaci&oacute;n</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">Estado del servicio de BizTalk</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">Inicio</th>
        <th data-morhtml="true">Detenci&oacute;n</th>
        <th data-morhtml="true">Reinicio</th>
        <th data-morhtml="true">Suspensi&oacute;n</th>
        <th data-morhtml="true">Reanudaci&oacute;n</th>
        <th data-morhtml="true">Eliminaci&oacute;n</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Active</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Disabled</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Suspended</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Stopped</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Service Update Failed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
</table>

#### Operaciones de escala, actualización de la configuración y copia de seguridad

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Operaci&oacute;n</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">Estado del servicio de BizTalk</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">Escala</th>
        <th data-morhtml="true">Actualizaci&oacute;n de la configuraci&oacute;n</th>
        <th data-morhtml="true">Copia de seguridad</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Active</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Disabled</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Suspended</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Stopped</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Service Update Failed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
</table>

Otras referencias
-----------------

-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

