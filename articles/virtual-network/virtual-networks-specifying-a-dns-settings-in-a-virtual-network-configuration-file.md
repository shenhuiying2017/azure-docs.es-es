<properties 
   pageTitle="Especificar una configuración DNS en un archivo de configuración de red virtual | Microsoft Azure"
   description="Cómo cambiar la configuración del servidor DNS en una red virtual con un archivo de configuración de red virtual"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/25/2015"
   ms.author="joaoma" />

# Especificación de una configuración DNS en un archivo de configuración de red virtual

Un archivo de configuración de red tiene dos elementos que puede utilizar para especificar la configuración del sistema de nombres de dominio (DNS): **DnsServers** y **DnsServerRef**. Puede agregar una lista de los servidores DNS especificando sus direcciones IP y nombres de referencia en el elemento **DnsServers**. Asimismo, puede usar un elemento **DnsServerRef** para especificar qué entradas del servidor DNS desde el elemento DnsServers se usan en diferentes sitios de red de la red virtual.

>[AZURE.IMPORTANT]Para obtener más información sobre cómo configurar el archivo de configuración de red, consulte [Configuración de una red virtual mediante un archivo de configuración de red](virtual-networks-using-network-configuration-file.md). Para obtener más información acerca de cada elemento de un archivo de configuración de red, consulte [Esquema de configuración de redes virtuales de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

El archivo de configuración de red puede contener los siguientes elementos. El título de cada elemento se vincula a una página que proporciona información adicional acerca de la configuración del valor de elemento.

[Elemento DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING]El atributo **nombre** en el elemento **DnsServer** solo se usa como referencia del elemento **DnsServerRef**. No representa el nombre de host del servidor DNS. Cada valor del atributo **DnsServer** debe ser único en toda la suscripción de Microsoft Azure

[Elemento Sitios de red virtual](http://go.microsoft.com/fwlink/?LinkId=248093)

	<DnsServersRef>
	  <DnsServerRef name="ID1" />
	  <DnsServerRef name="ID2" />
	  <DnsServerRef name="ID3" />
	</DnsServersRef>

>[AZURE.NOTE]Para especificar la configuración del elemento Sitios de red virtual, debe definirse previamente en el elemento DNS. El *nombre* DnsServerRef del elemento Sitios de red virtual debe hacer referencia a un valor de nombre especificado en el elemento DNS del *nombre* DnsServer.

## Pasos siguientes

[Configuración de una red virtual mediante archivos de configuración de red](virtual-networks-using-network-configuration-file.md)

[Esquema de configuración de red virtual de Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Esquema de configuración del servicio de Azure](https://msdn.microsoft.com/library/windowsazure/ee758710)

<!---HONumber=Oct15_HO3-->