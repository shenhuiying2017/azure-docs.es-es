<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso<sup>1</sup></th>
   <th align="left" valign="middle">Límite predeterminado</th>
</tr>
<tr>
   <td valign="middle"><p>TB por cuenta de almacenamiento</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamaño máximo de un contenedor de blobs, una tabla o una cola</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento</p></td>
   <td valign="middle"><p>El único límite es los 500 TB de capacidad de la cuenta de almacenamiento</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamaño máximo de un recurso compartido de archivos</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de archivos en un recurso compartido de archivos</p></td>
   <td valign="middle"><p>El único límite es la capacidad total de 5 TB del recurso compartido de archivos</p></td>
</tr>
<tr>
   <td valign="middle"><p>Máximo de 8 KB IOPS por disco persistente (nivel Básico)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Máximo de 8 KB IOPS por disco persistente (nivel Estándar)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB) por cuenta de almacenamiento</p></td>
   <td valign="middle"><p>Hasta 20.000 entidades o mensajes por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimiento de un blob</p></td>
   <td valign="middle"><p>Hasta 60 MB por segundo o hasta 500 solicitudes por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimiento de una cola (mensajes de 1 KB)</p></td>
   <td valign="middle"><p>Hasta 2000 mensajes por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimiento de una partición de tabla (entidades de 1 KB)</p></td>
   <td valign="middle"><p>Hasta 2000 entidades por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
   <td valign="middle"><p>10 Gbps si GRS<sup>3</sup> está habilitado, 20 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Salida máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
   <td valign="middle"><p>20 Gbps si GRS<sup>3</sup> está habilitado, 30 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
   <td valign="middle"><p>5 Gbps si GRS<sup>3</sup> está habilitado, 10 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Salida máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
   <td valign="middle"><p>10 Gbps si GRS<sup>3</sup> está habilitado, 15 Gbps para LRS</p></td>
</tr>
</table>

<sup>1</sup>Para obtener más detalles sobre estos límites, vea [Objetivos de escalabilidad y rendimiento de Almacenamiento de Azure](../articles/storage/storage-scalability-targets.md).

<sup>2</sup>En el caso de máquinas virtuales del nivel Básico, no ponga más de 66 VHD de uso intensivo en una cuenta de almacenamiento para evitar el límite de velocidad de solicitudes de 20 000 (20 000/300). Para máquinas virtuales del nivel Estándar, no ponga más de 40 VHD de uso intensivo en una cuenta de almacenamiento (20.000/500). Para obtener más información, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

<sup>3</sup>GRS es [almacenamiento con redundancia geográfica](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx). LRS es [almacenamiento con redundancia local](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx). Tenga en cuenta que GRS incluye también redundancia local.

<!---HONumber=62-->