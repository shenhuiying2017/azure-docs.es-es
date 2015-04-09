<properties 
	pageTitle="Introducción a Azure Site Recovery" 
	description="Implemente Azure Site Recovery para proteger las máquinas virtuales locales en otro sitio local o en Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Introducción a Azure Site Recovery

<div class="dev-callout"> 
<p>Azure Site Recovery organiza la replicación y conmutación por error en una serie de escenarios:</p>


<ul>
<li>**Protección de sitio de Hyper-V local a Azure con replicación de Hyper-V**: organice la replicación, la conmutación por error y la recuperación desde un sitio local con uno o más servidores de Hyper-V, pero sin System Center VMM. Los datos de máquinas virtuales se replican desde un servidor host de Hyper-V de origen a Azure. Lea <a href="http://go.microsoft.com/fwlink/?LinkId=522298">Introducción a Azure Site Recovery: Protección entre sitio de Hyper-V local y Azure con replicación de Hyper-V</a>.</li>
<li>**Protección de sitio de VMM local a sitio de VMM local con replicación de Hyper-V**: organice la replicación, la conmutación por error y la recuperación entre sitios de VMM locales. Los datos de máquinas virtuales se replican desde un servidor host de Hyper-V de origen hasta un servidor host de destino. Lea <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introducción a Azure Site Recovery: Protección entre dos sitios de VMM locales con replicación de Hyper-V</a>.</li>

<li>**Protección de sitio de VMM local a sitio de VMM local con replicación de SAN**: organiza la replicación de extremo a extremo, la conmutación por error y la recuperación mediante replicación basada en la matriz de almacenamiento entre los dispositivos de SAN que alojan datos de máquinas virtuales en sitios locales de origen y de destino. Lea <a href="http://go.microsoft.com/fwlink/?LinkId=518683">Introducción a Azure Site Recovery: : Protección entre dos sitios de VMM locales con replicación de SAN</a>.</li>

<li>**Protección de sitio de VMM local a Azure**: organice la replicación, la conmutación por error y la recuperación entre un sitio de VMM local y Azure. Los datos de máquinas virtuales replicados se almacenan en el almacenamiento de Azure. Lea <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Introducción a Azure Site Recovery: Protección entre sitio de VMM local y Azure.</a></li>

<li>**Sitio de VMWare local a sitio de VMWare local con InMage**: InMage Scout es una adquisición reciente de Microsoft que proporciona replicación en tiempo real entre sitios de VMWare locales. Ahora InMage está disponible como un producto independiente que se obtiene a través de una suscripción al servicio de Azure Site Recovery. Lea <a href="http://go.microsoft.com/fwlink/?LinkId=518684">Introducción a Azure Site Recovery: Protección entre dos sitios de VMWare locales con InMage</a>.</li>
</ul>

<p>La matriz de características resume estos escenarios.</p>

<table border="1">
<thead>
<tr>
	<th>Característica</th><th>De local a Azure</th>
	<th>De local a local (Hyper-V)</th>
	<th>De local a local (SAN)</th>
</tr>
</thead>

<tr>
<td>Datos a Azure Site Recovery</td>
<td><p>Los metadatos de la nube se envían a Azure Site Recovery.</p> <p>Los datos replicados se almacenan en el almacenamiento de Azure.</p></td>
<td><p>Los metadatos de la nube se envían a Azure Site Recovery.</p> <p>Los datos replicados se almacenan en la ubicación especificada por el servidor de Hyper-V de destino.</p></td>
<td><p>Los metadatos de la nube se envían a Azure Site Recovery.</p> <p>Los datos replicados se almacenan en el almacenamiento de la matriz de destino.</p></td>
</tr>

<tr>
<td>Requisitos del almacén</td>
<td><p>Necesitará una cuenta de Azure para configurar un almacén de Azure Site Recovery. Consulte <a href="http://aka.ms/try-azure">Evaluación gratuita de Azure</a>. Puede obtener información sobre precios en la página <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalles de precios de Azure Site Recovery Manager</a>.</p></td>
<td><p>Cuenta de Azure con Azure Site Recovery habilitado.</p>
</td><td><p>Cuenta de Azure con Azure Site Recovery habilitado.</p></td>
</tr>

<tr>
<td>Replicación</td>
<td>Las máquinas virtuales se replican desde un servidor de Hyper-V local de origen al almacenamiento de Azure de destino. Puede configurar la replicación inversa para replicar a la ubicación de origen.</td>
<td>Las máquinas virtuales se replican desde un servidor de Hyper-V local de origen a otro. Puede configurar la replicación inversa para replicar a la ubicación de origen.</td>
<td>Las máquinas virtuales se replican desde el dispositivo de almacenamiento de SAN de origen al dispositivo de SAN de destino. Puede configurar la replicación inversa para replicar a la ubicación de origen.</td>
</tr>

<tr>
<td>Almacenamiento de la máquina virtual</td>
<td>Unidad de disco duro de la máquina virtual almacenada en el almacenamiento de Azure</td>
<td>Unidad de disco duro de la máquina virtual almacenada en el host de Hyper-V.</td>
<td>Disco duro de máquina virtual almacenado en la matriz de almacenamiento de SAN.</td>
</tr>

<tr>
<td>Almacenamiento de Azure</td>
<td><p>Se requiere una cuenta de almacenamiento de Azure</p> <p>La cuenta debe tener habilitada la replicación geográfica, estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.</p></td>
<td>No aplicable</td>
<td>No aplicable</td>
</tr>

<tr>
<td>Matriz de almacenamiento de SAN</td>
<td><p>No aplicable</p></td>
<td>No aplicable</td>
<td>La matriz de almacenamiento de SAN debe estar disponible en los sitios de origen y de destino y administrada por VMM. Consulte los <a href="http://go.microsoft.com/fwlink/?LinkId=518685">requisitos previos de implementación</a> para obtener más información. </td>
</tr>

<tr>
<td>Servidor VMM</td>
<td>Requiere solo un servidor VMM en el sitio de origen únicamente. El servidor VMM debe tener al menos una nube que contiene al menos un servidor de host o clúster de Hyper-V.</td>
<td>Requiere servidores VMM de origen y destino con al menos una nube en cada uno, o un solo servidor VMM con dos nubes. Las nubes deben contener al menos un servidor de host o clúster de Hyper-V.</td>
<td>Requiere servidores VMM de origen y destino con al menos una nube en cada uno. Las nubes deben contener al menos un clúster de Hyper-V.</td>
</tr>

<tr>
<td>Versión de System Center de VMM</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 con SP1</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 con VMM Update Rollup 5.0</p></td>
</tr>

<tr>
<td>Configuración de VMM</td>
<td><p>Configurar nubes en sitios de origen y de destino</p><p>Configurar redes de VM en sitio de origen y de destino</p><p>Configurar clasificaciones de almacenamiento en sitios de origen y de destino <p>Instalar el proveedor en servidores VMM de origen y de destino</p></td>
<td><p>Configurar nubes en sitio de origen</p><p>Configurar almacenamiento de SAN</p><p>Configurar redes de VM en sitio de origen</p><p>Instalar el proveedor en servidor VMM de origen</p><p>Enable virtual machine protection</p></td>
<td><p>Configurar nubes en sitios de origen y de destino</p><p>Configurar redes de VM en sitios de origen y de destino</p><p>Instalar el proveedor en servidor VMM de origen y de destino</p><p>Enable virtual machine protection</p></td>
</tr>

<tr>
<td>Proveedor de Azure Site Recovery</td>
<td>Instalar en servidor VMM de origen</td>
<td>Instalar en servidores VMM de origen y destino</td>
<td>Instalar en servidores VMM de origen y destino</td>
</tr>

<tr>
<td>Agente de los Servicios de recuperación de Azure</td>
<td>Instalar en servidores host de Hyper-V situados en nubes VMM</td>
<td>No se requiere</td>
<td>No se requiere</td>
</tr>

<tr>
<td>Puntos de recuperación de la máquina virtual</td>
<td><p>Establecer los puntos de recuperación por hora.</p> <p>Especifica cuánto tiempo se debe mantener un punto de recuperación (de 0 a 24 horas). Los puntos de recuperación se crean mediante la fórmula 90/copy_frequency durante la primera y luego una vez por hora.</p></td>
<td><p>Establecer los puntos de recuperación por cantidad.</p> <p>Especifica cuántos puntos de recuperación adicionales se deben mantener (de 0 a 15). De forma predeterminada, se crea un punto de recuperación cada hora. Cuando se establece en cero, solo el último punto de recuperación se almacena en el servidor host de Hyper-V de réplica.</p></td>
<td>Configurado en configuración de matriz de almacenamiento.</td>
</tr>

<tr>
<td>Asignación de red</td>
<td><p>Asignar redes VM a redes Azure.</p> <p>La asignación de red garantiza que todas las máquinas virtuales que conmutan por error en la misma red VM de origen se pueden conectar después. Además, si hay una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se podrán conectar a las máquinas virtuales locales. </p><p>Si la asignación no está habilitada, solo las máquinas virtuales que conmutan por error en el mismo plan de recuperación se pueden conectar entre sí después a Azure.</p></td>
<td><p>Asignar redes VM de origen a redes VM de destino.</p> <p>La asignación de red se usa para colocar las máquinas virtuales replicadas en servidores host de Hyper-V óptimos, y garantiza que las máquinas virtuales asociadas a la red VM de origen estén asociadas a la red de destino asignada después de la conmutación por error. </p><p>Si la asignación no está habilitada, las máquinas virtuales replicadas no se conectarán a una red.</p></td>
<td><p>Asignar redes VM de origen a redes VM de destino.</p> <p>La asignación de red garantiza que las máquinas virtuales asociadas a la red VM de origen estén asociadas a la red de destino asignada después de la conmutación por error. </p><p>Si la asignación no está habilitada, las máquinas virtuales replicadas no se conectarán a una red.</p></td>
</tr>

<tr>
<td>Asignación de almacenamiento</td>
<td>No aplicable</td>
<td><p>Asigna las clasificaciones de almacenamiento en los servidores VMM de origen a las clasificaciones de almacenamiento en los servidores VMM de destino.</p> <p>Con la asignación habilitada, los discos duros de las máquinas virtuales en la clasificación de almacenamiento de origen estarán ubicados en la clasificación de almacenamiento de destino después de la conmutación por error.</p><p>Si la asignación de almacenamiento no está habilitada, los discos duros virtuales replicados se almacenarán en la ubicación predeterminada en el servidor host de Hyper-V de destino.</p></td>
<td><p>Asignaciones entre matrices de almacenamiento y grupos en sitios principales y secundarios.</p></td>
</tr>

</table>

<p>Si tiene preguntas, visite el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</p> 

<!--HONumber=49-->