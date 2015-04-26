<properties 
	pageTitle="Servicios de BizTalk: Solución de problemas con la utilización de los registros de operaciones | Azure" 
	description="Solucione los problemas de los Servicios de BizTalk con el uso de registros de operaciones. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="nitinme" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>


# Servicios de BizTalk: Solución de problemas con la utilización de los registros de operaciones

Registros de operaciones es una característica de los Servicios de administración, disponible en el Portal de administración de Azure, que permite consultar registros históricos realizados en los servicios de Azure, como el servicio BizTalk. Esta característica le permite consultar datos históricos relacionados con las operaciones de administración en su suscripción a Servicios BizTalk de hasta 180 días de antigüedad.

<div class="dev-callout"><b>Nota:</b>
<p>Esta característica únicamente captura registros para las operaciones de administración en Servicios BizTalk, por ejemplo, cuándo se inició el servicio o cuándo se hizo la copia de seguridad, entre otros. Se realiza el seguimiento de dichas operaciones con independencia de si se llevan a cabo en el Portal de administración de Azure o mediante las <a href="http://msdn.microsoft.com/library/windowsazure/dn232347.aspx">API REST de Servicios BizTalk</a>. Para obtener una lista completa de las operaciones que se siguen mediante los Servicios de administración, consulte <a href="#bizops">Seguimiento de operaciones mediante los Servicios de administración de Azure</a>.</p>
<p>No captura los registros de actividades relacionadas con el tiempo de ejecución de los Servicios BizTalk (como mensajes procesados por puentes, etc.). Para consultar estos registros, debe utilizar la vista de seguimiento desde el portal de Servicios BizTalk. Para obtener más información, consulte <a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">Mensajes de seguimiento</a>.</p>
</div>

##<a name="viewlogs"></a>Consulta de los registros de operaciones de los Servicios BizTalk
1. En el Portal de administración de Azure, haga clic en Servicios de administración y, a continuación, haga clic en la pestaña Registros de operaciones.
2. Puede filtrar los registros en función de varios parámetros, como suscripción, intervalo de fechas, tipo de servicio (por ejemplo, Servicios BizTalk), nombre de servicio o estado (de la operación, por ejemplo, correcto, con errores).
3. Haga clic en la marca de verificación para consultar la lista filtrada. La ilustración siguiente muestra las actividades relacionadas con testbiztalkservice.
	![View operation logs][ViewLogs] 
4. Para obtener más información sobre una operación específica, seleccione la fila y haga clic en <b>Detalles</b> en la parte inferior de la página.


##<a name="bizops"></a>Seguimiento de operaciones mediante los Servicios de administración de Azure
En la tabla siguiente se enumeran las operaciones cuyo seguimiento se realiza utilizando los Servicios de administración de Azure.

<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">Operación para crear un nuevo Servicio BizTalk</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">Operación para eliminar un Servicio BizTalk</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">Operación para reiniciar un Servicio BizTalk</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">Operación para iniciar un Servicio BizTalk</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">Operación para detener un Servicio BizTalk</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">Operación para deshabilitar un Servicio BizTalk</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">Operación para habilitar un Servicio BizTalk</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">Operación para hacer una copia de seguridad de un Servicio BizTalk</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">Operación para restaurar un Servicio BizTalk a partir de una copia de seguridad especificada</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">Operación para suspender un Servicio BizTalk</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">Operación para reanudar un Servicio BizTalk</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">Operación para escalar o reducir verticalmente un Servicio BizTalk</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">Operación para actualizar la configuración de un Servicio BizTalk</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">Operación para actualizar o degradar un Servicio BizTalk a una versión diferente</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">Operación para purgar copias de seguridad del Servicio BizTalk fuera el período de retención</td> 
</tr>
</table>


## Otras referencias
- [Copia de seguridad del servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restauración del servicio de BizTalk a partir de una copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Servicios de BizTalk: Gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

<!--HONumber=46--> 

<!--HONumber=46--> 
