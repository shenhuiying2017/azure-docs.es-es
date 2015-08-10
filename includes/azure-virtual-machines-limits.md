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

<sup>1</sup>Las máquinas virtuales creadas en Administración de servicios (en lugar de Administrador de recursos) se almacenan automáticamente en un servicio en la nube. Puede agregar más máquinas virtuales a ese servicio en la nube para usarlas para el equilibrio de carga y la disponibilidad. Consulte [Cómo conectar máquinas virtuales con una red virtual o un servicio de nube](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Los extremos de entrada permiten establecer comunicación con una máquina virtual desde fuera del servicio en la nube de la máquina virtual. Las máquinas virtuales en el mismo servicio en la nube o red virtual pueden comunicarse automáticamente entre sí. Consulte [Configuración de extremos en una máquina virtual](../virtual-machines/virtual-machines-set-up-endpoints.md).

<!---HONumber=July15_HO5-->