<properties 
	pageTitle="Distribuciones aprobadas de Linux en Azure" 
	description="Obtenga información acerca de Linux en distribuciones aprobadas de Azure, incluyendo instrucciones para Ubuntu, OpenLogic y SUSE." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="szark"/>



#Linux en distribuciones aprobadas por Azure

Varios asociados proporcionan las imágenes de Linux de la Galería de Azure; además, trabajamos con distintas comunidades de Linux para agregar aun más tipos a la lista de distribuciones aprobadas. Mientras tanto, para distribuciones no disponibles desde la galería, siempre puede ofrecer su propio Linux siguiendo las directrices de [esta página](virtual-machines-linux-create-upload-vhd.md).


## Distribuciones y versiones admitidas ##

En la tabla siguiente se enumeran las distribuciones y versiones de Linux que se admiten en Azure.

Los controladores de los Servicios de integración de Linux (LIS) para Hyper-V y Azure son módulos del kernel que Microsoft aporta directamente al kernel de Linux del canal de subida. Los controladores de LIS están integrados en el núcleo de distribución de forma predeterminada o, para distribuciones anteriores basadas en RHEL/CentOS, están disponibles [aquí](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) en forma de descarga independiente. Consulte [este artículo](virtual-machines-linux-create-upload-vhd-generic.md#linux-kernel-requirements) para obtener más información acerca de los controladores de LIS.

El Agente de Linux de Azure ya está preinstalado en las imágenes de la galería de Linux y generalmente está disponible en el repositorio de paquetes de distribución. El código fuente se puede encontrar en [GitHub](https://github.com/azure/walinuxagent).

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Distribución</th>		
	    <th>Versión</th>
	    <th>Controladores</th>
		<th>Agente</th>
			</tr>
	<tr>
		<th>  Ubuntu de Canonical </th>
		<td> Ubuntu 12.04.1+, 14.04 y 14.10 </td>
		<td>En kernel</td>
		<td>Paquete: En el repositorio, en "walinuxagent" <br />
			Origen: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> CentOS de OpenLogic </th>
		<td> CentOS 6.3+</td>
	    <td> CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">Descarga de LIS</a><br/>
	        CentOS 6.4 +: En kernel</td>
		<td>Paquete: En el repositorio <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">OpenLogic</a>, en "WALinuxAgent"<br />
			Origen: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		
	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> En kernel </td>
		<td> Origen: <a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>En kernel</td>
		<td>Paquete: En el repositorio, en "WALinuxAgent"<br />
			Origen: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>En kernel</td>
		<td>Paquete: En el repositorio <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a>, en "WALinuxAgent"<br />
			Origen: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> openSUSE 13.1+</td>
		<td>En kernel</td>
		<td>Paquete: En el repositorio <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a>, en "WALinuxAgent"<br />
			Código fuente: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>

## Asociados

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

La ingeniería de Canonical y la gobernanza de comunidad abierta impulsan el éxito de Ubuntu en la informática en nube, servidor y cliente, incluidos servicios personales en la nube para consumidores. La visión de Canonical es una plataforma gratuita unificada en Ubuntu, desde el teléfono a la nube, con una familia de interfaces consistentes para el teléfono, tabletas, televisores y equipos de escritorio, que hace de Ubuntu la primera opción para distintas instituciones, desde proveedores de nube pública a fabricantes de aparatos electrónicos de consumo, y una de las favoritas de los tecnólogos.

Con desarrolladores y centros de ingeniería alrededor de todo el mundo, Canonical ocupa una posición exclusiva para asociarse con fabricantes de software, proveedores de contenido y desarrolladores de software para poner en el mercado las soluciones de Ubuntu, desde equipos a servidores y dispositivos portátiles.


### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

En el sitio web de CoreOS:

*CoreOS está diseñado para la seguridad, coherencia y confiabilidad. En lugar de instalar paquetes a través de yum o apt, CoreOS utiliza contenedores de Linux para administrar los servicios en un nivel superior de abstracción. Un código y todas las dependencias de un servicio único se empaquetan dentro de un contenedor que se puede ejecutar en uno o varios equipos de CoreOS.*


### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic es un proveedor líder de soluciones empresariales de código abierto para la nube y el centro de datos. OpenLogic ayuda a cientos de empresas líderes en una amplia variedad de sectores para adquirir, respaldar y controlar de manera segura el software de código abierto. OpenLogic ofrece soporte técnico de calidad comercial e indemnización para 600 paquetes de código abierto con el respaldo de la comunidad de expertos de OpenLogic, incluido soporte técnico a nivel empresarial para CentOS, además de ser el asociado de lanzamiento para brindar imágenes basadas en CentOS en Azure.


### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La estrategia de Oracle es ofrecer una amplia cartera de soluciones para nubes públicas y privadas, mientras se brinda a los clientes la posibilidad de elegir y flexibilidad en la manera de implementar el software de Oracle en nubes de Oracle, así como también otras nubes. La asociación de Oracle y Microsoft permite que los clientes implementen software de Oracle en nubes públicas y privadas de Microsoft con la confianza que brinda la certificación y el respaldo de Oracle. El compromiso y la inversión de Oracle en las soluciones de nubes públicas y privadas de Oracle siguen intactos.


### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server en Azure es una plataforma probada que brinda confiabilidad y seguridad de nivel superior para la informática en nube. La versátil plataforma Linux de SUSE se integra de manera fluida con los servicios en la nube de Azure para brindar un entorno de nube fácilmente administrable. Y con más de 9.200 aplicaciones certificadas de más de 1.800 fabricantes de software independiente para SUSE Linux Enterprise Server, SUSE garantiza que las cargas de trabajo que se ejecutan de manera compatible en el centro de datos pueden implementarse con confianza en Azure.

<!---HONumber=58--> 