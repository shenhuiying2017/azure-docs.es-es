<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
   <th align="left" valign="middle">Límite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Redes virtuales</a><sup>1</sup> por suscripción</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Máquinas totales<sup>2</sup> por red virtual</p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Conexiones TCP concurrentes para una máquina virtual o instancia de rol</p></td>
   <td valign="middle"><p>500 KB</p></td>
   <td valign="middle"><p>500 KB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Listas de control de acceso (ACL) por extremo<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sitios de red local por red virtual</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Cada red virtual admite una sola [puerta de enlace de red virtual](http://msdn.microsoft.com/library/azure/jj156210.aspx).

<sup>2</sup>El número total de máquinas incluye las máquinas virtuales y las instancias de rol de trabajo y web.

<sup>3</sup>ACL se admite en extremos de entrada para máquinas virtuales. Para roles de trabajo y web, se admite en extremos de entrada y de entrada de instancia.

<!---HONumber=62-->