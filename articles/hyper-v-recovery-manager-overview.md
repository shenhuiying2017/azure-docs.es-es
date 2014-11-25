<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introducción a Azure Site Recovery

<div class="dev-callout">

Implemente Azure Site Recovery para proteger las máquinas virtuales que se ejecutan en servidores host de Hyper-V ubicados en nubes de System Center Virtual Machine Manager (VMM). Puede hacerlo de dos maneras:

<ul>
<li><b>Protección de local a local</b>—Replique las máquinas virtuales de un sitio local a otro. Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Los datos de máquinas virtuales se replican desde un servidor host de Hyper-V de origen hasta un servidor host de destino. Azure Site Recovery coordina el proceso. Se puede encontrar un tutorial para este escenario en <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introducción a Azure Site Recovery: protección de local a local][Introducción a Azure Site Recovery: protección de local a local</a>.</li>
<li><b>Replique las máquinas virtuales de un sitio local a Microsoft Azure</b>—Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Azure Site Recovery coordina el proceso y la máquina virtual replicada se almacena en el almacenamiento de Azure. Se puede encontrar un tutorial para este escenario en <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Introducción a Azure Site Recovery: protección de local a Azure</a>.</li></ul>

En la siguiente tabla se resumen y comparan las dos opciones de implementación.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Característica</th>
<th align="left">De local a Azure</th>
<th align="left">De local a local</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Datos a Azure Site Recovery</td>
<td align="left">Los metadatos de la nube se envían a Azure Site Recovery. Los datos replicados se almacenan en el almacenamiento de Azure.</td>
<td align="left">Los metadatos de la nube se envían a Azure Site Recovery. Los datos replicados se almacenan en el servidor host de Hyper-V de destino.</td>
</tr>
<tr class="even">
<td align="left">Requisitos del almacén</td>
<td align="left"><p>El almacén requiere un certificado que cumpla con los <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">requisitos del almacén</a>.</p>
<p>Una clave de almacén se genera automáticamente. La clave garantiza la integridad del tráfico entre el proveedor de Azure Site Recovery en el servidor VMM y Azure Site Recovery.</p></td>
<td align="left"><p>El almacén requiere un certificado que cumpla con los <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">requisitos del almacén</a>.</p>
<p>Una clave de almacén se genera automáticamente. La clave garantiza la integridad del tráfico entre el proveedor de Azure Site Recovery en el servidor VMM y Azure Site Recovery.</p></td>
</tr>
<tr class="odd">
<td align="left">Replicación</td>
<td align="left">Las máquinas virtuales se replican desde un servidor de Hyper-V local al almacenamiento de Azure.</td>
<td align="left">Las máquinas virtuales se replican desde un servidor de Hyper-V local a otro.</td>
</tr>
<tr class="even">
<td align="left">Almacenamiento de la máquina virtual</td>
<td align="left">Unidad de disco duro de la máquina virtual almacenada en el almacenamiento de Azure</td>
<td align="left">Unidad de disco duro de la máquina virtual almacenada en el host de Hyper-V.</td>
</tr>
<tr class="odd">
<td align="left">Almacenamiento de Azure</td>
<td align="left"><p>Se requiere una cuenta de almacenamiento de Azure</p>
<p>La cuenta debe tener habilitada la replicación geográfica, estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.</p></td>
<td align="left">No aplicable</td>
</tr>
<tr class="even">
<td align="left">Servidor VMM</td>
<td align="left">Requiere únicamente un servidor VMM de origen</td>
<td align="left">Requiere servidores VMM de origen y destino con al menos una nube en cada uno, o un solo servidor VMM con dos nubes</td>
</tr>
<tr class="odd">
<td align="left">Versión de System Center del servidor VMM</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 con SP1</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Proveedor de Azure Site Recovery</td>
<td align="left">Instalar en servidor VMM de origen</td>
<td align="left">Instalar en servidores VMM de origen y destino</td>
</tr>
<tr class="odd">
<td align="left">Agente de los Servicios de recuperación de Azure</td>
<td align="left">Instalar en servidores host de Hyper-V situados en nubes VMM</td>
<td align="left">No se requiere</td>
</tr>
<tr class="even">
<td align="left">Puntos de recuperación de la máquina virtual</td>
<td align="left"><p>Establecer los puntos de recuperación por hora.</p>
<p>Especifica cuánto tiempo se debe mantener un punto de recuperación (de 0 a 24 horas). Los puntos de recuperación se crean mediante la fórmula 90/copy_frequency durante la primera y luego una vez por hora.</p></td>
<td align="left"><p>Establecer los puntos de recuperación por cantidad.</p>
<p>Especifica cuántos puntos de recuperación adicionales se deben mantener (de 0 a 15). De forma predeterminada, se crea un punto de recuperación cada hora. Cuando se establece en cero, solo el último punto de recuperación se almacena en el servidor host de Hyper-V de réplica.</p></td>
</tr>
<tr class="odd">
<td align="left">Asignación de red</td>
<td align="left"><p>Asignar redes VM a redes Azure.</p>
<p>La asignación de red garantiza que todas las máquinas virtuales que conmutan por error en la misma red VM de origen se pueden conectar después. Además, si hay una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se podrán conectar a las máquinas virtuales locales.</p>
<p>Si la asignación no está habilitada, solo las máquinas virtuales que conmutan por error en el mismo plan de recuperación se pueden conectar entre sí después a Azure.</p></td>
<td align="left"><p>Asignar redes VM de origen a redes VM de destino.</p>
<p>La asignación de red se usa para colocar las máquinas virtuales replicadas en servidores host de Hyper-V óptimos, y garantiza que las máquinas virtuales asociadas a la red VM de origen estén asociadas a la red de destino asignada después de la conmutación por error.</p>
<p>Si la asignación no está habilitada, las máquinas virtuales replicadas no se conectarán a una red.</p></td>
</tr>
<tr class="even">
<td align="left">Asignación de almacenamiento</td>
<td align="left">No aplicable</td>
<td align="left"><p>Asigna las clasificaciones de almacenamiento en los servidores VMM de origen a las clasificaciones de almacenamiento en los servidores VMM de destino.</p>
<p>Con la asignación habilitada, los discos duros de las máquinas virtuales en la clasificación de almacenamiento de origen estarán ubicados en la clasificación de almacenamiento de destino después de la conmutación por error.</p>
<p>Si la asignación de almacenamiento no está habilitada, los discos duros virtuales replicados se almacenarán en la ubicación predeterminada en el servidor host de Hyper-V de destino.</p></td>
</tr>
</tbody>
</table>

Si tiene preguntas, visite el [foro de Servicios de recuperación de Azure][foro de Servicios de recuperación de Azure].



  [foro de Servicios de recuperación de Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
