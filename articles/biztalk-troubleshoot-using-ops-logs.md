<properties linkid="biztalk-troubleshoot-using-ops-logs" urlDisplayName="BizTalk Services: Troubleshoot using operation logs" pageTitle="BizTalk Services: Troubleshoot using ops logs | Azure" metaKeywords="" description="BizTalk Services: Troubleshoot using ops logs" metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Troubleshoot using ops logs" authors="" solutions="" writer="nitinme" manager="paulettm" editor="cgronlun" />

Servicios de BizTalk: Solución de problemas con la utilización de los registros de operaciones
==============================================================================================

Registros de operaciones es una característica de los Servicios de administración, disponible en el Portal de administración de Azure, que permite consultar registros históricos realizados en los servicios de Azure, como el servicio BizTalk. Esta característica le permite consultar datos históricos relacionados con las operaciones de administración en su suscripción a Servicios BizTalk de hasta 180 días de antigüedad.

**Nota:**

Esta característica únicamente captura registros para las operaciones de administración en los Servicios BizTalk, por ejemplo, cuándo se inició el servicio o cuándo se hizo la copia de seguridad, entre otros. Se realiza el seguimiento de dichas operaciones con independencia de si se llevan a cabo en el Portal de administración de Azure o mediante las [APIS de REST de los Servicios BizTalk](http://msdn.microsoft.com/en-us/library/windowsazure/dn232347.aspx). Para obtener una lista completa de las operaciones que se siguen mediante los Servicios de administración, consulte [Seguimiento de operaciones mediante los Servicios de administración de Azure](#bizops).

No captura los registros de actividades relacionadas con el tiempo de ejecución de los Servicios BizTalk (como mensajes procesados por puentes, etc.). Para consultar estos registros, debe utilizar la vista de seguimiento desde el portal de Servicios BizTalk. Para obtener más información, consulte [Mensajes de seguimiento](http://msdn.microsoft.com/library/windowsazure/hh949805.aspx).

Consulta de los registros de operaciones de los Servicios BizTalk
-----------------------------------------------------------------

1.  En el Portal de administración de Azure, haga clic en Servicios de administración y, a continuación, haga clic en la pestaña Registros de operaciones.
2.  Puede filtrar los registros en función de varios parámetros, como suscripción, intervalo de fechas, tipo de servicio (por ejemplo, Servicios BizTalk), nombre de servicio o estado (de la operación, por ejemplo, correcto, con errores).
3.  Haga clic en la marca de verificación para consultar la lista filtrada. La ilustración siguiente muestra las actividades relacionadas con testbiztalkservice. 

  ![Consulta de registros de operaciones](./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png)
4.  Para obtener más información sobre una operación específica, seleccione la fila y haga clic en **Detalles** en la parte inferior de la página.

Seguimiento de operaciones mediante los Servicios de administración de Azure
----------------------------------------------------------------------------

En la tabla siguiente se enumeran las operaciones cuyo seguimiento se realiza utilizando los Servicios de administración de Azure.

<table data-morhtml="true" border="1" cellpadding="5">
<tr data-morhtml="true">
<td data-morhtml="true">CreateBizTalkService</td> 
<td data-morhtml="true" align="left">Operation to create a new BizTalk Service</td> 
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">DeleteBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para eliminar un Servicio BizTalk</td>  
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">RestartBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para reiniciar un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">StartBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para iniciar un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">StopBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para detener un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">DisableBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para deshabilitar un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">EnableBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para habilitar un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">BackupBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para hacer una copia de seguridad de un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">RestoreBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para restaurar un Servicio BizTalk a partir de una copia de seguridad especificada</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">SuspendBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para suspender un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ResumeBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para reanudar un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ScaleBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para escalar o reducir verticalmente un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ConfigUpdateBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para actualizar la configuraci&oacute;n de un Servicio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ServiceUpdateBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para actualizar o degradar un Servicio BizTalk a una versi&oacute;n diferente</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">PurgeBackupBizTalkService</td> 
<td data-morhtml="true" align="left">Operaci&oacute;n para purgar copias de seguridad del Servicio BizTalk fuera el per&iacute;odo de retenci&oacute;n</td> 
</tr>
</table>


Otras referencias
-----------------

-   [Copia de seguridad del servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [Restauración del servicio de BizTalk a partir de una copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servicios de BizTalk: Gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

