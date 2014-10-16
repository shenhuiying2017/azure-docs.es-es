<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Suscripción de Azure y límites de servicio, cuotas y restricciones

En el siguiente documento se especifican algunos de los límites más comunes de Microsoft Azure. Tenga en cuenta que, actualmente, este documento no cubre todos los servicios de Azure. Con el tiempo, estos límites se expandirán y actualizarán para abarcar más de la plataforma.

-   [Límites de suscripción][]
-   [Límites web y de trabajo][]
-   [Límites de máquinas virtuales][]
-   [Límites de red][]
-   [Límites de almacenamiento][]
-   [Límites de Base de datos SQL][]

> [WACOM.NOTE] Si desea subir el límite por encima del valor de **Límite predeterminado**, abra un incidente con [asistencia al cliente][]. Los límites no se pueden subir por encima del valor de **Límite máximo** que figura en las tablas siguientes. Si la columna **Límite máximo** no existe, el recurso especificado no tiene límites ajustables.

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
<td align="left"><p>Núcleos por <a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">subscripción</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">Coadministradores</a> por suscripción</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/es-es/documentation/articles/storage-whatis-account/">Cuentas de almacenamiento</a> por suscripción</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>50</p></td>
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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">Redes locales</a> por suscripción</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">Grupos de afinidad</a> por suscripción</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Recuento de instancias extrapequeñas como un núcleo hacia el límite de núcleos a pesar de utilizar un núcleo parcial.

<sup>2</sup>Cada red virtual admite una sola puerta de enlace de red virtual.

## <a name="webworkerlimits"></a>Límites web y de trabajo

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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">Extremos de entrada de instancia</a> por implementación</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">Extremos de entrada</a> por implementación</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">Extremos internos</a> por implementación</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Cada servicio en la nube con roles web y de trabajo puede tener dos implementaciones, una para producción y otra para ensayo.

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

<sup>2</sup>Cada red virtual admite una sola [puerta de enlace de red virtual][].

<sup>3</sup>ACL se admite en extremos de entrada para máquinas virtuales. Para roles de trabajo y web, se admite en extremos de entrada y de entrada de instancia.

## <a name="storagelimits"></a>Límites de almacenamiento<sup>1</sup>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">Límite predeterminado</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>TB por cuenta de almacenamiento<sup>2</sup></p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>IOPS máximo por disco permanente</p></td>
<td align="left"><p>500<sup>3</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>IOPS máximo por cuenta de almacenamiento</p></td>
<td align="left"><p>20,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>Entrada máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
<td align="left"><p>10 Gbps si GRS<sup>4</sup> se habilita, 20 Gbps si se deshabilita</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Entrada máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
<td align="left"><p>5 Gbps si GRS<sup>4</sup> se habilita, 10 Gbps si se deshabilita</p></td>
</tr>
<tr class="even">
<td align="left"><p>Salida máxima por cuenta de almacenamiento (regiones de EE. UU.)</p></td>
<td align="left"><p>20 Gbps si GRS<sup>4</sup> se habilita, 30 Gbps si se deshabilita</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Salida máxima por cuenta de almacenamiento (regiones de Europa y Asia)</p></td>
<td align="left"><p>10 Gbps si GRS<sup>4</sup> se habilita, 15 Gbps si se deshabilita</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Para obtener más detalles sobre estos límites, vea [Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure][].

<sup>2</sup>Para blobs en páginas solamente las páginas que están en uso acumulan capacidad de uso. Por ejemplo, por una máquina virtual con un VHD de 127 GB de los que el SO solamente usa 30 GB, solamente se cobra por la parte de 30 GB usada dentro del VHD, no por todos los 127 GB.

<sup>3</sup>No ponga más de 40 VHD con mucho uso en una cuenta para evitar el límite de 20.000 IOPS.

<sup>4</sup>Cuenta de almacenamiento con redundancia geográfica

## <a name="sqldblimits"></a>Límites de Base de datos SQL

Para los límites de Base de datos SQL, vea los temas siguientes:

-   [Niveles de servicio de base de datos SQL de Azure (ediciones)][]
-   [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure][]
-   [Límites de recursos de Base de datos SQL][]

## <a name="seealso"></a>Otras referencias

[Tamaños de máquinas virtuales y servicios en la nube de Azure][]

[Objetivos de escalabilidad y rendimiento del almacenamiento de Azure][Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure]

[Niveles de servicio de base de datos SQL de Azure (ediciones)][]

[Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure][]

[Límites de recursos de Base de datos SQL][]

  [Límites de suscripción]: #subscription
  [Límites web y de trabajo]: #webworkerlimits
  [Límites de máquinas virtuales]: #vmlimits
  [Límites de red]: #networkinglimits
  [Límites de almacenamiento]: #storagelimits
  [Límites de Base de datos SQL]: #sqldblimits
  [asistencia al cliente]: http://azure.microsoft.com/en-us/support/faq/
  [subscripción]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [Coadministradores]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [Cuentas de almacenamiento]: http://azure.microsoft.com/es-es/documentation/articles/storage-whatis-account/
  [Servicios en la nube]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-what-is/
  [Redes virtuales]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [Redes locales]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [Grupos de afinidad]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [Extremos de entrada de instancia]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [Extremos de entrada]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [Extremos internos]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [Máquinas virtuales]: http://azure.microsoft.com/es-es/documentation/services/virtual-machines/
  [puerta de enlace de red virtual]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Niveles de servicio de base de datos SQL de Azure (ediciones)]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [Límites de recursos de Base de datos SQL]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Tamaños de máquinas virtuales y servicios en la nube de Azure]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
