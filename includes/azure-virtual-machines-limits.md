Recurso|Límite predeterminado|Límite máximo
--|--|--
[Máquinas virtuales](../documentation/services/virtual-machines/) por servicio en la nube<sup>1</sup>|50|50
Extremos de entrada por servicio en la nube<sup>2</sup>|150|150

<sup>1</sup>Las máquinas virtuales creadas en Administración de servicios (en lugar de Administrador de recursos) se almacenan automáticamente en un servicio en la nube. Puede agregar más máquinas virtuales a ese servicio en la nube para usarlas para el equilibrio de carga y la disponibilidad. Consulte [Cómo conectar máquinas virtuales con un servicio de nube o red virtual](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Los extremos de entrada permiten las comunicaciones con una máquina virtual desde fuera del servicio en la nube de la máquina virtual. Las máquinas virtuales en el mismo servicio en la nube o red virtual pueden comunicarse automáticamente entre sí. Consulte: [Configuración de extremos en una máquina virtual](../virtual-machines/virtual-machines-set-up-endpoints.md)

<!---HONumber=August15_HO6-->