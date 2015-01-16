<properties urlDisplayName="Endorsed distributions" pageTitle="Distribuciones aprobadas de Linux en Azure" metaKeywords="" description="Obtenga información acerca de Linux en distribuciones aprobadas de Azure, incluyendo instrucciones para Ubuntu, OpenLogic y SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="szark" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="szark" />





#Linux en distribuciones aprobadas por Azure

Los siguientes asociados proporcionan las imágenes de distribución de la Galería de Azure; además, trabajamos con las distintas comunidades de Linux para brindar incluso más distribuciones aprobadas. Mientras tanto, para distribuciones no disponibles desde la galería, siempre puede ofrecer su propio Linux siguiendo las directrices de [esta página](../virtual-machines-linux-create-upload-vhd/).

## Canonical ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

La ingeniería de Canonical y la gobernanza de comunidad abierta impulsan el éxito de Ubuntu en la informática en nube, servidor y cliente, incluidos servicios personales en la nube para consumidores. La visión de Canonical es una plataforma gratuita unificada en Ubuntu, desde el teléfono a la nube, con una familia de interfaces consistentes para el teléfono, tabletas, televisores y equipos de escritorio, que hace de Ubuntu la primera opción para distintas instituciones, desde proveedores de nube pública a fabricantes de aparatos electrónicos de consumo, y una de las favoritas de los tecnólogos.

Con desarrolladores y centros de ingeniería alrededor de todo el mundo, Canonical ocupa una posición exclusiva para asociarse con fabricantes de software, proveedores de contenido y desarrolladores de software para poner en el mercado las soluciones de Ubuntu, desde equipos a servidores y dispositivos portátiles.

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic es un proveedor líder de soluciones empresariales de código abierto para la nube y el centro de datos. OpenLogic ayuda a cientos de empresas líderes en una amplia variedad de sectores para adquirir, respaldar y controlar de manera segura el software de código abierto. OpenLogic ofrece soporte técnico de calidad comercial e indemnización para 600 paquetes de código abierto con el respaldo de la comunidad de expertos de OpenLogic, incluido soporte técnico a nivel empresarial para CentOS, además de ser el asociado de lanzamiento para brindar imágenes basadas en CentOS en Azure.

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La estrategia de Oracle es ofrecer una amplia cartera de soluciones para nubes públicas y privadas, mientras se brinda a los clientes la posibilidad de elegir y flexibilidad en la manera de implementar el software de Oracle en nubes de Oracle, así como también otras nubes.  La asociación de Oracle y Microsoft permite que los clientes implementen software de Oracle en nubes públicas y privadas de Microsoft con la confianza que brinda la certificación y el respaldo de Oracle.  El compromiso y la inversión de Oracle en las soluciones de nubes públicas y privadas de Oracle siguen intactos.

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server en Azure es una plataforma probada que brinda confiabilidad y seguridad de nivel superior para la informática en nube. La versátil plataforma Linux de SUSE se integra de manera fluida con los servicios en la nube de Azure para brindar un entorno de nube fácilmente administrable. Y con más de 9.200 aplicaciones certificadas de más de 1.800 fabricantes de software independiente para SUSE Linux Enterprise Server, SUSE garantiza que las cargas de trabajo que se ejecutan de manera compatible en el centro de datos pueden implementarse con confianza en Azure.

## Versiones compatibles ##

La siguiente tabla muestra las distintas versiones de distribución, controladores de Linux Integration Services (LIS) y versiones del agente de Linux para Azure que se probaron para trabajar en Azure. Los controladores de LIS están integrados en el kernel de la distribución de forma predeterminada o están disponibles [aquí](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Las versiones del Agente de Linux están disponibles en el repositorio de paquetes de distribución o en [Github](https://github.com/azure/walinuxagent).

La tabla también incluye un vínculo a la [revisión de compatibilidad del kernel de Linux](http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409) que necesitan algunas versiones de distribución/kernel para funcionar de manera óptima en Azure.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Distribución</th>		
	    <th>Versión</th>
	    <th>Controladores</th>
		<th>Revisión de compatibilidad de kernel</th>
		<th>Agente</th>
			</tr>
	<tr>
		<th>  Ubuntu de Canonical </th>
		<td> Ubuntu 12.04.1+, 14.04 y 14.10 </td>
		<td>En kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&clcid=0x409">Se requiere solo para 12.04 o 12.04.01 (en inglés)</a></td>
		<td>Paquete: En el repositorio de paquetes bajo walinuxagent <br />
			Origen: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> CentOS de OpenLogic </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">Controladores de LIS</a>; controladores de CentOS 6.4+: en Kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&clcid=0x409">Se requiere solo para 6.3</a></td>
		<td>Paquete: en <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">el repositorio del paquete de OpenLogin </a> en walinuxagent<br />
			Origen: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		
	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> En kernel </td>
		<td> N/D </td>
		<td> Origen: <a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>En kernel</td>
		<td>N/D</td>
		<td>Paquete: en repositorio, nombre: WALinuxAgent<br />
			Origen: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>En kernel</td>
		<td>N/D</td>
		<td>Paquete: En el <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> repo, name: WALinuxAgent<br />
			Source Code: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> openSUSE 13.1+</td>
		<td>En kernel</td>
		<td>N/D</td>
		<td>Paquete: En el <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> repo, name: WALinuxAgent<br />
			Source Code: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>


<!--HONumber=35.1-->
