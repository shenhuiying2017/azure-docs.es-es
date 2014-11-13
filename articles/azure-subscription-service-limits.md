<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="L&iacute;mites, cuotas y restricciones de suscripci&oacute;n y servicios de Microsoft Azure" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Proporciona una lista de l&iacute;mites habituales de suscripci&oacute;n y servicios de Azure junto con los valores m&aacute;ximos." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure

En el siguiente documento se especifican algunos de los límites más comunes de Microsoft Azure. Tenga en cuenta que, actualmente, este documento no cubre todos los servicios de Azure. Con el tiempo, estos límites se expandirán y actualizarán para abarcar más de la plataforma.

-   [Límites de suscripción][Límites de suscripción]
-   [Límites de Servicios en la nube][Límites de Servicios en la nube]
-   [Límites de máquinas virtuales][Límites de máquinas virtuales]
-   [Límites de Sitios web][Límites de Sitios web]
-   [Límites de red][Límites de red]
-   [Límites de almacenamiento][Límites de almacenamiento]
-   [Límites de Base de datos de documentos (vista previa)][Límites de Base de datos de documentos (vista previa)]
-   [Límites de Base de datos SQL][Límites de Base de datos SQL]
-   [Límites de Servicios multimedia][Límites de Servicios multimedia]
-   [Límites de Bus de servicio][Límites de Bus de servicio]

> [WACOM.NOTE] Si desea aumentar el límite por encima del **Límite predeterminado**, [abra una solicitud de servicio al cliente en línea sin cargo alguno][abra una solicitud de servicio al cliente en línea sin cargo alguno]. Los límites no se pueden subir por encima del valor de **Límite máximo** que figura en las tablas siguientes. Si la columna **Límite máximo** no existe, el recurso especificado no tiene límites ajustables.

## <a name="subscription"></a>Límites de suscripción

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Límite predeterminado</th>
<th align="left">Límite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Núcleos por <a href="http://msdn.microsoft.com/es-es/library/azure/hh531793.aspx">subscripción</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/es-es/library/azure/gg456328.aspx">Coadministradores</a> por suscripción</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/es-es/documentation/articles/storage-whatis-account/">Cuentas de almacenamiento</a> por suscripción</p></td>
<td align="left"><p>100</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/es-es/documentation/articles/cloud-services-what-is/">Servicios en la nube</a> por suscripción</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Redes virtuales</a> por suscripción<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/es-es/library/jj157100.aspx">Redes locales</a> por suscripción</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Bases de datos SQL por suscripción</p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Servidores lógicos de bases de datos SQL por suscripción</p></td>
<td align="left"><p>6</p></td>
<td align="left"><p>150</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Servidores DNS por suscripción</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>Direcciones IP reservadas por suscripción</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Certificados de servicio hospedados por suscripción</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/es-es/library/azure/jj156085.aspx">Grupos de afinidad</a> por suscripción</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Recuento de instancias extrapequeñas como un núcleo hacia el límite de núcleos a pesar de utilizar un núcleo parcial.

<sup>2</sup>Cada red virtual admite una sola puerta de enlace de red virtual.

## <a name="webworkerlimits"></a>Límites de servicios en la nube

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Límite predeterminado</th>
<th align="left">Límite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/es-es/documentation/articles/cloud-services-what-is/">Roles web y de trabajo por implementación<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/es-es/library/gg557552.aspx#InstanceInputEndpoint">Extremos de entrada de instancia</a> por implementación</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/es-es/library/gg557552.aspx#InputEndpoint">Extremos de entrada</a> por implementación</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/es-es/library/gg557552.aspx#InternalEndpoint">Extremos internos</a> por implementación</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Cada servicio en la nube con roles web y de trabajo puede tener dos implementaciones, una para producción y otra para ensayo. Tenga en cuenta también que este límite hace referencia al número de roles (configuración) y no al número de instancias por rol (escalado).

## <a name="vmlimits"></a>Límites de máquinas virtuales

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Límite predeterminado</th>
<th align="left">Límite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/es-es/documentation/services/virtual-machines/">Máquinas virtuales</a> por servicio en la nube<sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Extremos de entrada por servicio en la nube<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Cuando crea una máquina virtual, se crea automáticamente un servicio en la nube para dar cabida a esa máquina. Después puede agregar varias máquinas virtuales en ese mismo servicio en la nube.

<sup>2</sup>Los extremos de entrada se usan para permitir la comunicación con las máquinas virtuales externas al servicio en la nube contenedor. Las máquinas virtuales dentro del mismo servicio en la nube permiten la comunicación automáticamente entre todos los puertos UDP y TCP para comunicación interna.

## <a name="websiteslimits"></a>Límites de Sitios web

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## <a name="networkinglimits"></a>Límites de red

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Límite predeterminado</th>
<th align="left">Límite máximo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Máquinas en total <sup>1</sup> por <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">red virtual</a><sup>2</sup></p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>Conexiones TCP concurrentes para una máquina virtual o instancia de rol</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Listas de control de acceso (ACL) por extremo<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Sitios de red local por red virtual</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>El número total de máquinas incluye las máquinas virtuales y las instancias de rol de trabajo y web.

<sup>2</sup>Cada red virtual admite una sola [puerta de enlace de red virtual][puerta de enlace de red virtual].

<sup>3</sup>ACL se admite en extremos de entrada para máquinas virtuales. Para roles de trabajo y web, se admite en extremos de entrada y de entrada de instancia.

## <a name="storagelimits"></a>Límites de almacenamiento

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso<sup>1</sup></th>
<th align="left">Límite predeterminado</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>TB por cuenta de almacenamiento</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="even">
<td align="left"><p>Tamaño máximo de un contenedor de blobs, una tabla o una cola</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento</p></td>
<td align="left"><p>El único límite es los 500 TB de capacidad de la cuenta de almacenamiento</p></td>
</tr>
<tr class="even">
<td align="left"><p>Tamaño máximo de un recurso compartido de archivos</p></td>
<td align="left"><p>5 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Número máximo de archivos en un recurso compartido de archivos</p></td>
<td align="left"><p>El único límite es la capacidad total de 5 TB del recurso compartido de archivos</p></td>
</tr>
<tr class="even">
<td align="left"><p>Máximo de 8 KB IOPS por disco persistente (nivel Básico)</p></td>
<td align="left"><p>300<sup>2</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Máximo de 8 KB IOPS por disco persistente (nivel Estándar)</p></td>
<td align="left"><p>500<sup>2</sup></p></td>
</tr>
<tr class="even">
<td align="left"><p>Velocidad de solicitudes (suponiendo un tamaño de objeto de 1 KB) por cuenta de almacenamiento</p></td>
<td align="left"><p>Hasta 20.000 entidades o mensajes por segundo</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Rendimiento de un blob</p></td>
<td align="left"><p>Hasta 60 MB por segundo o hasta 500 solicitudes por segundo</p></td>
</tr>
<tr class="even">
<td align="left"><p>Rendimiento de una cola (mensajes de 1 KB)</p></td>
<td align="left"><p>Hasta 2000 mensajes por segundo</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Rendimiento de una partición de tabla (entidades de 1 KB)</p></td>
<td align="left"><p>Hasta 2000 entidades por segundo</p></td>
</tr>
<tr class="even">
<td align="left"><p>Entrada máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
<td align="left"><p>10 Gbps si GRS<sup>3</sup> está habilitado, 20 Gbps para LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Salida máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
<td align="left"><p>20 Gbps si GRS<sup>3</sup> está habilitado, 30 Gbps para LRS</p></td>
</tr>
<tr class="even">
<td align="left"><p>Entrada máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
<td align="left"><p>5 Gbps si GRS<sup>3</sup> está habilitado, 10 Gbps para LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Salida máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
<td align="left"><p>10 Gbps si GRS<sup>3</sup> está habilitado, 15 Gbps para LRS</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Para obtener más detalles sobre estos límites, vea [Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure][Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure].

<sup>2</sup>Para máquinas virtuales del nivel Básico, no ponga más de 66 VHD de uso intensivo en una cuenta de almacenamiento para evitar el límite de velocidad de solicitudes de 20.000 (20.000/300). Para máquinas virtuales del nivel Estándar, no ponga más de 40 VHD de uso intensivo en una cuenta de almacenamiento (20.000/500). Para obtener más información, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure][Tamaños de máquinas virtuales y servicios en la nube de Azure].

<sup>3</sup>GRS es [almacenamiento con redundancia geográfica][almacenamiento con redundancia geográfica]. LRS es [almacenamiento con redundancia local][almacenamiento con redundancia local]. Tenga en cuenta que GRS incluye también redundancia local.

## <a name="documentdblimits"></a>Límites de Base de datos de documentos (vista previa)

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## <a name="sqldblimits"></a>Límites de Base de datos SQL

Para los límites de Base de datos SQL, vea los temas siguientes:

-   [Niveles de servicio de base de datos SQL de Azure (ediciones)][Niveles de servicio de base de datos SQL de Azure (ediciones)]
-   [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure][Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure]
-   [Cuotas de unidades de rendimiento de base de datos (DTU)][Cuotas de unidades de rendimiento de base de datos (DTU)]
-   [Límites de recursos de Base de datos SQL][Límites de recursos de Base de datos SQL]

## <a name="mediaserviceslimits"></a>Límites de Servicios multimedia

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## <a name="servicebuslimits"></a>Límites de Bus de servicio

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## <a name="seealso"></a>Otras referencias

[Concepto de límites de Azure y aumento de los mismos][abra una solicitud de servicio al cliente en línea sin cargo alguno]

[Tamaños de máquinas virtuales y servicios en la nube de Azure][Tamaños de máquinas virtuales y servicios en la nube de Azure]

  [Límites de suscripción]: #subscription
  [Límites de Servicios en la nube]: #webworkerlimits
  [Límites de máquinas virtuales]: #vmlimits
  [Límites de Sitios web]: #websiteslimits
  [Límites de red]: #networkinglimits
  [Límites de almacenamiento]: #storagelimits
  [Límites de Base de datos de documentos (vista previa)]: #documentdblimits
  [Límites de Base de datos SQL]: #sqldblimits
  [Límites de Servicios multimedia]: #mediaserviceslimits
  [Límites de Bus de servicio]: #servicebuslimits
  [abra una solicitud de servicio al cliente en línea sin cargo alguno]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [puerta de enlace de red virtual]: http://msdn.microsoft.com/es-es/library/azure/jj156210.aspx
  [Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Tamaños de máquinas virtuales y servicios en la nube de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn197896.aspx
  [almacenamiento con redundancia geográfica]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [almacenamiento con redundancia local]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [Niveles de servicio de base de datos SQL de Azure (ediciones)]: http://msdn.microsoft.com/es-es/library/azure/dn741340.aspx
  [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn741336.aspx
  [Cuotas de unidades de rendimiento de base de datos (DTU)]: http://msdn.microsoft.com/es-es/library/azure/ee336245.aspx#DTUs
  [Límites de recursos de Base de datos SQL]: http://msdn.microsoft.com/es-es/library/azure/dn338081.aspx
