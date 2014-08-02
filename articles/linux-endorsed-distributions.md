<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="Endorsed distributions" pageTitle="Endorsed distributions of Linux in Azure" metaKeywords="" description="Learn about Linux on Azure-endorsed distributions, including guidelines for Ubuntu, OpenLogic, and SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Linux en distribuciones aprobadas por Azure
===========================================

Los siguientes asociados proporcionan las imágenes de distribución de la galería; además, trabajamos con la comunidad para brindar incluso más distribuciones aprobadas. Mientras tanto, siempre puede traer su propio Linux siguiendo las instrucciones de esta página.

Canonical
---------

<http://www.ubuntu.com/cloud/azure>

La ingeniería de Canonical y la gobernanza de comunidad abierta impulsan el éxito de Ubuntu en la informática en nube, servidor y cliente, incluidos servicios personales en la nube para consumidores. La visión de Canonical es una plataforma gratuita unificada en Ubuntu, desde el teléfono a la nube, con una familia de interfaces consistentes para el teléfono, tabletas, televisores y equipos de escritorio, que hace de Ubuntu la primera opción para distintas instituciones, desde proveedores de nube pública a fabricantes de aparatos electrónicos de consumo, y una de las favoritas de los tecnólogos.

Con desarrolladores y centros de ingeniería alrededor de todo el mundo, Canonical ocupa una posición exclusiva para asociarse con fabricantes de software, proveedores de contenido y desarrolladores de software para poner en el mercado las soluciones de Ubuntu, desde equipos a servidores y dispositivos portátiles.

OpenLogic
---------

<http://www.openlogic.com/azure>

OpenLogic es un proveedor líder de soluciones empresariales de código abierto para la nube y el centro de datos. OpenLogic ayuda a cientos de empresas líderes en una amplia variedad de sectores para adquirir, respaldar y controlar de manera segura el software de código abierto. OpenLogic ofrece soporte técnico de calidad comercial e indemnización para 600 paquetes de código abierto con el respaldo de la comunidad de expertos de OpenLogic, incluido soporte técnico a nivel empresarial para CentOS, además de ser el asociado de lanzamiento para brindar imágenes de CentOS en Azure.

Oracle
------

<http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html>

La estrategia de Oracle es ofrecer una amplia cartera de soluciones para nubes públicas y privadas, mientras se brinda a los clientes la posibilidad de elegir y flexibilidad en la manera de implementar el software de Oracle en nubes de Oracle, así como también otras nubes. La asociación de Oracle y Microsoft permite que los clientes implementen software de Oracle en nubes públicas y privadas de Microsoft con la confianza que brinda la certificación y el respaldo de Oracle. El compromiso y la inversión de Oracle™ en las soluciones de nubes públicas y privadas de Oracle siguen intactos.

SUSE
----

<http://www.suse.com/suse-linux-enterprise-server-on-azure>

SUSE Linux Enterprise Server en Azure es una plataforma probada que brinda confiabilidad y seguridad de nivel superior para la informática en nube. La versátil plataforma Linux de SUSE se integra de manera fluida con los servicios en la nube de Azure para brindar un entorno de nube fácilmente administrable. Y con más de 9.200 aplicaciones certificadas de más de 1.800 fabricantes de software independiente para SUSE Linux Enterprise Server, SUSE garantiza que las cargas de trabajo que se ejecutan de manera compatible en el centro de datos pueden implementarse con confianza en Azure.

Versiones compatibles
---------------------

La siguiente tabla muestra las distintas versiones de distribución, controladores de Linux Integration Services (LIS) y versiones del agente de Linux para Azure que se probaron para trabajar en Azure. Los controladores de LIS se encuentran disponibles en <http://www.microsoft.com/en-us/download/details.aspx?id=34603>. Las versiones del agente de Linux están disponibles en <https://github.com/windowsazure/walinuxagent>.

Esta tabla también incluye un vínculo a la revisión de compatibilidad de kernel que se requiere para que algunas versiones de distribución funcionen de manera óptima en Azure.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Distribución</th>		
	    <th>Versión</th>
	    <th>Controladores</th>
		<th>Revisión de compatibilidad de kernel</th>
		<th>Agente</th>
			</tr>
	<tr>
		<th>  UBUNTU de Canonical </th>
		<td>Ubuntu 12.04.1, 12.10 y 13.04</td>
		<td>En kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">Se requiere solo para 12.04 o 12.04.01 (en inglés)</a></td>
		<td>Paquete: en el repositorio de paquetes bajo walinuxagent Origen: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
			</tr>
	<tr>
		<th> CENTOS de Open Logic </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/p/?LinkID=254263">controladores de LIS</a> (en inglés); controladores CentOS 6.4+: en kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">Se requiere solo para 6.3 (en inglés)</a></td>
		<td>Paquete: en el <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">repositorio de paquetes de Open Logic </a>(en inglés) bajo walinuxagent<br />
			Origen: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
 		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>En kernel</td>
		<td>N/D</td>
		<td>Paquete: en repositorio, nombre: WALinuxAgent<br />
			Origen: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr><tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>En kernel</td>
		<td>N/D</td>
		<td>Paquete: en el repositorio <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</A> nombre: WALinuxAgent<br />
			Código fuente: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> OpenSUSE 13.1+</td>
		<td>En kernel</td>
		<td>N/D</td>
		<td>Paquete: en el repositorio <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</A> nombre: WALinuxAgent<br />
			Código fuente: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
</table>

