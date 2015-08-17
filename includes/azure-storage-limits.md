<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
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
   <td valign="middle"><p>Máximo de 8&#160;kB de IOPS por disco persistente (máquina virtual de nivel Básico)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Máximo de 8&#160;kB de IOPS por disco persistente (máquina virtual de nivel Estándar)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB) por cuenta de almacenamiento</p></td>
   <td valign="middle"><p>Hasta 20.000 IOPS, entidades por segundo o mensajes por segundo</p></td>
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
   <td valign="middle"><p>Rendimiento de destino para un solo recurso compartido de archivos (vista previa)</p></td>
   <td valign="middle"><p>Hasta 60 MB por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima<sup>2</sup> por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
   <td valign="middle"><p>10 Gbps si GRS<sup>3</sup> está habilitado, 20 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Salida máxima<sup>2</sup> por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
   <td valign="middle"><p>20 Gbps si GRS<sup>3</sup> está habilitado, 30 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima<sup>2</sup> por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
   <td valign="middle"><p>5 Gbps si GRS<sup>3</sup> está habilitado, 10 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Salida máxima<sup>2</sup> por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
   <td valign="middle"><p>10 Gbps si GRS<sup>3</sup> está habilitado, 15 Gbps para LRS</p></td>
</tr>
</table>

<sup>1</sup>El límite de velocidad de solicitudes total para una cuenta de almacenamiento es 20.000 IOPS. Si una máquina virtual usa el número máximo de IOPS por disco, para evitar una posible limitación, asegúrese de que el número total de IOPS en todos los discos duros virtuales de las máquinas virtuales no supere el límite de la cuenta de almacenamiento (20.000 IOPS).

Puede calcular aproximadamente el número de discos muy usados admitidos por una única cuenta de almacenamiento en función del límite de transacciones. Por ejemplo, para una máquina virtual de nivel Básico, el número máximo de discos muy usados es alrededor de 66 (20.000/300 IOPS por disco) y, para una máquina virtual de nivel Estándar, aproximadamente 40 (20.000/500 IOPS por disco). Sin embargo, la cuenta de almacenamiento admite un mayor número de discos si no todos están siendo muy usados al mismo tiempo.

<sup>2</sup>*Entrada* se refiere a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

<sup>3</sup>GRS hace referencia al almacenamiento con redundancia geográfica, mientras que LRS se refiere al almacenamiento con redundancia local.

<!---HONumber=August15_HO6-->