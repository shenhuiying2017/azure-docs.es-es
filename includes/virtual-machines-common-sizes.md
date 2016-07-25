
Para consultar límites generales de las máquinas virtuales de Azure, vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../articles/azure-subscription-service-limits.md).

Los tamaños estándar constan de varias series: A, D, DS, F, Fs, G y GS. Entre las consideraciones para algunos de estos tamaños, se incluyen:

*   Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.

* La serie F se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, que puede alcanzar velocidades de reloj de hasta 3,1 GHz con Intel Turbo Boost Technology 2.0. Este es el mismo rendimiento de CPU que el de la serie Dv2 de máquinas virtuales. Con un precio en lista inferior por hora, la serie F tiene la mejor relación precio/rendimiento en la cartera de Azure según en la unidad de proceso de Azure (ACU) por núcleo.

	La serie F también presenta un nuevo estándar en lo referente a la nomenclatura de tamaño de máquina virtual para Azure. Para esta serie y los tamaños de máquina virtual lanzados en el futuro, el valor numérico después de la letra del nombre de familia coincidirá con el número de núcleos de CPU. La designación de funcionalidades adicionales, como la optimización para almacenamiento premium, se realizará mediante letras a continuación del número de núcleos de CPU. Este formato de nombres se utilizará en los futuros tamaños de máquina virtual lanzados, pero no tendrá efecto retroactivo sobre los nombres de los tamaños de máquina virtual existentes que se hayan lanzado. Estos permanecen igual.


*   Las máquinas virtuales de la serie G ofrecen la mayor cantidad de memoria y se ejecutan en hosts con procesadores de la familia Intel Xeon E5 V3.


*   Las máquinas virtuales de las series DS, DSv2, Fs y GS pueden usar Almacenamiento premium, lo que proporciona almacenamiento de alto rendimiento y baja latencia para cargas de trabajo con uso intensivo de E/S. Estas VM utilizan unidades de estado sólido (SSD) para hospedar los discos de una máquina virtual y también proporcionan una memoria caché de disco SSD local. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)


*   Las máquinas virtuales de la serie A se pueden implementar en diversos procesadores y tipos de hardware. Según el hardware, el tamaño es una limitación para ofrecer un rendimiento coherente del procesador para la instancia en ejecución, independientemente del hardware en que se implementó. Con el fin de determinar el hardware físico en que se implementó este tamaño, cree una consulta para el hardware virtual desde dentro de la máquina virtual.

*   El tamaño A0 está sobresuscrito en el hardware físico. Solo en este tamaño específico, las implementaciones de otros clientes podrían afectar el rendimiento de la carga de trabajo en ejecución. A continuación, se indica el rendimiento relativo como la línea base esperada, sujeta a una variabilidad aproximada de 15 por ciento.


El tamaño de la máquina virtual afecta a los precios. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Los costes de almacenamiento se calculan por separado según las páginas utilizadas en la cuenta de almacenamiento. Para obtener más información, consulte [Precios de Máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/).


Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:


* Los tamaños A8-A11 también se conocen como *instancias de proceso intensivo*. El hardware que ejecuta estos tamaños está diseñado y optimizado para aplicaciones de proceso intensivo que consumen muchos recursos de red, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HPC), el modelado y las simulaciones. Para obtener más información y consideraciones sobre el uso de estos tamaños, consulte [Sobre las instancias de proceso intensivo A8, A9, A10 y A11](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


*	Las series Dv2, D, G y DS/GS son ideales para las aplicaciones que requieren CPU más rápidas, mejor rendimiento de disco local, o tienen mayor demanda de memoria. Ofrecen una combinación eficaz para muchas aplicaciones de clase empresarial.

* Las máquinas virtuales de la serie F son una opción excelente para cargas de trabajo que exigen CPU más rápidas, pero que no necesitan tanta memoria o SSD local por núcleo de CPU. Las cargas de trabajo, como análisis, servidores de juegos, servidores web y procesamiento por lotes se beneficiarán del valor de la serie F.

*   Puede que algunos de los hosts físicos de los centros de datos de Azure no admitan tamaños de máquinas virtuales grandes, como A5 – A11. En consecuencia, puede ver el mensaje de error **No se pudo configurar la máquina virtual <nombre de la máquina>** o **No se pudo crear la máquina virtual <nombre de la máquina>** al cambiar el tamaño de una máquina virtual existente por un nuevo tamaño, al crear una nueva máquina virtual en una red virtual creada antes del 16 de abril de 2013 o al agregar una nueva máquina virtual a un servicio en la nube existente. Consulte [Error: "No se pudo configurar la máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) en el foro de soporte técnico para ver una lista de soluciones alternativas para cada escenario de implementación.


## Consideraciones sobre rendimiento

Creamos el concepto de unidad de proceso de Azure (ACU) para brindar una forma de comparar el rendimiento de los procesos (CPU) en todas las SKU de Azure. Esto le ayudará a identificar fácilmente el SKU que tiene más probabilidades de satisfacer sus necesidades de rendimiento. Actualmente, una ACU está estandarizada en una máquina virtual pequeña (Standard\_A1) como 100 y todas las demás SKU representan, aproximadamente, qué tanto más rápido esa SKU puede ejecutar una prueba comparativa estándar.

>[AZURE.IMPORTANT] La ACU es solo una referencia. Los resultados de la carga de trabajo pueden variar.

<br>

|Familia de SKU |ACU/núcleo |
|---|---|
|[Standard\_A0](#a-series) |50 |
|[Standard\_A1-4](#a-series) |100 |
|[Standard\_A5-7](#a-series) |100 |
|[A8-A11](#a-series) |225*|
|[D1-14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210 - 250*|
|[14 DS1](#ds-series) |160 |
|[15v2 DS1](#dsv2-series) |210-250* |
|[F1-F16](#f-series) | 210-250*|
|[F1s F16s](#fs-series) | 210-250*|
|[G1 5](#g-series) |180 - 240*|
|[GS1 5](#gs-series) |180 - 240*|


Las ACU marcadas con un asterisco * usan la tecnología Intel® Turbo para incrementar la frecuencia de CPU y brindar una mejora del rendimiento. El volumen de la mejora puede variar según el tamaño de la máquina virtual, la carga de trabajo y las otras cargas de trabajo que se ejecutan en el mismo host.

## Tablas de tamaño

Las siguientes tablas muestran los tamaños y las capacidades que ofrecen.

* La capacidad de almacenamiento se representa mediante 1024^3 bytes como unidad de medida para GB. En ocasiones, esto se conoce como gibibyte o definición de base 2 Al comparar los tamaños que utilizan distintos sistemas de base, tenga en cuenta que los tamaños de base 2 podrían parecer más pequeños que los de base 10. No obstante, para cualquier tamaño específico (como 1 GB), un sistema de base 2 ofrece más capacidad que un sistema de base 10, ya que 1024^3 es mayor que 1000^3.

* El ancho de banda de red máximo es el ancho de banda agregado máximo que se asigna por cada tipo de VM. El ancho de banda máximo proporciona una orientación a la hora de seleccionar el tipo de VM correcto a fin de garantizar la disponibilidad de la capacidad de red adecuada. Al cambiar a Bajo, Moderado, Alto y Muy alto, el rendimiento aumentará en consecuencia. El rendimiento de red real dependerá de muchos factores (como, por ejemplo, las cargas de la red y de la aplicación y la configuración de red de la aplicación).


## Serie A

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|
|Standard\_A0 |1|768 MB|1| Temporal = 20 GB |1|1x500| Bajo |
|Standard\_A1 |1|1,75 GB|1|Temporal = 70 GB |2|2 x 500| Moderado |
|Standard\_A2 |2|3,5 GB|1|Temporal = 135 GB |4|4x500| Moderado |
|Standard\_A3 |4|7 GB|2|Temporal = 285 GB |8|8x500| Alto |
|Standard\_A4 |8|14 GB|4|Temporal = 605 GB |16|16x500| Alto |
|Standard\_A5 |2|14 GB|1|Temporal = 135 GB |4|4x500| Moderado |
|Standard\_A6 |4|28 GB|2|Temporal = 285 GB |8|8x500| Alto |
|Standard\_A7 |8|56 GB|4|Temporal = 605 GB |16|16x500| Alto |



## Serie A: instancias de proceso intensivo

Para más información, junto con consideraciones sobre el uso de estos tamaños, consulte [Sobre las instancias de proceso intensivo A8, A9, A10 y A11](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|
|Standard\_A8|8|56 GB|2| Temporal = 382 GB |16|16x500| Alto |
|Standard\_A9|16|112 GB|4| Temporal = 382 GB |16|16x500| Muy alto |
|Standard\_A10|8|56 GB|2| Temporal = 382 GB |16|16x500| Alto |
|Standard\_A11|16|112 GB|4| Temporal = 382 GB |16|16x500| Muy alto |

## Serie D

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|
|Standard\_D1 |1|3,5 GB|1|Temporal (SSD) =50 GB |2|2 x 500| Moderado |
|Standard\_D2 |2|7 GB|2|Temporal (SSD) =100 GB |4|4x500| Alto |
|Standard\_D3 |4|14 GB|4|Temporal (SSD) =200 GB |8|8x500| Alto |
|Standard\_D4 |8|28 GB|8|Temporal (SSD) =400 GB |16|16x500| Alto |
|Standard\_D11 |2|14 GB|2|Temporal (SSD) =100 GB |4|4x500| Alto |
|Standard\_D12 |4|28 GB|4|Temporal (SSD) =200 GB |8|8x500| Alto |
|Standard\_D13 |8|56 GB|8|Temporal (SSD) =400 GB |16|16x500| Alto |
|Standard\_D14 |16|112 GB|8|Temporal (SSD) =800 GB |32|32x500| Muy alto |


## Serie Dv2

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|
|Standard\_D1\_v2 |1|3,5 GB|1|Temporal (SSD) =50 GB |2|2 x 500| Moderado |
|Standard\_D2\_v2 |2|7 GB|2|Temporal (SSD) =100 GB |4|4x500| Alto |
|Standard\_D3\_v2 |4|14 GB|4|Temporal (SSD) =200 GB |8|8x500| Alto |
|Standard\_D4\_v2 |8|28 GB|8|Temporal (SSD) =400 GB |16|16x500| Alto |
|Standard\_D5\_v2 |16|56 GB|8|Temporal (SSD) =800 GB |32|32x500| Extremadamente alto |
|Standard\_D11\_v2 |2|14 GB|2|Temporal (SSD) =100 GB |4|4x500| Alto |
|Standard\_D12\_v2 |4|28 GB|4|Temporal (SSD) =200 GB |8|8x500| Alto |
|Standard\_D13\_v2 |8|56 GB|8|Temporal (SSD) =400 GB |16|16x500| Alto |
|Standard\_D14\_v2 |16|112 GB|8|Temporal (SSD) =800 GB |32|32x500| Extremadamente alto |
|Standard\_D15\_v2 |20|140 GB|10|Temporal (SSD) =1 TB |40|40 x 500| Extremadamente alto |


## Serie DS*

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Tamaño de caché (GB)|E/S de disco máx. y ancho de banda| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|---|
|Standard\_DS1 |1|3,5|1|Disco SSD local = 7 GB |2|43| 3\.200 32 MB por segundo | Moderado |
|Standard\_DS2 |2|7|2|Disco SSD local = 14 GB |4|86| 6\.400 64 MB por segundo | Alto |
|Standard\_DS3 |4|14|4|Disco SSD local = 28 GB |8|172| 12\.800 128 MB por segundo | Alto |
|Standard\_DS4 |8|28|8|Disco SSD local = 56 GB |16|344| 25\.600 256 MB por segundo | Alto |
|Standard\_DS11 |2|14|2|Disco SSD local = 28 GB |4|72| 6\.400 64 MB por segundo | Alto |
|Standard\_DS12 |4|28|4|Disco SSD local = 56 GB |8|144| 12\.800 128 MB por segundo | Alto |
|Standard\_DS13 |8|56|8|Disco SSD local = 112 GB |16|288| 25\.600 256 MB por segundo | Alto |
|Standard\_DS14 |16|112|8|Disco SSD local = 224 GB |32|576| 51 200 512 MB por segundo | Muy alto |

*Las operaciones de entrada/salida máximas por segundo (E/S) y el rendimiento (ancho de banda) posibles con una máquina virtual de la serie DS se ven afectadas por el tamaño del disco. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)


## Serie DSv2*

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Tamaño de caché (GB)|E/S de disco máx. y ancho de banda| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|---|
|Standard\_DS1\_v2 |1|3,5|1|Disco SSD local = 7 GB |2|43| 3\.200 48 MB por segundo | Moderado |
|Standard\_DS2\_v2 |2|7|2|Disco SSD local = 14 GB |4|86| 6\.400 96 MB por segundo | Alto |
|Standard\_DS3\_v2 |4|14|4|Disco SSD local = 28 GB |8|172| 12\.800 192 MB por segundo | Alto |
|Standard\_DS4\_v2 |8|28|8|Disco SSD local = 56 GB |16|344| 25\.600 384 MB por segundo | Alto |
|Standard\_DS5\_v2 |16|56|8|Disco SSD local = 112 GB |32|688| 51 200 768 MB por segundo | Extremadamente alto |
|Standard\_DS11\_v2 |2|14|2|Disco SSD local = 28 GB |4|72| 6\.400 96 MB por segundo | Alto |
|Standard\_DS12\_v2 |4|28|4|Disco SSD local = 56 GB |8|144| 12\.800 192 MB por segundo | Alto |
|Standard\_DS13\_v2 |8|56|8|Disco SSD local = 112 GB |16|288| 25\.600 384 MB por segundo | Alto |
|Standard\_DS14\_v2 |16|112|8|Disco SSD local = 224 GB |32|576| 51 200 768 MB por segundo | Extremadamente alto |
|Standard\_DS15\_v2 |20|140 GB|10|Disco SSD local = 280 GB |40| 720|64 000 960 MB por segundo | Extremadamente alto |


*Las operaciones de entrada/salida máximas por segundo (E/S) y el rendimiento (ancho de banda) posibles con una máquina virtual de la serie DS se ven afectadas por el tamaño del disco. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)


## Serie F


| Tamaño | Núcleos de CPU | Memoria | NICs (Máx) | Tamaño del disco | Número máximo de discos de datos (1023 GB cada uno) | E/S máx. (500 por disco) | Ancho de banda de red máximo |
|--------------|-----------|--------|------------|-------------------------|--------------------------|--------------------------|-------------|
| Standard\_F1 | 1 | 2 GB | 1 | Temporal (SSD) =16 GB | 2 | 2 x 500 | Moderado |
| Standard\_F2 | 2 | 4 GB | 2 | Temporal (SSD) =32 GB | 4 | 4x500 | Alto |
| Standard\_F4 | 4 | 8 GB | 4 | Temporal (SSD) =64 GB | 8 | 8x500 | Alto |
| Standard\_F8 | 8 | 16 GB | 8 | Temporal (SSD) =128 GB | 16 | 16x500 | Alto |
| Standard\_F16 | 16 | 32 GB | 8 | Temporal (SSD) =256 GB | 32 | 32x500 | Extremadamente alto |



## Serie Fs*

| Tamaño | Núcleos de CPU | Memoria | NICs (Máx) | Tamaño del disco | Número máximo de discos de datos (1023 GB cada uno) | Tamaño de caché (GB) | Ancho de banda y E/S máx. de disco | Ancho de banda de red máximo |
|---------------|-----------|--------|------------|------------------------|-----------|-----------|----------------------------|------------|
| Standard\_F1s | 1 | 2 | 1 | Disco SSD local = 4 GB | 2 | 12 | 3\.200 48 MB por segundo | Moderado |
| Standard\_F2s | 2 | 4 | 2 | Disco SSD local = 8 GB | 4 | 24 | 6\.400 96 MB por segundo | Alto |
| Standard\_F4s | 4 | 8 | 4 | Disco SSD local = 16 GB | 8 | 48 | 12\.800 192 MB por segundo | Alto |
| Standard\_F8s | 8 | 16 | 8 | Disco SSD local = 32 GB | 16 | 96 | 25\.600 384 MB por segundo | Alto |
| Standard\_F16s | 16 | 32 | 8 | Disco SSD local = 64 GB | 32 | 192 | 51 200 768 MB por segundo | Extremadamente alto |



*Las operaciones de entrada/salida máximas por segundo (IOPS) y el rendimiento (ancho de banda) posibles con una máquina virtual de la serie Fs se ven afectadas por el tamaño del disco. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)





## Serie G

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|
|Standard\_G1 |2|28 GB|1|Disco SSD local = 384 GB |4|4 x 500| Alto |
|Standard\_G2 |4|56 GB|2|Disco SSD local = 768 GB |8|8 x 500| Alto |
|Standard\_G3 |8|112 GB|4|Disco SSD local = 1.536 GB |16|16 x 500| Muy alto | 
|Standard\_G4 |16|224 GB|8|Disco SSD local = 3.072 GB |32|32 x 500| Extremadamente alto |
|Standard\_G5 |32|448 GB|8|Disco SSD local = 6.144 GB |64| 64 x 500 | Extremadamente alto |




## Serie GS

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Tamaño de caché (GB)|E/S de disco máx. y ancho de banda| Ancho de banda de red máximo |
|---|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|Disco SSD local = 56 GB |4|264| 5\.000 125 MB por segundo | Alto |
|Standard\_GS2|4|56|2|Disco SSD local = 112 GB |8|528| 10\.000 250 MB por segundo | Alto | 
|Standard\_GS3|8|112|4|Disco SSD local = 224 GB |16|1056| 20\.000 500 MB por segundo | Muy alto |
|Standard\_GS4|16|224|8|Disco SSD local = 448 GB |32|2112| 40\.000 1.000 MB por segundo | Extremadamente alto |
|Standard\_GS5|32|448|8|Disco SSD local = 896 GB |64|4224| 80\.000 2.000 MB por segundo | Extremadamente alto |




## Notas: Standard\_A0 - A4 con CLI y Powershell 


En el modelo de implementación clásica, algunos nombres de tamaños de VM varían ligeramente en la CLI y en Powershell:

* Standard\_A0 es ExtraSmall
* Standard\_A1 es Small
* Standard\_A2 es Medium
* Standard\_A3 es Large
* Standard\_A4 es ExtraLarge


## Pasos siguientes

- Conozca los [límites, las cuotas y las restricciones de suscripción y servicios de Azure](../articles/azure-subscription-service-limits.md).
- Más información [sobre las instancias de proceso intensivo A8, A9, A10 y A11](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) para cargas de trabajo, como informática de alto rendimiento (HPC).

<!---HONumber=AcomDC_0713_2016-->