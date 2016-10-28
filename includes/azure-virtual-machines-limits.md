Recurso|Límite predeterminado|Límite máximo
---|---|---
[Máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-about.md) por servicio en la nube<sup>1</sup>|50|50
Extremos de entrada por servicio en la nube<sup>2</sup>|150|150

<sup>1</sup>Las máquinas virtuales creadas en Administración de servicios (en lugar de Administrador de recursos) se almacenan automáticamente en un servicio en la nube. Puede agregar más máquinas virtuales a ese servicio en la nube para usarlas para el equilibrio de carga y la disponibilidad. Consulte [Cómo conectar máquinas virtuales con un servicio de nube o red virtual](../articles/virtual-machines/virtual-machines-linux-classic-connect-vms.md).

<sup>2</sup>Los extremos de entrada permiten establecer comunicación con una máquina virtual desde fuera del servicio en la nube de la máquina virtual. Las máquinas virtuales en el mismo servicio en la nube o red virtual pueden comunicarse automáticamente entre sí. Consulte [Configuración de extremos en una máquina virtual](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

<!---HONumber=AcomDC_0323_2016-->