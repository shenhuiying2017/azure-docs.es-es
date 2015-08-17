<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
   <th align="left" valign="middle">Límite máximo</th>
</tr>
<tr>
   <td valign="middle"><p>Cuentas de Servicios multimedia de Azure (AMS) en una única suscripción</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>Recursos por cuenta de AMS</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1 000 000</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tareas encadenadas por trabajo</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>Recursos por tarea.</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Recursos por trabajo</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Trabajos por cuenta de AMS </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50 000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Localizadores únicos asociados a un recurso al mismo tiempo</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Canales activos por cuenta de AMS </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Programas en estado detenido por canal </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Programas en estado de ejecución por canal </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Extremos de streaming en estado de ejecución por cuenta de ASM</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unidades de streaming por extremo de streaming </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unidades de codificación por cuenta de AMS </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> Puede solicitar la actualización de los límites de esta cuota abriendo una incidencia de soporte técnico. No cree más cuentas de AMS para aumentar los límites; en su lugar, envíe una incidencia de soporte técnico.

<sup>2</sup> Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados. Puede eliminar los trabajos antiguos con **IJob.Delete** o con la solicitud HTTP **DELETE**.

<sup>3</sup> Al realizar una solicitud para obtener una lista de las entidades Job, se devolverá un máximo de 1000 por solicitud. Si necesita realizar un seguimiento de todos los trabajos enviados, puede usar top y skip, tal como se describe en [Opciones de consulta del sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Los localizadores no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM).

<!---HONumber=August15_HO6-->