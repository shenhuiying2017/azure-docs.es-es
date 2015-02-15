<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#HConexión de máquinas virtuales en un Servicio en la nube

Cuando crea una máquina virtual, se crea automáticamente un servicio en la nube para dar cabida a esa máquina. Puede crear varias máquinas virtuales en el mismo servicio en la nube para que se puedan comunicar entre sí. 

> [AZURE.NOTE] Cuando las máquinas virtuales se encuentran en el mismo servicio en la nube, también puede equilibrar la carga entre ellas y administrar su disponibilidad, para lo que es necesario realizar pasos adicionales. Para obtener más información, consulte [Equilibrio de carga de máquinas virtuales](../../articles/load-balance-virtual-machines/) y [Administración de la disponibilidad de las máquinas virtuales](../../articles/manage-availability-virtual-machines/). 

En primer lugar, debe crear una máquina virtual nueva con un nuevo servicio en la nube. A continuación, puede crear más máquinas virtuales en el mismo servicio en la nube. Este las 'conecta'. 

1. Cree la primera máquina virtual siguiendo los pasos descritos en [Creación de una máquina virtual personalizada](../../articles/virtual-machines-create-custom/).

2. Siga el mismo proceso básico para crear las demás máquinas virtuales, salvo en el caso de que las agregue al servicio en la nube en lugar de crear un servicio en la nube. Por ejemplo, si crea un servicio de nube denominado  *EndpointTest*, elija ese servicio. En el siguiente gráfico se muestra así:

	![Agregar una máquina virtual a un servicio de nube existente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. Rellene los demás campos de esta página y de la siguiente y, a continuación, haga clic en la marca de verificación para crear la máquina virtual conectada.

#Recursos

Después de haber creado una máquina virtual, es conveniente agregar un disco de datos para que los servicios y las cargas de trabajo tengan una ubicación donde almacenar los datos. Consulte alguno de los recursos siguientes:

[Acoplamiento de un disco de datos a una máquina virtual Linux](http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Acoplamiento de un disco de datos a una máquina virtual de Windows](http://azure.microsoft.com/es-es/documentation/articles/storage-windows-attach-disk/)



<!--HONumber=42-->
