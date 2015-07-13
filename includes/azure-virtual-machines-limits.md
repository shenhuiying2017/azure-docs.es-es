<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
   <th align="left" valign="middle">Límite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Máquinas virtuales</a> por servicio en la nube<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Extremos de entrada por servicio en la nube<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Cuando crea una máquina virtual fuera de un grupo de recursos de Azure, se crea automáticamente un servicio en la nube para dar cabida a esa máquina. Después puede agregar varias máquinas virtuales en ese mismo servicio en la nube.

<sup>2</sup>Los extremos de entrada se usan para permitir la comunicación con las máquinas virtuales externas al servicio en la nube contenedor. Las máquinas virtuales dentro del mismo servicio en la nube permiten la comunicación automáticamente entre todos los puertos UDP y TCP para comunicación interna.

<!---HONumber=62-->