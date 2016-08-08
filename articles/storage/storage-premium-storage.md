<properties
	pageTitle="Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure | Microsoft Azure"
	description="El Almacenamiento premium le ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un uso intensivo de E/S, que se ejecutan en máquinas virtuales de Azure. Las máquinas virtuales de Azure de la serie DS, DSv2 y GS admiten el Almacenamiento premium."
	services="storage"
	documentationCenter=""
	authors="aungoo-msft"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2016"
	ms.author="aungoo-msft"/>


# Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure

## Información general

El Almacenamiento premium de Azure le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales con cargas de trabajo intensivas de E/S. Discos de máquinas virtuales que usan datos de almacén de Almacenamiento premium en unidades de estado sólido (SSD). Puede migrar los discos de máquina virtual de su aplicación al Almacenamiento premium de Azure para aprovechar la velocidad y rendimiento de estos discos.

Una máquina virtual de Azure admite la conexión de varios discos de Almacenamiento premium para que las aplicaciones puedan disponer de hasta 64 TB de almacenamiento por máquina virtual. Con el Almacenamiento premium, las aplicaciones pueden tener hasta 80.000 E/S por segundo (operaciones de entrada/salida por segundo) por máquina virtual, así como un rendimiento del disco de 2.000 MB por segundo por máquina virtual, con latencias extremadamente bajas para operaciones de lectura.

Con el Almacenamiento premium, Azure ofrece la capacidad de levantar y mover realmente sus exigentes aplicaciones empresariales como Dynamics AX, Dynamics CRM, Exchange Server, granjas de servidores de SharePoint y el conjunto de aplicaciones de SAP Business, a la nube. Puede ejecutar una variedad de cargas de trabajo de bases de datos de rendimiento intensivo como SQL Server, Oracle, MongoDB, MySQL, Redis, que requieren una baja latencia y un alto rendimiento coherentes en el almacenamiento Premium.

>[AZURE.NOTE] Se recomienda migrar cualquier disco de máquina virtual que requiera un número elevado de operaciones de entrada/salida por segundo al Almacenamiento premium de Azure para mejorar el rendimiento de la aplicación. Si el disco no requiere un número elevado de operaciones de entrada/salida por segundo, puede limitar los costos mediante el mantenimiento del almacenamiento estándar, que almacena los datos de disco de máquina virtual en unidades de disco duro (HDD) en lugar de SSD.

Para empezar a usar Almacenamiento premium de Azure, visite la página [Empiece de forma gratuita](https://azure.microsoft.com/pricing/free-trial/). Para información sobre cómo migrar las máquinas virtuales existentes al Almacenamiento premium, vea [Migrar al Almacenamiento premium de Azure](storage-migration-to-premium-storage.md).

>[AZURE.NOTE] El Almacenamiento premium se admite actualmente en algunas regiones. Encontrará la lista de regiones disponibles en [Servicios por región](https://azure.microsoft.com/regions/#services) para Azure.

## Características del Almacenamiento premium

**Discos del Almacenamiento premium**: el Almacenamiento premium de Azure admite discos de máquina virtual que se pueden conectar a las máquinas virtuales de Azure de la serie DS, DSv2 o GS. Al usar el Almacenamiento premium, tendrá la opción de tres tamaños de disco: P10 (128 GiB), P20 (512 GiB) y P30 (1024 GiB), cada uno con sus propias especificaciones de rendimiento. Según los requisitos de la aplicación, puede conectar uno o varios de estos discos a una máquina virtual de la serie DS, DSv2 o GS. En la sección siguiente, [Objetivos de escalabilidad y rendimiento del Almacenamiento premium](#premium-storage-scalability-and-performance-targets), describiremos las especificaciones con más detalle.

**Blob en páginas premium**: el Almacenamiento premium admite blobs en páginas de Azure, que se usan para contener discos persistentes para máquinas virtuales de Azure (VM). Actualmente, el Almacenamiento premium no admite blobs en bloques de Azure, blobs en anexos de Azure, archivos de Azure, tablas de Azure ni colas de Azure. Cualquier otro objeto colocado en una cuenta de Almacenamiento premium será un blob en páginas y se ajustará a uno de los tamaños aprovisionados admitidos. Por lo tanto, la cuenta de Almacenamiento premium no está pensada para el almacenamiento de blobs de tamaño muy pequeño.

**Cuenta de Almacenamiento premium**: para empezar a usar el Almacenamiento premium, debe crear una cuenta de Almacenamiento premium. Si prefiere usar el [Portal de Azure](https://portal.azure.com), puede crear una cuenta de Almacenamiento premium si especifica el nivel de rendimiento "Premium" y "almacenamiento con redundancia local (LRS)" como opción de replicación. También puede crear una cuenta de Almacenamiento premium si especifica "Premium\_LRS" como tipo con la versión 2014-02-14 o posterior de la [API de REST de almacenamiento](http://msdn.microsoft.com//library/azure/dd179355.aspx); la versión 2014-10-01 o posterior (implementaciones de la versión clásica) de la [API de REST de administración de servicios](http://msdn.microsoft.com/library/azure/ee460799.aspx); la [Referencia de la API de REST del proveedor de recursos de Almacenamiento de Azure](http://msdn.microsoft.com/library/azure/mt163683.aspx) (implementaciones de Resource Manager); y la versión 0.8.10 o posterior de [Azure PowerShell](../powershell-install-configure.md). Obtenga información acerca de los límites de la cuenta de Almacenamiento premium en la siguiente sección, en [Objetivos de escalabilidad y rendimiento del Almacenamiento premium](#premium-storage-scalability-and-performance-targets).

**Almacenamiento con redundancia local premium**: una cuenta de Almacenamiento premium solo admite almacenamiento con redundancia local (LRS) como opción de replicación y mantiene tres copias de los datos en una única región. Para consideraciones relativas a la replicación geográfica cuando se usa el Almacenamiento premium, consulte la sección [Instantáneas y Copy Blob](#snapshots-and-copy-blob) de este artículo.

Azure usa la cuenta de almacenamiento como un contenedor para el sistema operativo (SO) y los discos de datos. Cuando crea una máquina virtual de Azure de la serie DS, DSv2 o GS y selecciona una cuenta de Almacenamiento premium de Azure, los discos del sistema operativo y de datos se almacenan en dicha cuenta de almacenamiento.

Puede usar Almacenamiento premium para discos de una de las siguientes maneras:
- En primer lugar, cree una cuenta de Almacenamiento premium. Después, al crear una nueva máquina virtual DS, DSv2 o GS, seleccione la cuenta de Almacenamiento premium en las opciones de configuración del Almacenamiento. O bien,
- Cuando cree una máquina virtual DS, DSv2 o GS, cree una nueva cuenta de Almacenamiento premium en las opciones de configuración del Almacenamiento o deje que el Portal de Azure cree una predeterminada.

Para instrucciones detalladas, consulte la sección [Inicio rápido](#quick-start) más adelante en este artículo.

>[AZURE.NOTE] Una cuenta de almacenamiento premium no se puede asignar a un nombre de dominio personalizado.

## Máquinas virtuales de las series DS, DSv2 y GS

El Almacenamiento premium es compatible con las máquinas virtuales (VM) de las series DS, DSv2 y GS. Puede usar discos de Almacenamiento estándar y premium con la serie DS, DSv2 o GS de VM. Pero no puede usar discos de Almacenamiento premium con series que no sean DS o GS de VM.

Para más información sobre los tamaños y tipos de máquina virtual de Azure disponibles para Windows, consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md). Para más información sobre los tamaños y tipos de máquina virtual de Azure disponibles para Linux, consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-linux-sizes.md).

A continuación, se muestran algunas de las características de las máquinas virtuales de las series DS, DSv2 y GS:

**Servicio en la nube**: las máquinas virtuales de la serie DS se pueden agregar a un servicio en la nube que incluya solo máquinas virtuales de la serie DS. No agregue máquinas virtuales de la serie DS a un servicio en la nube existente que incluya otras que no sean de la serie DS. Puede migrar los discos duros virtuales existentes a un nuevo servicio en la nube donde solo se ejecuten máquinas virtuales de la serie DS. Si desea conservar la misma dirección IP virtual (VIP) para el nuevo servicio en la nube que hospeda las máquinas virtuales de la serie DS, use [Direcciones IP reservadas](../virtual-network/virtual-networks-instance-level-public-ip.md). Las VM de la serie GS pueden agregarse a un servicio en la nube existente ejecutando solo VM de la serie G.

**Disco del sistema operativo**: las máquinas virtuales de Azure de las series DS, DSv2 y GS se pueden configurar para usar un disco del sistema operativo (SO) hospedado en una cuenta de Almacenamiento estándar o de Almacenamiento premium. Para una mejor experiencia, se recomienda el uso de discos de SO basados en Almacenamiento premium.

**Discos de datos**: puede usar discos del Almacenamiento premium y estándar en la misma máquina virtual de la serie DS, DSv2 o GS. Con el Almacenamiento premium, puede aprovisionar una máquina virtual de la serie DS, DSv2 o GS y conectar varios discos de datos persistentes a ella. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen.

> [AZURE.NOTE] Si secciona discos de datos Almacenamiento premium usando [Espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), será necesario configurarlo con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. De forma predeterminada, la interfaz de usuario (IU) del administrador del servidor permite configurar columnas de hasta 8 discos. Pero si tiene más de 8 discos, necesitará usar PowerShell para crear el volumen y especificar manualmente el número de columnas. De lo contrario, la IU del administrador del servidor sigue usando 8 columnas aunque tenga más discos. Por ejemplo, si tiene 32 discos en un conjunto de bandas único, debe especificar 32 columnas. Puede usar el parámetro *NumberOfColumns* del cmdlet [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) de PowerShell para especificar el número de columnas que usa el disco virtual. Para obtener más información, consulte [Storage Spaces Overview](http://technet.microsoft.com/library/hh831739.aspx) (Introducción a espacios de almacenamiento) y [Storage Spaces Frequently Asked Questions](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx) (Preguntas más frecuentes sobre espacios de almacenamiento).

**Caché**: las máquinas virtuales de las series DS, DSv2 y GS cuentan con una funcionalidad de almacenamiento en caché única con la que puede obtener altos niveles de rendimiento y latencia, lo que supera el rendimiento de disco del Almacenamiento premium subyacente. Puede configurar la directiva de almacenamiento en caché de disco en los discos del Almacenamiento premium como ReadOnly, ReadWrite o None. La directiva de almacenamiento en caché de disco predeterminada es ReadOnly para todos los discos de datos premium y ReadWrite para los discos del sistema operativo. Use la opción de configuración adecuada para lograr un rendimiento óptimo para su aplicación. Por ejemplo, en el caso de los discos de datos de solo lectura o con mucha actividad de lectura, como los archivos de datos de SQL Server, establezca la directiva de almacenamiento en caché de disco en "ReadOnly". Para los discos de datos de solo escritura o con mucha actividad de escritura, como los archivos de registro de SQL Server, establezca la directiva de almacenamiento en caché de disco en "None". Consulte más información sobre cómo optimizar el diseño con el Almacenamiento premium en [Almacenamiento premium de Azure: diseño de alto rendimiento](storage-premium-storage-performance.md).

**Análisis**: para analizar el rendimiento de las máquinas virtuales con discos en cuentas de Almacenamiento premium, puede habilitar los diagnósticos de máquina virtual de Azure en el Portal de Azure. Consulte [Supervisión de máquinas virtuales de Microsoft Azure con la extensión de Diagnósticos de Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) para ver información detallada. Para ver el rendimiento de disco, use herramientas del sistema operativo como [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) para máquinas virtuales de Windows e [IOSTAT](http://linux.die.net/man/1/iostat) para máquinas virtuales de Linux.

**Rendimiento y límites de escala de máquinas virtuales**: cada tamaño de máquina virtual de las series DS, DSv2 y GS tiene límites de escala y una especificación de rendimiento para IOPS, ancho de banda y número de discos que se pueden conectar por máquina virtual. Cuando use discos del Almacenamiento premium con máquinas virtuales de la serie DS, DSv2 o GS, asegúrese de que IOPS y el ancho de banda de la máquina virtual sean suficientes para dirigir el tráfico de disco. Por ejemplo, una máquina virtual STANDARD\_DS1 tiene un ancho de banda dedicado de 32 MB por segundo disponible para el tráfico de disco de Almacenamiento premium. Un disco del Almacenamiento premium P10 puede proporcionar un ancho de banda de 100 MB por segundo. Si hay un disco del Almacenamiento premium P10 conectado a esta máquina virtual, solo puede ofrecer 32 MB por segundo, pero no llegaría a los 100 MB por segundo que puede proporcionar el disco P10.

Actualmente, la máquina virtual más grande de la serie DS es STANDARD\_DS14 y puede proporcionar hasta 512 MB por segundo en todos los discos. La VM más grande de la serie GS es STANDARD\_GS5 y puede proporcionar hasta 2000 MB por segundo en todos los discos. Tenga en cuenta que estos límites son para el tráfico de disco por sí solo, sin incluir los aciertos de caché y el tráfico de red. Hay un ancho de banda independiente disponible para el tráfico de red de máquina virtual, que es diferente del ancho de banda dedicado para discos de Almacenamiento premium.

Para consultar información más actualizada sobre los niveles máximos de IOPS y rendimiento (ancho de banda) de las máquinas virtuales de las series DS, DSv2 y GS, consulte los artículos sobre [Tamaños de máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sizes.md) o [Tamaños de máquinas virtuales de Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Para información acerca de los discos del Almacenamiento premium, sus IOPS y sus límites de rendimiento, consulte la tabla que encontrará en la sección [Objetivos de escalabilidad y rendimiento del Almacenamiento premium](#premium-storage-scalability-and-performance-targets) de este artículo.

## Objetivos de escalabilidad y rendimiento del Almacenamiento premium

En esta sección, describiremos todos los objetivos de escalabilidad y rendimiento que se deben tener en cuenta al usar el Almacenamiento premium.

### Límites de cuentas de Almacenamiento premium

Las cuentas de Almacenamiento premium tienen estos objetivos de escalabilidad:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacidad total de la cuenta</strong></td>
	<td><strong>Ancho de banda total de una cuenta de almacenamiento con redundancia local</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacidad de disco: 35 TB</li>
       <li type=round>Capacidad de instantánea: 10 TB</li>
    </ul>
	</td>
	<td>Hasta 50 gigabits por segundo de entrada y salida</td>
</tr>
</tbody>
</table>

- Entrada se refiere a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento.
- Salida se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

Para obtener más información, consulte [Objetivos de escalabilidad y rendimiento de Almacenamiento de Azure](storage-scalability-targets.md).

Si las necesidades de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación para que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento. Por ejemplo, si quiere conectar discos de 51 terabytes (TB) a diferentes máquinas virtuales, distribúyalos entre dos cuentas de almacenamiento, ya que 35 TB es el límite de una sola cuenta de Almacenamiento premium. Asegúrese de que una sola cuenta de Almacenamiento premium no tenga nunca más de 35 TB de discos aprovisionados.

### Límites de discos del Almacenamiento premium

Cuando aprovisiona un disco con una cuenta de Almacenamiento premium, la cantidad de operaciones de E/S por segundo (IOPS) y el rendimiento (ancho de banda) que se pueden obtener depende del tamaño del disco. Actualmente, hay tres tipos de discos de Almacenamiento premium: P10, P20 y P30. Cada uno tiene límites de IOPS y de rendimiento específicos, tal como se especifica en la tabla siguiente:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Tipo de disco de Almacenamiento premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Tamaño del disco</strong></td>
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>E/S por segundo por disco</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5.000</td>
</tr>
<tr>
	<td><strong>Rendimiento por disco</strong></td>
	<td>100 MB por segundo </td>
	<td>150 MB por segundo </td>
	<td>200 MB por segundo </td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Asegúrese de que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco, tal como se explica en la anterior sección [Máquinas virtuales de las series DS, DSv2 y GS](#ds-dsv2-and-gs-series-vms) de este artículo. De lo contrario, el rendimiento y las E/S por segundo del disco estarán obligados a reducir los valores basándose en los límites de la máquina virtual en vez de en los límites de disco que se mencionan en la tabla anterior.

Estos son algunos aspectos importantes que debe conocer sobre los objetivos de escalabilidad y rendimiento del Almacenamiento premium:

- **Rendimiento y capacidad aprovisionados**: cuando se aprovisiona un disco del Almacenamiento premium, a diferencia del almacenamiento estándar, se garantizan la capacidad, las IOPS y el rendimiento de ese disco. Por ejemplo, si crea un disco P30, Azure aprovisiona una capacidad de almacenamiento de 1024 GB, 5000 IOPS y un rendimiento de 200 MB por segundo para ese disco. La aplicación puede usar toda la capacidad y el rendimiento o parte de ellos.

- **Tamaño del disco**: Azure asigna el tamaño de disco (redondeado al alza) a la opción de disco del Almacenamiento premium más cercana según se especifica en la tabla. Por ejemplo, un disco de 100 GB se clasifica como una opción P10, puede realizar hasta 500 unidades de E/S por segundo y dispone de un rendimiento de hasta 100 MB por segundo. De forma similar, un disco de 400 GB se clasifica como una opción P20, puede realizar hasta 2300 unidades de E/S por segundo y dispone de un rendimiento de hasta 150 MB por segundo.

	> [AZURE.NOTE] Puede aumentar fácilmente el tamaño de los discos existentes. Por ejemplo, si desea aumentar el tamaño de un disco de 30 GB a 128 GB o 1 TB. O bien, si lo desea, convertir el disco P20 en disco P30 porque necesita más capacidad o más E/S por segundo y rendimiento. Puede expandir el disco mediante el commandlet "Update-AzureDisk" de PowerShell con la propiedad "-ResizedSizeInGB". Para realizar esta acción, se debe desconectar el disco de la máquina virtual o se debe detener la máquina virtual.

- **Tamaño de E/S**: el tamaño de la unidad de entrada y salida (E/S) es de 256 KB. Si el tamaño de los datos transferidos es inferior a 256 KB, se considera una sola unidad de E/S. Los tamaños de E/S más grandes se cuentan como varias operaciones de E/S con un tamaño de 256 KB. Por ejemplo, una E/S de 1100 KB se cuenta como cinco unidades de E/S.

- **Rendimiento**: el límite de rendimiento incluye escrituras en el disco, así como lecturas desde ese disco que no se sirven desde la memoria caché. Por ejemplo, un disco P10 tiene un rendimiento de 100 MB por segundo por disco. Algunos ejemplos de rendimiento válido para el disco P10 son:
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
<td><strong>Rendimiento máximo por disco P10</strong></td>
<td><strong>Lecturas del disco no en memoria caché</strong></td>
<td><strong>Escrituras en el disco no memoria caché</strong></td>
</tr>
<tr>
<td>100&#160;MB por segundo</td>
<td>100&#160;MB por segundo</td>
<td>0</td>
</tr>
<tr>
<td>100&#160;MB por segundo</td>
<td>0</td>
<td>100&#160;MB por segundo</td>
</tr>
<tr>
<td>100 MB por segundo </td>
<td>60&#160;MB por segundo </td>
<td>40 MB por segundo </td>
</tr>
</tbody>
</table>

- **Aciertos de caché**: los aciertos de caché no están limitados por las E/S por segundo ni el rendimiento asignados del disco. Por ejemplo, cuando se usa un disco de datos con la configuración de caché ReadOnly en una máquina virtual de la serie DS, DSv2 o GS, las lecturas que se atienden desde la memoria caché no están sujetas a los límites de disco del Almacenamiento premium. Por lo tanto, podría obtener un rendimiento muy alto desde un disco si la carga de trabajo es fundamentalmente de lectura. Tenga en cuenta que la caché depende de límites de E/S por segundo o de rendimiento independientes a nivel de la máquina virtual en función del tamaño de dicha máquina. Las máquinas virtuales de la serie DS tienen aproximadamente 4000 E/S por segundo y 33 MB/seg. por núcleo de caché y E/S de SSD locales. Las máquinas virtuales de la serie GS tienen un límite de 5000 IOPS y 50 MB/s por núcleo para las E/S de SSD local y memoria caché.

## Limitaciones
Puede que se produzca una limitación si las IOPS o el rendimiento de la aplicación superan los límites asignados a un disco del Almacenamiento premium o si el tráfico total de disco en todos los discos de la máquina virtual supera el límite de ancho de banda de disco disponible para la máquina virtual. Para evitar la limitación, recomendamos que limite el número de solicitudes de E/S pendientes para discos basándose en los objetivos de rendimiento y escalabilidad que ha aprovisionado y en el ancho de banda de disco disponible para la máquina virtual.

La aplicación puede lograr la menor latencia cuando se ha diseñado para evitar la limitación. Por otro lado, si el número de solicitudes de E/S pendientes del disco es demasiado pequeño, la aplicación no puede aprovechar al máximo los niveles de rendimiento y IOPS que están disponibles en el disco.

En los ejemplos siguientes se muestra cómo calcular los niveles de limitación. Todos los cálculos se basan en un tamaño de unidad de E/S de 256 KB.

### Ejemplo 1:
La aplicación hizo 495 unidades de E/S de 16 KB de tamaño en un segundo en un disco P10. Se contarán como 495 unidades de E/S por segundo (IOPS). Si intenta una E/S de 2 MB en el mismo segundo, el total de unidades de E/S es igual a 495 + 8. Esto es porque una E/S de 2 MB resulta en 2048 KB / 256 KB = 8 unidades de E/S cuando el tamaño de la unidad de E/S es de 256 KB. Puesto que la suma de 495 + 8 supera el límite IOPS de 500 para el disco, se produce la limitación.

### Ejemplo 2:
La aplicación hizo 400 unidades de E/S de 256 KB de tamaño en un disco P10. El ancho de banda total consumido es (400 × 256)/1024 = 100 MB/s. El rendimiento de un disco P10 es de 100 MB por segundo. Si la aplicación intenta realizar más operaciones de E/S en ese segundo, se limita porque supera el límite asignado.

### Ejemplo 3:
Tiene una máquina virtual DS4 con dos discos P30 conectados. Cada disco P30 es capaz de tener un rendimiento de 200 MB por segundo. Sin embargo, una máquina virtual DS4 tiene una capacidad de ancho de banda de disco total de 256 MB por segundo. Por lo tanto, no puede dirigir los discos conectados al máximo rendimiento en esta máquina virtual DS4 al mismo tiempo. Para resolver este problema, puede mantener el tráfico de 200 MB por segundo en un disco y 56 MB por segundo en el otro disco. Si la suma del disco tráfico supera los 256 MB por segundo, el tráfico de disco queda limitado.

>[AZURE.NOTE] Si el tráfico de disco consta principalmente de tamaños de E/S pequeños, es muy probable que la aplicación alcance el límite de IOPS antes que el límite de rendimiento. Por otro lado, si el tráfico de disco consiste principalmente en tamaños grandes de E/S, es muy probable que la aplicación alcance el límite de rendimiento en lugar del límite de IOPS. Puede aumentar la tasa de IOPS de su aplicación y la capacidad de rendimiento mediante el uso de tamaños de E/S óptimos y también mediante la limitación del número de solicitudes de E/S pendientes de un disco.

Para obtener más información sobre el diseño de alto rendimiento con el Almacenamiento premium, consulte el artículo [Almacenamiento premium de Azure: diseño de alto rendimiento](storage-premium-storage-performance.md).

## Instantáneas y Copy Blob
Puede crear una instantánea para el Almacenamiento premium de la misma que crea una instantánea al usar Almacenamiento estándar. Dado que el Almacenamiento premium solo admite almacenamiento con redundancia local (LRS) como opción de replicación, se recomienda crear instantáneas y luego copiarlas en una cuenta de Almacenamiento estándar con redundancia geográfica. Para obtener más información, consulte [Opciones de redundancia de Almacenamiento de Azure](storage-redundancy.md).

Si un disco está conectado a una máquina virtual, no se permiten determinadas operaciones de API en el blob en página que respalda el disco. Por ejemplo, no puede realizar una operación [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) en ese blob mientras el disco esté conectado a una máquina virtual. En su lugar, cree una instantánea del blob mediante el método de API de REST [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) y, luego, realice la operación [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) de la instantánea para copiar el disco conectado. Como alternativa, puede desasociar el disco y, luego, realizar las operaciones necesarias en el blob subyacente.

Los límites siguientes se aplican a las instantáneas de blob del Almacenamiento premium:
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Límite del Almacenamiento premium</strong></td>
	<td><strong>Valor</strong></td>
</tr>
<tr>
	<td>Número máximo de instantáneas por blob</td>
	<td>100</td>
</tr>
<tr>
	<td>Capacidad para instantáneas de la cuenta de almacenamiento (incluye datos solo en instantáneas, pero no los datos en el blob de base)</td>
	<td>10 TB</td>
</tr>
<tr>
	<td>Tiempo mínimo entre instantáneas consecutivas</td>
	<td>10 minutos</td>
</tr>
</tbody>
</table>

Para mantener copias con redundancia geográfica, puede copiar instantáneas desde una cuenta de almacenamiento premium a una cuenta de almacenamiento estándar con redundancia geográfica con AzCopy o Copy Blob. Para más información, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md) y [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).

Para obtener más información acerca de cómo realizar operaciones REST en blobs en página en las cuentas de Almacenamiento premium, consulte [Uso de operaciones de servicios BLOB con Almacenamiento premium de Azure](http://go.microsoft.com/fwlink/?LinkId=521969) en MSDN library.

## Uso de máquinas virtuales Linux con Almacenamiento premium
Consulte a continuación instrucciones importantes para configurar las máquinas virtuales Linux en Almacenamiento premium:

- Para todos los discos de Almacenamiento premium con la configuración de caché como “ReadOnly” o “None”, debe deshabilitar “barreras” al montar el sistema de archivos a fin de lograr los objetivos de escalabilidad para Almacenamiento Premium. No es necesita barreras para este escenario porque las escrituras en los discos de Almacenamiento premium de copia de seguridad son duraderas para esta configuración de caché. Cuando la solicitud de escritura se completa correctamente, los datos están escritos en el almacenamiento permanente. Utilice los métodos siguientes para deshabilitar “barreras” en función del sistema de archivos:
	- Si utiliza **reiserFS**, deshabilite las barreras mediante la opción de montaje “barrier=none” (para habilitar las barreras, use “barrier=flush”)
	- Si utiliza **ext3/ext4**, deshabilite las barreras mediante la opción de montaje “barrier=0” (para habilitar las barreras, use “barrier=1”)
	- Si utiliza **XFS**, deshabilite las barreras mediante la opción de montaje “nobarrier” (para habilitar las barreras, use la opción “barrier”)

- Para discos de Almacenamiento premium con caché de configuración "ReadWrite", deben habilitarse las barreras para la durabilidad de las escrituras.
- Para que las etiquetas de volumen persistan después de reiniciar la VM, debe actualizar/etc/fstab con las referencias UUID a los discos. Consulte también [Acoplamiento de un disco de datos a una máquina virtual de Linux](../virtual-machines/virtual-machines-linux-classic-attach-disk.md).

Las siguientes son las distribuciones de Linux que se validan con Almacenamiento premium. Se recomienda actualizar las máquinas virtuales al menos a una de estas versiones (o posteriores) para mejorar el rendimiento y la estabilidad con Almacenamiento premium. Además, algunas de las versiones requieren el LIS más reciente (Linux Integration Services v4.0 para Microsoft Azure). Siga el vínculo proporcionado para su descarga e instalación. Seguiremos agregando más imágenes a la lista según vayamos completando validaciones adicionales. Tenga en cuenta que nuestras validaciones demostraron que el rendimiento varía para estas imágenes y también depende de las características de carga de trabajo y la configuración de las imágenes. Se optimizan diferentes imágenes para distintos tipos de cargas de trabajo.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Distribución</strong></td>
	<td><strong>Versión</strong></td>
	<td><strong>Kernel compatible</strong></td>
	<td><strong>Detalles</strong></td>
</tr>
<tr>
	<td rowspan="2"><strong>Ubuntu</strong></td>
	<td>12.04</td>
	<td>3.2.0-75.110+</td>
	<td>Ubuntu-12_04_5-LTS-amd64-server-20150119-es-ES-30GB</td>
</tr>
<tr>
	<td>14.04+</td>
	<td>3.13.0-44.73+</td>
	<td>Ubuntu-14_04_1-LTS-amd64-server-20150123-es-ES-30GB</td>
</tr>
<tr>
	<td><strong>Debian</strong></td>
	<td>7.x, 8.x</td>
	<td>3.16.7-ckt4-1+</td>
    <td> </td>
</tr>
<tr>
	<td rowspan="2"><strong>SUSE</strong></td>
	<td>SLES 12</td>
	<td>3.12.36-38.1+</td>
	<td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
	<td>SLES 11 SP4</td>
    <td>3.0.101-0.63.1+</td>
    <td> </td>
</tr>
<tr>
	<td><strong>CoreOS</strong></td>
	<td>584.0.0+</td>
	<td>3.18.4+</td>
	<td>CoreOS 584.0.0</td>
</tr>
<tr>
	<td rowspan="2"><strong>CentOS</strong></td>
	<td>6.5, 6.6, 6.7, 7.0</td>
	<td></td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 requerido </a> <br/>
		*Vea la nota siguiente*
	</td>
</tr>
<tr>
	<td>7.1+</td>
	<td>3.10.0-229.1.2.el7+</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 recomendado </a> <br/>
		*Vea la nota siguiente*
	</td>
</tr>
<tr>
	<td><strong>RHEL</strong></td>
	<td>6.8+, 7.2+</td>
	<td> </td>
	<td></td>
</tr>
<tr>
	<td rowspan="3"><strong>Oracle</strong></td>
    <td>6.8+, 7.2+</td>
    <td> </td>
    <td> UEK4 o RHCK </td>

</tr>
<tr>
	<td>7.0-7.1</td>
	<td> </td>
	<td>UEK4 o RHCK con <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a></td>
</tr>
<tr>
	<td>6.4-6.7</td>
	<td></td>
	<td>UEK4 o RHCK con <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a></td>
</tr>
</tbody>
</table>


### Controladores de LIS para CentOS Openlogic

Los clientes que ejecutan las máquinas virtuales de OpenLogic CentOS tienen que ejecutar el comando siguiente para instalar a los controladores más recientes:

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

A continuación, se le pedirá un reinicio para activar los nuevos controladores.

## Precios y facturación
Al usar Almacenamiento premium, se aplican las siguientes consideraciones de facturación:
- Disco de almacenamiento Premium / tamaño de Blob
- Instantáneas del Almacenamiento premium
- Transferencias de datos de salida

**Tamaño de disco o blob de Almacenamiento premium**: la facturación de un disco o blob de almacenamiento premium depende del tamaño aprovisionado del disco o blob. Azure asigna el tamaño aprovisionado (redondeado) a la opción de disco de Almacenamiento premium más cercana según se especifica en la tabla de la sección [Objetivos de escalabilidad y rendimiento del Almacenamiento premium](#premium-storage-scalability-and-performance-targets). Todos los objetos almacenados en una cuenta de Almacenamiento premium se asignarán a uno de los tamaños aprovisionados admitidos y se facturarán en consecuencia. Por lo tanto, evite el uso de cuentas de Almacenamiento premium para almacenar blobs de tamaño muy pequeño. La facturación de cualquier disco o blob aprovisionado se prorratea cada hora con el precio mensual de la oferta de Almacenamiento premium. Por ejemplo, si se aprovisiona un disco P10 y se elimina después de 20 horas, se factura la oferta P10 prorrateada a 20 horas. Esto es así independientemente de la cantidad de datos que se escriba en el disco o del rendimiento/IOPS usado.

**Instantáneas del Almacenamiento premium**: las instantáneas en el Almacenamiento premium se facturan según la capacidad adicional que hayan usado. Para obtener información sobre las instantáneas, consulte [Crear una instantánea de un blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

**Transferencias de datos de salida**: las [transferencias de datos de salida](https://azure.microsoft.com/pricing/details/data-transfers/) (datos que salen de los centros de datos de Azure) se facturan en función del uso de ancho de banda.

Para obtener información detallada sobre los precios de Almacenamiento premium y VM de las series DS, DSv2 y GS, consulte:

- [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/)

## Copia de seguridad
Se pueden realizar copias de seguridad de las máquinas virtuales que usan Almacenamiento Premium mediante Copia de seguridad de Azure. [Más detalles](../backup/backup-azure-vms-first-look-arm.md).

## Inicio rápido

## Creación y uso de una cuenta de Almacenamiento premium para un disco de datos de la máquina virtual

En esta sección se mostrarán los siguientes escenarios con el Portal de Azure, Azure PowerShell y la CLI de Azure:

- Cómo crear una cuenta de Almacenamiento premium.
- Cómo crear una máquina virtual y conectarle un disco de datos cuando se usa el Almacenamiento premium.
- Cómo cambiar la directiva de almacenamiento en caché de disco de un disco de datos conectado a una máquina virtual.

### Creación de una máquina virtual de Azure mediante Almacenamiento premium a través del Portal de Azure

#### I. Creación de una cuenta de Almacenamiento premium en el Portal de Azure

En esta sección se muestra cómo crear una cuenta de Almacenamiento premium mediante el Portal de Azure.

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com). Consulte la oferta [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) si todavía no tiene una suscripción.

2. En el menú del concentrador, seleccione **Nuevo** -> **Datos + almacenamiento** -> **Cuenta de almacenamiento**.

3. Escriba un nombre para la cuenta de almacenamiento.

	> [AZURE.NOTE] Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.
	>  
	> El nombre de la cuenta de almacenamiento debe ser único dentro de Azure. El Portal de Azure indicará si el nombre de la cuenta de almacenamiento que seleccione ya está en uso.

4. Especifique el modelo de implementación que se va a usar: **Resource Manager** o **Clásico**. **Administrador de recursos** es el modelo de implementación recomendado. Para obtener más información, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](../resource-manager-deployment-model.md).

5. Especifique el nivel de rendimiento de la cuenta de almacenamiento como **Premium**.

6. **Almacenamiento con redundancia local (LRS)** es la única opción de replicación disponible con Almacenamiento Premium. Para obtener más información sobre las opciones de replicación del Almacenamiento de Azure, consulte [Replicación de Almacenamiento de Azure](storage-redundancy.md).

7. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

8. Especifique un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).

9. Seleccione la ubicación geográfica para la cuenta de almacenamiento. Puede confirmar si Almacenamiento Premium está disponible en la ubicación seleccionada en [Azure Services by Region](https://azure.microsoft.com/regions/#services) (Servicios de Azure por región).

10. Haga clic en **Crear** para crear la cuenta de almacenamiento.

#### II. Creación de una máquina virtual de Azure mediante el Portal de Azure

Debe crear una máquina virtual de la serie DS, DSv2 o GS para poder usar el Almacenamiento premium. Siga los pasos de [Creación de una máquina virtual de Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para crear una nueva máquina virtual DS, DSv2 o GS.

#### III. Conexión de un disco de datos del Almacenamiento premium mediante el Portal de Azure

1. Busque la máquina virtual DS, DSv2 o GS nueva o existente en el Portal de Azure.
2. En la opción **Toda la configuración** de la máquina virtual, vaya a **Discos** y haga clic en **Adjuntar nuevo**.
3. Escriba el nombre del disco de datos y seleccione **Premium** en el parámetro **Type**. Seleccione los valores que quiera en **Tamaño** y **Almacenamiento en caché de host**.

	![Disco premium][Image1]

Consulte pasos más detallados en[How to attach a data disk in Azure portal](../virtual-machines/virtual-machines-windows-attach-disk-portal.md) (Cómo adjuntar un disco de datos en el Portal de Azure).

#### IV. Cambio de la directiva de almacenamiento en caché de disco mediante el Portal de Azure

1. Busque la máquina virtual DS, DSv2 o GS nueva o existente en el Portal de Azure.
2. En Toda la configuración para la máquina virtual, vaya a Discos y haga clic en el disco que desee cambiar.
3. Cambie la opción Almacenamiento en caché de host al valor deseado: None, ReadOnly o ReadWrite.

### Creación de una máquina virtual de Azure mediante Almacenamiento premium a través de Azure PowerShell

#### I. Creación de una cuenta de Almacenamiento premium en Azure PowerShell

Este ejemplo de PowerShell muestra cómo crear una nueva cuenta de Almacenamiento premium y conectar un disco de datos que use esa cuenta a una nueva máquina virtual de Azure.

1. Configure el entorno de PowerShell siguiendo los pasos que se indican en [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
2. Inicie la consola de PowerShell, conéctese a su suscripción y ejecute el cmdlet siguiente de PowerShell en la ventana de la consola. Tal como se muestra en la instrucción de PowerShell, deberá especificar el parámetro **Type** como **Premium\_LRS** al crear una cuenta de Almacenamiento Premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

#### II. Creación de una máquina virtual de Azure mediante Azure PowerShell

A continuación, cree una nueva VM de la serie DS y especifique que quiere Almacenamiento premium mediante la ejecución de los siguientes cmdlets de PowerShell en la ventana de consola. Puede crear una VM de la serie GS con los mismos pasos. Especifique el tamaño de VM adecuado en los comandos. Para, por ejemplo, Standard\_GS2:

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

#### III. Conexión de un disco de datos del Almacenamiento premium mediante Azure PowerShell

Si quiere más espacio en disco para la VM, adjunte un nuevo disco de datos a una VM existente de la serie DS, DSv2 o GS después de crearlo mediante la ejecución de los siguientes cmdlets de PowerShell en la ventana de consola:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

#### IV. Cambio de la directiva de almacenamiento en caché de disco mediante Azure PowerShell

Para actualizar la directiva de almacenamiento en caché de disco, anote el número LUN del disco de datos conectado. Ejecute el siguiente comando para actualizar el disco de datos conectado en el LUN número 2 a ReadOnly.

		Get-AzureVM "myservice" -name "MyVM" | Set-AzureDataDisk -LUN 2 -HostCaching ReadOnly | Update-AzureVM

### Creación de una máquina virtual de Azure mediante Almacenamiento premium a través de la interfaz de línea de comandos de Azure

La [interfaz de la línea de comandos de Azure](../xplat-cli-install.md) (CLI de Azure) proporciona un conjunto de comandos multiplataforma de código abierto para trabajar con la plataforma Azure. Los ejemplos siguientes muestran cómo usar la CLI de Azure (versión 0.8.14 y posterior) para crear una cuenta de Almacenamiento premium, una nueva máquina virtual y conectar un nuevo disco de datos desde una cuenta de Almacenamiento premium.

#### I. Creación de una cuenta de Almacenamiento premium mediante la CLI de Azure

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### II. Creación de una máquina virtual de la serie DS mediante la CLI de Azure

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-es-ES-30GB" -u "myusername" -p "passwd@123"

Visualización de información acerca de la máquina virtual

	azure vm show premium-test-vm

#### III. Conexión de un nuevo disco de datos premium mediante la CLI de Azure

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

Visualización de información acerca del nuevo disco de datos

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

#### IV. Cambio de la directiva de almacenamiento en caché de disco

Para cambiar la directiva de caché en uno de los discos mediante la CLI de Azure, ejecute el siguiente comando:

		$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>

Tenga en cuenta que las opciones de directiva de almacenamiento en caché pueden ser ReadOnly, None o ReadWrite. Para ver más opciones, consulte la Ayuda ejecutando el comando siguiente:

		azure vm disk attach --help

## Preguntas más frecuentes

1. **¿Puedo conectar discos de datos premium y estándar a una máquina virtual de la serie DS, DSv2 o GS?**

	Sí. Sí, puede conectar discos de datos premium y estándar a una máquina virtual de la serie DS, DSv2 o GS.

2. **¿Puedo conectar discos de datos premium y estándar a una máquina virtual de la serie D, Dv2 o G?**

	No, solo puede conectar discos de datos estándar a todas las máquinas virtuales que no sean de la serie DS, DSv2 o GS.

3. **Si creo un disco de datos premium a partir un disco duro virtual existente con 80 GB de tamaño, ¿cuánto me costará?**

	Un disco de datos premium creado a partir de un disco duro virtual de 80 GB se tratará como el siguiente tamaño de disco premium disponible, un disco P10. Se le cobrará según el precio de los discos P10.

4. **¿Hay costos de transacción cuando se usa el Almacenamiento premium?**

	Hay un costo fijo para cada tamaño de disco que esté aprovisionado con un número de IOPS y un rendimiento determinados. Los únicos otros costos son por el ancho de banda de salida y la capacidad para instantáneas, si corresponde. Consulte [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) para obtener más detalles.

5. **¿Dónde se pueden almacenar los diagnósticos de arranque de mi máquina virtual de la serie DS, DSv2 o GS?**

	Cree una cuenta de almacenamiento estándar para almacenar los diagnósticos de arranque de una máquina virtual de la serie DS, DSv2 o GS.

6. **¿Cuántas IOPS y cuánto rendimiento puedo obtener de la memoria caché de disco?**

	Los límites combinados de memoria caché y SSD local para la serie DS son 4000 IOPS por núcleo y 33 MB por segundo y núcleo. La serie GS ofrece 5000 IOPS por núcleo y 50 MB por segundo y núcleo.

7. **¿Qué es el SSD local en una máquina virtual de la serie DS, DSv2 o GS?**

	El SSD local es un almacenamiento temporal que se incluye con una máquina virtual de la serie DS, DSv2 o GS. No existe ningún costo extra para este almacenamiento temporal. Se recomienda que no use este almacenamiento temporal o SSD local para almacenar los datos de la aplicación, ya que no se conserva en el Almacenamiento de blobs de Azure.

8. **¿Puedo convertir mi cuenta de almacenamiento estándar en una premium?**

	No, no se puede convertir una cuenta de almacenamiento estándar en premium ni viceversa. Debe crear una cuenta de almacenamiento con el tipo deseado y copiar los datos a ella, si es aplicable.

9. **¿Cómo se convierte una máquina virtual de la serie D en una de la serie DS?**

	Consulte la guía de migración, [Migrar a Almacenamiento premium de Azure](storage-migration-to-premium-storage.md), para mover la carga de trabajo de una máquina virtual de la serie D con una cuenta de Almacenamiento Estándar a una de la serie DS con una cuenta de Almacenamiento Premium.

## Pasos siguientes

Para más información sobre el Almacenamiento premium de Azure, consulte los siguientes artículos.

### Diseño e implementación con el Almacenamiento premium de Azure

- [Almacenamiento premium de Azure: diseño de alto rendimiento](storage-premium-storage-performance.md)
- [Uso de operaciones de servicios BLOB con Almacenamiento premium de Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

### Guía de operaciones

- [Migrar a Almacenamiento premium de Azure](storage-migration-to-premium-storage.md)

### Entradas de blog

- [Azure Premium Storage Generally Available (Disponibilidad general del Almacenamiento premium de Azure)](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
- [Announcing the GS-Series: Adding Premium Storage Support to the Largest VMs in the Public Cloud (Anuncio de la serie GS: Incorporación de compatibilidad con el Almacenamiento premium a las mayores máquinas virtuales en la nube pública)](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

[Image1]: ./media/storage-premium-storage/Azure_attach_premium_disk.png

<!---HONumber=AcomDC_0727_2016-->