<properties 
   pageTitle="Administración de las propiedades de la red virtual (VNet)"
   description="Obtenga información sobre cómo ver y editar la configuración de redes virtuales"
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
   ms.date="09/02/2015"
   ms.author="telmos" />

# Administración de las propiedades de la red virtual (VNet)
Puede ver y modificar la configuración de red virtual mediante el portal de Azure.

## Visualización de la configuración de redes virtuales

1. Inicie sesión en el Portal de administración.

1. En el panel de navegación, haga clic en **Redes** y, a continuación, haga clic en el nombre de la red virtual en la columna **Nombre**.

1. Haga clic en **Panel** para ver la siguiente información:

	- **red virtual:** es la red virtual y subredes junto con la red local y el servidor DNS.

	- **datos de entrada, datos de salida:** visible cuando se dispone de conectividad entre entornos. Estos son los datos que entran y salen de la red virtual.

	- **dirección IP de puerta de enlace:** visible cuando se ha configurado una puerta de enlace. Es la dirección IP de la puerta de enlace: Se trata de la dirección IP que va a utilizar para configurar el dispositivo VPN para la conectividad entre entornos.

	- **recursos:** son los recursos de Azure conectados a esta red virtual.

	- **vista rápida:** un lugar para encontrar rápidamente la información siguiente:

		- **Descargar paquete de VPN de cliente:** este paquete se utiliza al crear conexiones VPN entre entornos punto a sitio. Descargue el paquete que pertenece al cliente.

		- **Descargar script de dispositivo VPN:** estas plantillas se utilizan para ayudar a configurar los dispositivos VPN en conexiones entre entornos de sitio a sitio.

		- **estado**

		- **Id. de suscripción**
		
		- **Id. de red virtual**
		
		- **ubicación**

1. Haga clic en **Configurar** para ver la siguiente información:

	- **servidores dns:** son los servidores DNS y sus direcciones IP correspondientes que los clientes de la red virtual utilizarán para la resolución de nombres.

	- **conectividad de punto a sitio:** visible cuando se ha configurado la red virtual para las conexiones punto a sitio.

	- **conectividad de sitio a sitio:** visible cuando se ha configurado la red virtual para las conexiones sitio a sitio.

	- **red local:** se muestra si dispone de conectividad de sitio a sitio. Se trata de la red local seleccionada que representa su sitio local. La configuración de red local puede configurarse en **redes**, en la página **Redes locales**.
	
	- **espacios de direcciones de la red virtual:** el espacio de direcciones y subredes de la red virtual.

1. Haga clic en **Certificados** para ver la siguiente información:

	- **Certificado raíz cargado:** para configuraciones VPN punto a sitio
	
	- **Huella digital de certificado**
	
	- **Caducidad de certificados**
	
	- **Cargar:** carga un certificado raíz
	
	- **Eliminar:** elimina un certificado raíz seleccionado

## Edición de la configuración de redes virtuales

Después de implementar la red virtual, solo se pueden modificar ciertas configuraciones. Los pasos siguientes ayudan a modificar las opciones de configuración de las redes virtuales mediante el Portal de administración.

1. Inicie sesión en el Portal de administración.

1. En el panel de navegación, haga clic en **Redes** y, a continuación, haga clic en el nombre de la red virtual en la columna **Nombre**.

1. Haga clic en **Configurar**.

	- En **servidores DNS**, puede agregar servidores DNS adicionales para la resolución de nombres. Si agrega servidores DNS adicionales después de haber implementado las máquinas virtuales a la red virtual, debe reiniciar cada máquina virtual para que seleccione el nuevo servidor DNS adicional.
	
	- En la sección **conectividad de punto a sitio** puede configurar el espacio de direcciones que desea designar para las conexiones punto a sitio.
	
	- En la sección **conectividad de sitio a sitio** puede configurar las opciones de sitio a sitio.
	
	- En la sección **espacios de direcciones de la red virtual** puede agregar espacio adicional de direcciones y subredes. Para agregar subredes, haga clic en **agregar subredes** y configure el espacio de direcciones que desea utilizar. Para agregar una subred de puerta de enlace para la conectividad entre entornos, haga clic en **Agregar subred de puerta de enlace**. Solo puede agregar una subred de puerta de enlace por cada red virtual.

1. Una vez que haya terminado de cambiar la configuración, haga clic en **GUARDAR**, en la parte inferior de la página.

## Pasos siguientes

[Administración de servidores DNS usados por una red virtual](../virtual-networks-manage-dns-in-vnet)

[Uso de las direcciones IP públicas en una red virtual](../virtual-networks-public-ip-within-vnet)

[Eliminación de una red virtual](../virtual-networks-delete-vnet)

<!---HONumber=Oct15_HO3-->