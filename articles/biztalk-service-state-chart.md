<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Servicios de BizTalk: Gráfico del estado de servicio

En función del estado real del servicio de BizTalk, habrá operaciones que pueda realizar o no en el servicio de BizTalk.

Por ejemplo, supongamos que aprovisiona un servicio de BizTalk nuevo en el Portal de administración de Azure. Cuando finalice correctamente, el servicio de BizTalk se encuentra en estado Active. Puede detener el servicio de BizTalk si está en estado Active. Si se detiene correctamente, el servicio de BizTalk pasa al estado Stopped. Si no se detiene correctamente, el servicio de BizTalk pasa al estado StopFailed. Puede reiniciar el servicio de BizTalk si se encuentra en el estado StopFailed. Si intenta realizar una operación no permitida, como reanudar el servicio de BizTalk, se produce el error siguiente:

**Operation not allowed**

Para aprovisionar un servicio de BizTalk, consulte [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure][Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure].

Las tablas siguientes incluyen las operaciones que se pueden realizar cuando el servicio de BizTalk se encuentra en un estado concreto. La marca de verificación indica que la operación se puede realizar mientras se encuentre en el estado en cuestión; y la entrada vacía indica que la operación no se puede realizar en ese estado.

#### Operaciones de inicio, detención, reinicio, suspensión, reanudación y eliminación

<table border="1">
<tr>
<th colspan="15">
Operación

</th>
</tr>
<tr>
<th rowspan="18">
Estado del servicio de BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Inicio

</th>
<th>
Detención

</th>
<th>
Reinicio

</th>
<th>
Suspensión

</th>
<th>
Reanudación

</th>
<th>
Eliminación

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Active</strong>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Disabled</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Suspended</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Stopped</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Service Update Failed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>DisableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>EnableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>StartFailed
 StopFailed
 RestartFailed</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>SuspendedFailed
 ResumeFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>CreatedFailed
 RestoreFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ConfigUpdateFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ScaleFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
</table>

#### Operaciones de escala, actualización de la configuración y copia de seguridad

<table border="1">
<tr>
<th colspan="15">
Operación

</th>
</tr>
<tr>
<th rowspan="18">
Estado del servicio de BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Escala

</th>
<th>
Actualización de la configuración

</th>
<th>
Copia de seguridad

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Active</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Disabled</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Suspended</strong>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Stopped</strong>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Service Update Failed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>DisableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>EnableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>StartFailed
 StopFailed
 RestartFailed</strong>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>SuspendedFailed
 ResumeFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>CreatedFailed
 RestoreFailed
</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ConfigUpdateFailed</strong>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ScaleFailed</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
</tr>
</table>
## Otras referencias

-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure][Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure]
-   [Servicios de BizTalk: Pestañas Panel, Monitor y Escala][Servicios de BizTalk: Pestañas Panel, Monitor y Escala]
-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium][Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium]
-   [Servicios de BizTalk: Copia de seguridad y restauración][Servicios de BizTalk: Copia de seguridad y restauración]
-   [Servicios de BizTalk: Limitaciones][Servicios de BizTalk: Limitaciones]
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor][Servicios de BizTalk: Nombre de emisor y clave de emisor]
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?][¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?]

  [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Servicios de BizTalk: Pestañas Panel, Monitor y Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Servicios de BizTalk: Copia de seguridad y restauración]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Servicios de BizTalk: Limitaciones]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Servicios de BizTalk: Nombre de emisor y clave de emisor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?]: http://go.microsoft.com/fwlink/p/?LinkID=302335
