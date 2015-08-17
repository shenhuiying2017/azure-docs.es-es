<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
</tr>
<tr>
   <td valign="middle"><p>Tamaño de base de datos</p></td>
   <td valign="middle"><p>Depende del nivel de rendimiento.<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Inicios de sesión</p></td>
   <td valign="middle"><p>Depende del nivel de rendimiento.<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Uso de la memoria</p></td>
   <td valign="middle"><p>16&#160;MB de concesión de memoria durante más de 20 segundos</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sesiones</p></td>
   <td valign="middle"><p>Depende del nivel de rendimiento.<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamaño de tempdb</p></td>
   <td valign="middle"><p>5&#160;GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Duración de la transacción</p></td>
   <td valign="middle"><p>24 horas<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Bloqueos por transacción</p></td>
   <td valign="middle"><p>1 millón</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamaño de cada transacción</p></td>
   <td valign="middle"><p>2 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Porcentaje de espacio del registro total usado por transacción</p></td>
   <td valign="middle"><p>20&#160;%</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de solicitudes simultáneas (subprocesos de trabajo)</p></td>
   <td valign="middle"><p>Depende del nivel de rendimiento.<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>Base de datos SQL tiene niveles de rendimiento, por ejemplo, Básico, Estándar y Premium. Estándar y Premium se subdividen a su vez en más niveles de rendimiento. Para ver los límites detallados por nivel y nivel de servicio, consulte [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Si la transacción bloquea un recurso necesario para una tarea del sistema subyacente, la duración máxima es de 20 segundos.

<!---HONumber=August15_HO6-->