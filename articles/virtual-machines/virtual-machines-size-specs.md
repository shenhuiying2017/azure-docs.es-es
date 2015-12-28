<properties
 pageTitle="Tamaños de máquina virtual | Microsoft Azure"
 description="Enumera los distintos tamaños de máquinas virtuales y sus capacidades."
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/11/2015"
 ms.author="cynthn"/>

# Tamaños de máquinas virtuales

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Información general

En este artículo se describen los tamaños y las opciones disponibles para los recursos de proceso basados en la máquina virtual que puede usar para ejecutar las aplicaciones y cargas de trabajo. También ofrece consideraciones de implementación que hay que tener en cuenta siempre que planee usar estos recursos. Para obtener información sobre los precios de los diferentes tamaños, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

Para consultar límites generales de las máquinas virtuales de Azure, vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

Los tamaños estándar constan de varias series: A, D, DS, G y GS. Entre las consideraciones para algunos de estos tamaños, se incluyen:

*   Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,2 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.

    La disponibilidad regional de la serie Dv2 se basará en esta programación:
        octubre de 2015: Este 2 de EE. UU., Centro de EE. UU., Centro y norte de EE. UU., Oeste de EE. UU.
        Noviembre 2015: Este de EE. UU., Norte de Europa, Oeste de Europa
        Enero de 2016: Centro y Sur de EE. UU., Este de Asia y del Pacífico Sur, Sudeste de Asia y del Pacífico Sur, Este de Japón, Oeste de Japón,
                Este de Australia, Sudeste de Australia, Sur de Brasil


*   Las VM de la serie G ofrecen el mayor tamaño y el mejor rendimiento, y se ejecutan en hosts que tienen procesadores de la familia Intel Xeon E5 V3.

*   Las VM de las series DS y GS pueden usar Almacenamiento premium, que proporciona un almacenamiento de alto rendimiento y una baja latencia para cargas de trabajo con uso intensivo de E/S. Estas VM utilizan unidades de estado sólido (SSD) para hospedar los discos de una máquina virtual y también proporcionan una memoria caché de disco SSD local. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../storage-premium-storage-preview-portal.md)

El tamaño de la máquina virtual afecta a los precios. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Los costes de almacenamiento se calculan por separado según las páginas utilizadas en la cuenta de almacenamiento. Para obtener más información, consulte [Precios de Máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de Almacenamiento de Azure](http://azure.microsoft.com/pricing/details/storage/). Para obtener más detalles acerca del almacenamiento de máquinas virtuales, consulte [Acerca de los discos y los discos duros virtuales de las máquinas virtuales ](virtual-machines-disks-vhds.md).

Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:


*   Puede que algunos de los hosts físicos de los centros de datos de Azure no admitan tamaños de máquinas virtuales grandes, como A5 – A11. En consecuencia, puede ver el mensaje de error **No se pudo configurar la máquina virtual <machine name>** o **No se pudo crear la máquina virtual <machine name>** al cambiar el tamaño de una máquina virtual existente a un nuevo tamaño, al crear una nueva máquina virtual en una red virtual creada antes de 16 de abril de 2013 o al agregar una nueva máquina virtual a un servicio en la nube existente. Consulte [Error: "No se pudo configurar la máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) en el foro de soporte técnico para ver una lista de soluciones alternativas para cada escenario de implementación.  

*   Los tamaños de máquina virtual A8/A10 y A9/A11 tienen las mismas capacidades físicas. Las instancias de máquina virtual A8 y A9 incluyen otro adaptador de red que se conecta a una red de acceso de memoria directa remota (RDMA) para una rápida comunicación entre máquinas virtuales. Las instancias A8 y A9 están diseñadas para aplicaciones informáticas de alto rendimiento que requieren una comunicación constante y de baja latencia entre nodos durante la ejecución, por ejemplo, aplicaciones que utilizan la interfaz de paso de mensajes (MPI). Las instancias de máquina virtual A10 y A11 no incluyen el adaptador de red adicional. Las instancias A10 y A11 están diseñadas para aplicaciones informáticas de alto rendimiento que no requieren la comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.

*	Las series Dv2, D, G y DS/GS son ideales para las aplicaciones que requieren CPU más rápidas, mejor rendimiento de disco local, o tienen mayor demanda de memoria. Ofrecen una combinación eficaz para muchas aplicaciones de clase empresarial.





## Tablas de tamaño

Las siguientes tablas muestran los tamaños y las capacidades que ofrecen.

>[AZURE.NOTE] La capacidad de almacenamiento se representa mediante 1024^3 bytes como unidad de medida para GB. En ocasiones, esto se conoce como gibibyte o definición de base 2 Al comparar los tamaños que utilizan distintos sistemas de base, tenga en cuenta que los tamaños de base 2 podrían parecer más pequeños que los de base 10. No obstante, para cualquier tamaño específico (como 1 GB), un sistema de base 2 ofrece más capacidad que un sistema de base 10, ya que 1024^3 es mayor que 1000^3.



## Nivel estándar: serie A

En el modelo de implementación clásica, algunos tamaños de máquina virtual son ligeramente diferentes en Powershell y CLI.

* Standard\_A0 es ExtraSmall 
* Standard\_A1 es Small
* Standard\_A2 es Medium
* Standard\_A3 es Large
* Standard\_A4 es ExtraLarge

<br>

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
|---|---|---|---|---|---|---|
|Standard\_A0\\ExtraSmall |1|768 MB|1| Temporal = 20 GB |1|1x500|
|Standard\_A1\\Small|1|1,75 GB|1|Temporal = 70 GB |2|2 x 500|
|Standard\_A2\\Medium|2|3,5 GB|1|Temporal = 135 GB |4|4x500|
|Standard\_A3\\Large|4|7 GB|2|Temporal = 285 GB |8|8x500|
|Standard\_A4\\ExtraLarge|8|14 GB|4|Temporal = 605 GB |16|16x500|
|Standard\_A5|2|14 GB|1|Temporal = 135 GB |4|4x500|
|Standard\_A6|4|28 GB|2|Temporal = 285 GB |8|8x500|
|Standard\_A7|8|56 GB|4|Temporal = 605 GB |16|16x500|
|Standard\_A8|8|56 GB|2| Temporal = 382 GB Nota: para obtener más información y comentarios sobre el uso de este tamaño, consulte [Acerca de las instancias de proceso intensivo A8, A9, A10 y A11](http://go.microsoft.com/fwlink/p/?linkid=328042). |16|16x500|
|Standard\_A9|16|112 GB|4| Temporal = 382 GB Nota: para obtener más información y comentarios sobre el uso de este tamaño, consulte [Acerca de las instancias de proceso intensivo A8, A9, A10 y A11](http://go.microsoft.com/fwlink/p/?linkid=328042). |16|16x500|
|Standard\_A10|8|56 GB|2| Temporal = 382 GB Nota: para obtener más información y comentarios sobre el uso de este tamaño, consulte [Acerca de las instancias de proceso intensivo A8, A9, A10 y A11](http://go.microsoft.com/fwlink/p/?linkid=328042). |16|16x500|
|Standard\_A11|16|112 GB|4| Temporal = 382 GB Nota: para obtener más información y comentarios sobre el uso de este tamaño, consulte [Acerca de las instancias de proceso intensivo A8, A9, A10 y A11](http://go.microsoft.com/fwlink/p/?linkid=328042). |16|16x500|

## Nivel estándar: serie D

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
|---|---|---|---|---|---|---|
|Standard\_D1 |1|3,5 GB|1|Temporal (SSD) =50 GB |2|2 x 500|
|Standard\_D2 |2|7 GB|2|Temporal (SSD) =100 GB |4|4x500|
|Standard\_D3 |4|14 GB|4|Temporal (SSD) =200 GB |8|8x500|
|Standard\_D4 |8|28 GB|8|Temporal (SSD) =400 GB |16|16x500|
|Standard\_D11 |2|14 GB|2|Temporal (SSD) =100 GB |4|4x500|
|Standard\_D12 |4|28 GB|4|Temporal (SSD) =200 GB |8|8x500|
|Standard\_D13 |8|56 GB|8|Temporal (SSD) =400 GB |16|16x500|
|Standard\_D14 |16|112 GB|8|Temporal (SSD) =800 GB |32|32x500|

## Nivel estándar: serie Dv2

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
|---|---|---|---|---|---|---|
|Standard\_D1\_v2 |1|3,5 GB|1|Temporal (SSD) =50 GB |2|2 x 500|
|Standard\_D2\_v2 |2|7 GB|2|Temporal (SSD) =100 GB |4|4x500|
|Standard\_D3\_v2 |4|14 GB|4|Temporal (SSD) =200 GB |8|8x500|
|Standard\_D4\_v2 |8|28 GB|8|Temporal (SSD) =400 GB |16|16x500|
|Standard\_D5\_v2 |16|56 GB|8|Temporal (SSD) =800 GB |32|32x500|
|Standard\_D11\_v2 |2|14 GB|2|Temporal (SSD) =100 GB |4|4x500|
|Standard\_D12\_v2 |4|28 GB|4|Temporal (SSD) =200 GB |8|8x500|
|Standard\_D13\_v2 |8|56 GB|8|Temporal (SSD) =400 GB |16|16x500|
|Standard\_D14\_v2 |16|112 GB|8|Temporal (SSD) =800 GB |32|32x500|

## Nivel estándar: serie DS*

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Tamaño de caché (GB)|E/S de disco máx. y ancho de banda|
|---|---|---|---|---|---|---|---|
|Standard\_DS1 |1|3,5|1|Disco SSD local = 7 GB |2|43| 3\.200 32 MB por segundo |
|Standard\_DS2 |2|7|2|Disco SSD local = 14 GB |4|86| 6\.400 64 MB por segundo |
|Standard\_DS3 |4|14|4|Disco SSD local = 28 GB |8|172| 12\.800 128 MB por segundo |
|Standard\_DS4 |8|28|8|Disco SSD local = 56 GB |16|344| 25\.600 256 MB por segundo |
|Standard\_DS11 |2|14|2|Disco SSD local = 28 GB |4|72| 6\.400 64 MB por segundo |
|Standard\_DS12 |4|28|4|Disco SSD local = 56 GB |8|144| 12\.800 128 MB por segundo |
|Standard\_DS13 |8|56|8|Disco SSD local = 112 GB |16|288| 25\.600 256 MB por segundo |
|Standard\_DS14 |16|112|8|Disco SSD local = 224 GB |32|576| 50\.000 512 MB por segundo |

*Las operaciones de entrada/salida máximas por segundo (E/S) y el rendimiento (ancho de banda) posibles con una máquina virtual de la serie DS se ven afectadas por el tamaño del disco. Para obtener información detallada, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage-premium-storage-preview-portal.md).

## Nivel estándar: serie G

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
|---|---|---|---|---|---|---|
|Standard\_G1 |2|28 GB|1|Disco SSD local = 384 GB |4|4 x 500|
|Standard\_G2 |4|56 GB|2|Disco SSD local = 768 GB |8|8 x 500|
|Standard\_G3 |8|112 GB|4|Disco SSD local = 1.536 GB |16|16 x 500|
|Standard\_G4 |16|224 GB|8|Disco SSD local = 3.072 GB |32|32 x 500|
|Standard\_G5 |32|448 GB|8|Disco SSD local = 6.144 GB |64| 64 x 500 |

## Nivel estándar: serie GS

|Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Tamaño de caché (GB)|E/S de disco máx. y ancho de banda|
|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|Disco SSD local = 56 GB |4|264| 5\.000 125 MB por segundo |
|Standard\_GS2|4|56|2|Disco SSD local = 112 GB |8|528| 10\.000 250 MB por segundo |
|Standard\_GS3|8|112|4|Disco SSD local = 224 GB |16|1056| 20\.000 500 MB por segundo |
|Standard\_GS4|16|224|8|Disco SSD local = 448 GB |32|2112| 40\.000 1.000 MB por segundo |
|Standard\_GS5|32|448|8|Disco SSD local = 896 GB |64|4224| 80\.000 2.000 MB por segundo |


### Consulte también

[Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)

[Sobre las instancias informáticas intensivas A8, A9, A10 y A11](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_1217_2015-->