<properties 
	pageTitle="Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure" 
	description="Proporciona una lista de límites, cuotas y restricciones de suscripción y servicio comunes de Azure. Esto incluye información acerca de cómo aumentar los límites junto con los valores máximos."
	services="" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="jroth"/>

# Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure

## Información general

En este documento se especifican algunos de los límites más comunes de Microsoft Azure. Tenga en cuenta que actualmente esto no cubre todos los servicios de Azure. Con el tiempo, estos límites se expandirán y actualizarán para abarcar más de la plataforma.

> [AZURE.NOTE] Si desea aumentar el límite por encima del **Límite predeterminado**, puede [abrir una solicitud de servicio al cliente en línea sin cargo alguno][azurelimitsblogpost]. Los límites no se pueden subir por encima del valor de **Límite máximo** que figura en las tablas siguientes. Si la columna **Límite máximo** no existe, el recurso especificado no tiene límites ajustables.

## Límites de suscripción

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
   <th align="left" valign="middle">Límite máximo</th>
</tr>
<tr>
   <td valign="middle"><p>Núcleos por <a href="http://msdn.microsoft.com/library/azure/hh531793.aspx">suscripción</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10.000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/gg456328.aspx">Coadministradores</a> por suscripción</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/storage-create-storage-account/">Cuentas de almacenamiento</a> por suscripción</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Servicios en la nube</a> por suscripción</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Redes virtuales</a> por suscripción<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/jj157100.aspx">Redes locales</a> por suscripción</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Servidores de base de datos SQL por suscripción</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>Bases de datos SQL por servidor</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>Servidores DNS por suscripción</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Direcciones IP reservadas por suscripción</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Certificados de servicio hospedados por suscripción</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156085.aspx">Grupos de afinidad</a> por suscripción</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/">Grupos de recursos</a> por suscripción</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/services/batch/">Cuentas de vista previa de lotes</a> por región y por suscripción</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>Recuento de instancias extrapequeñas como un núcleo hacia el límite de núcleos a pesar de utilizar un núcleo parcial.

<sup>2</sup>Cada red virtual admite una sola puerta de enlace de red virtual.

## Límites de servicios en la nube

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
   <th align="left" valign="middle">Límite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Roles web y de trabajo por implementación<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint">Extremos de entrada de instancia</a> por implementación</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint">Extremos de entrada</a> por implementación</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint">Extremos internos</a> por implementación</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Cada servicio en la nube con roles web y de trabajo puede tener dos implementaciones, una para producción y otra para ensayo. Tenga en cuenta también que este límite hace referencia al número de roles (configuración) y no al número de instancias por rol (escalado). 

## Límites de máquinas virtuales

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
   <th align="left" valign="middle">Límite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Máquinas virtuales</a> por servicio en la nube<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Extremos de entrada por servicio en la nube<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Cuando crea una máquina virtual, se crea automáticamente un servicio en la nube para dar cabida a esa máquina. Después puede agregar varias máquinas virtuales en ese mismo servicio en la nube.

<sup>2</sup>Los extremos de entrada se usan para permitir la comunicación con las máquinas virtuales externas al servicio en la nube contenedor. Las máquinas virtuales dentro del mismo servicio en la nube permiten la comunicación automáticamente entre todos los puertos UDP y TCP para comunicación interna.

## Límites de Sitios web

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## Límites de red

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Límite predeterminado</th>
   <th align="left" valign="middle">Límite máximo</th>
</tr>
<tr>
   <td valign="middle"><p>Total de máquinas<sup>1</sup> por <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">red virtual</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Conexiones TCP concurrentes para una máquina virtual o instancia de rol</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Listas de control de acceso (ACL) por extremo<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sitios de red local por red virtual</p></td>
   <td valign="middle"><p>de</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>El número total de máquinas incluye las máquinas virtuales y las instancias de rol de trabajo y web.

<sup>2</sup>Cada red virtual admite una sola [puerta de enlace][de red virtual].

<sup>3</sup>ACL se admite en extremos de entrada para máquinas virtuales. Para roles de trabajo y web, se admite en extremos de entrada y de entrada de instancia.

## Límites de almacenamiento

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso<sup>1</sup></th>
   <th align="left" valign="middle">Límite predeterminado</th>
</tr>
<tr>
   <td valign="middle"><p>TB por cuenta de almacenamiento</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamaño máximo de un contenedor de blobs, una tabla o una cola</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento</p></td>
   <td valign="middle"><p>El único límite es los 500 TB de capacidad de la cuenta de almacenamiento</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamaño máximo de un recurso compartido de archivos</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de archivos en un recurso compartido de archivos</p></td>
   <td valign="middle"><p>El único límite es la capacidad total de 5 TB del recurso compartido de archivos</p></td>
</tr>
<tr>
   <td valign="middle"><p>Máximo de 8 KB IOPS por disco persistente (nivel Básico)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Máximo de 8 KB IOPS por disco persistente (nivel Estándar)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB) por cuenta de almacenamiento</p></td>
   <td valign="middle"><p>Hasta 20.000 entidades o mensajes por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimiento de un blob</p></td>
   <td valign="middle"><p>Hasta 60 MB por segundo o hasta 500 solicitudes por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimiento de una cola (mensajes de 1 KB)</p></td>
   <td valign="middle"><p>Hasta 2000 mensajes por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimiento de una partición de tabla (entidades de 1 KB)</p></td>
   <td valign="middle"><p>Hasta 2000 entidades por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
   <td valign="middle"><p>10 GB/s si GRS<sup>3</sup> habilitado, 20 GB/s para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Salida máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
   <td valign="middle"><p>20 GB/s si GRS<sup>3</sup> habilitado, 30 GB/s para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
   <td valign="middle"><p>5 GB/s si GRS<sup>3</sup> habilitado, 10 GB/s para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Salida máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
   <td valign="middle"><p>10 GB/s si GRS<sup>3</sup> habilitado, 15 GB/s para LRS</p></td>
</tr>
</table>

<sup>1</sup>Para obtener más información sobre estos límites, consulte [Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure][storagelimits]. 

<sup>2</sup>Para máquinas virtuales del nivel Básico, no ponga más de 66 VHD de uso intensivo en una cuenta de almacenamiento para evitar el límite de velocidad de solicitudes de 20.000 (20.000/300). Para máquinas virtuales del nivel Estándar, no ponga más de 40 VHD de uso intensivo en una cuenta de almacenamiento (20.000/500). Para obtener más información, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure][vmsizes]. 

<sup>3</sup>GRS es [Almacenamiento con redundancia geográfica][georedundantstorage]. LRS es [Almacenamiento con redundancia local][locallyredundantstorage]. Tenga en cuenta que GRS incluye también redundancia local.

## Límites de la vista previa de lote

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

## Límites de DocumentDB (vista previa)

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## Límites de Base de datos SQL

Para los límites de Base de datos SQL, vea los temas siguientes:

 - [Niveles de servicio de base de datos SQL de Azure (ediciones)][sqltiers]
 - [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure][sqltiersperflevels]
 - [Cuotas de unidades de rendimiento de base de datos (DTU)][sqlDTU]
 - [Límites de recursos de base de datos de SQL][sqldblimits]

## Límites de Servicios multimedia

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## Límites de Bus de servicio

[AZURE.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## Límites de Active Directory

Para Azure Active Directory (AD), consulte el tema siguiente:

 - [Restricciones y límites del servicio Azure Active Directory][adlimitsandrestrictions]

## Otras referencias

[Concepto de límites de Azure y aumento de los mismos][azurelimitsblogpost]

[Tamaños de máquinas virtuales y servicios en la nube de Azure][vmsizes]

[customersupportfaq]: http://azure.microsoft.com/support/faq/
[azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[de red virtual]: http://msdn.microsoft.com/library/azure/jj156210.aspx 
[storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
[georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
[sqldblimits]: http://msdn.microsoft.com/library/azure/dn338081.aspx
[sqltiers]: http://msdn.microsoft.com/library/azure/dn741340.aspx
[sqltiersperflevels]: http://msdn.microsoft.com/library/azure/dn741336.aspx
[sqlDTU]: http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs
[vmsizes]: http://msdn.microsoft.com/library/azure/dn197896.aspx
[georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/library/azure/dn764971.aspx

<!--HONumber=47-->
