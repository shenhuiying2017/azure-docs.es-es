<properties 
   pageTitle="Eliminación de una red virtual (VNet)"
	description="Obtenga información acerca de cómo eliminar una red virtual existente"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="carolz"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/29/2015"
	ms.author="telmos"/>

# Eliminación de una red virtual (VNet)

Si desea eliminar una red virtual, no solo hay que hacer clic en **Eliminar**. Hay que llevar a cabo antes algunos procedimientos:

1. **Guardar la configuración (opcional)**: si desea guardar la configuración de la red virtual en un archivo local, exporte el archivo de configuración antes de eliminar la red virtual. Consulte [Exportación de la configuración de la red virtual a un archivo de configuración de red](virtual-networks-using-network-configuration-file.md) para obtener más información. Guardar la configuración le permite volver a crear la red virtual en el futuro, si es preciso.

1. **Eliminar la puerta de enlace de red virtual**: si configura una puerta de enlace para la red virtual, debe eliminarla antes de eliminar la red virtual. Para eliminar la puerta de enlace de la red virtual, vaya a la página Panel de la red virtual. En la parte inferior de la página, haga clic en **Eliminar puerta de enlace**.
						
	Es posible que tenga que esperar entre 5 y 10 minutos para que se elimine la puerta de enlace antes de continuar con los pasos siguientes.

1. **Eliminar servicios en la nube, sitios web y máquinas virtuales**: si ha implementado algo en la red virtual, deberá eliminar esos objetos antes de poder eliminar la red virtual.

1. **Eliminar la red virtual**: haga clic a la derecha de la fila *Nombre* para resaltar la red virtual que desea eliminar y después haga clic en **Eliminar** en la parte inferior de la página. Siga las instrucciones que aparecen en pantalla.

1. **Procedimiento adicional**: también puede eliminar cualquier configuración de la red local, los servidores DNS y el grupo de afinidad después de eliminar la red virtual.
 

<!---HONumber=August15_HO9-->