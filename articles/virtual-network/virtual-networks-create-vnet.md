<properties 
   pageTitle="Creación de una red virtual"
   description="Obtenga información sobre cómo crear una red virtual"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Creación de una red virtual

Cuando crea una red virtual, los servicios y las máquinas virtuales de la red virtual pueden comunicarse de manera segura entre sí sin tener que hacerlo a través de Internet. Crear una red virtual de Azure es un proceso relativamente rápido y sencillo si la red virtual no está diseñada para conectarse a otras redes virtuales o a la red local, ya que no se necesita adquirir y configurar un dispositivo VPN ni coordinar las direcciones IP que se elijan con otras redes virtuales o la red local.

>[AZURE.WARNING]No utilice este procedimiento para crear una red virtual que posteriormente se conectará a otras redes virtuales o su red local. Si desea crear una conexión segura híbrida o entre locales, consulte [Sobre la conectividad segura entre locales de redes virtuales](https://msdn.microsoft.com/library/azure/dn133798.aspx). Si desea crear una máquina virtual que se conecta a otra red virtual, consulte [Configurar una conexión VNet a VNet](https://msdn.microsoft.com/library/azure/dn690122.aspx).

## Configuración de la red virtual

1. Inicie sesión en el **Portal de administración de Azure**.

1. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.

1. En la página **Detalles de la red virtual** escriba la siguiente información:

	![Detalles de red virtual](./media/virtual-networks-create-vnet/IC736054.png)

	- **Nombre**: asigne un nombre a la red virtual. Por ejemplo, creamos el nombre *EastUSVNet*. Puede crear cualquier nombre que desee. Utilizará este nombre de red virtual al implementar las máquinas virtuales y los servicios, por lo que es preferible que este nombre no sea muy complicado.

	- **Ubicación**: seleccione la ubicación (región) en la lista desplegable. La ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales) cuando los implementa en esta red virtual. Por ejemplo, si desea que las máquinas virtuales se encuentren físicamente en *Oeste de EE.UU.*, seleccione esa región de ubicación. No se puede cambiar la región asociada a la red virtual después de crearla.

1. En la página **Servidores DNS y conectividad de VPN**, no realice ningún cambio. Simplemente haga clic en la flecha para avanzar a la página siguiente. De modo predeterminado, Azure proporciona una resolución de nombres básica para la red virtual. Es posible que sus requisitos de resolución de nombres sean más complejos de lo que pueda manejar la resolución de nombres básica de Azure. En ese caso, puede que desee agregar más adelante una máquina virtual que ejecute el DNS en su red virtual. Para obtener más información acerca de la resolución de nombres de Azure y DNS, consulte [Resolución de nombres (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx).

1. La página **Espacios de direcciones de la red virtual** es donde introduce el espacio de direcciones que desea usar para esta red virtual. A menos que exija un cierto intervalo de direcciones IP internas para las máquinas virtuales o desee crear una subred específica para las máquinas virtuales que recibirán una DIP estática, no es necesario que realice ningún cambio en esta página. Si desea crear varias subredes, puede hacer clic en **Agregar subred** en esta página para hacerlo.

	Información adicional:

	- Los intervalos de esta página contienen las direcciones IP internas dinámicas (DIP) que las máquinas virtuales recibirán cuando se implementen en esta red virtual. Estas direcciones IP sirven únicamente para la comunicación dentro de la red virtual. No son direcciones IP para extremos de Internet.

	- Dado que no va a conectar esta red virtual privada a la red local mediante una configuración de VPN entre locales, no es necesario que coordine estas configuraciones con los intervalos de direcciones IP existentes de la red local. Si cree que puede querer crear una configuración entre entornos más adelante, deberá coordinar los espacios de direcciones ahora con los intervalos que ya existen en el sitio local a fin de evitar problemas de enrutamiento. Cambiar los intervalos más adelante puede ser complicado, especialmente si tiene la superposición de intervalos de direcciones.

	![Espacio de direcciones](./media/virtual-networks-create-vnet/IC716778.png)

1. Haga clic en la marca de verificación de la parte inferior derecha de la página **Espacios de direcciones de la red virtual** y se empezará a crear la red virtual. Cuando se haya creado la red virtual, verá **Creada** bajo **Estado** en la página **Redes** del Portal de administración.

1. Una vez creada la red virtual, puede crear máquinas virtuales e instancias de PaaS dentro de la red virtual. Seleccione **De la galería** al crear una nueva máquina virtual para tener la opción de seleccionar su red virtual. Recuerde que si ya tiene implementadas instancias existentes de máquinas virtuales y PaaS, estas no se pueden mover a su nuevo VNet. Esto se debe a que los valores de configuración de red que se necesitan se agregarán durante la implementación.

## Adición de máquinas virtuales a la red virtual

Después de crear la red virtual, puede agregar nuevas máquinas virtuales a ella. Es importante crear primero la red virtual y después implementar la máquina virtual. Una vez implementada la máquina virtual, no se puede mover a una red virtual sin volver a implementarla. Si utiliza el Portal de administración para crear las máquinas virtuales, la interfaz para implementar la máquina virtual en una red virtual solo está disponible cuando selecciona **Nuevo/Proceso/Máquina virtual/De la galería**. Cuando recorre el Asistente para crear la máquina virtual, en la página **Configuración de la máquina virtual**, verá **Región/Grupo de afinidad/Red virtual**. En la lista desplegable, seleccione la red virtual ya creada. Para obtener más información acerca de la creación de las máquinas virtuales, consulte [Máquinas virtuales de Azure](../virtual-machines).

## Pasos siguientes

[Administración de las propiedades de la red virtual](../virtual-networks-settings)

[Administración de servidores DNS usados por una red virtual](../virtual-networks-manage-dns-in-vnet)

[Uso de las direcciones IP públicas en una red virtual](../virtual-networks-public-ip-within-vnet)

[Eliminación de una red virtual](../virtual-networks-delete-vnet)
 

<!---HONumber=July15_HO4-->