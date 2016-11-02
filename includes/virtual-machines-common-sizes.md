


Hay varios tamaños estándar para elegir en Azure. Entre las consideraciones para algunos de estos tamaños, se incluyen:

*   Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.

* La serie F se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, que puede alcanzar velocidades de reloj de hasta 3,1 GHz con Intel Turbo Boost Technology 2.0. Este es el mismo rendimiento de CPU que el de la serie Dv2 de máquinas virtuales.  Con un precio en lista inferior por hora, la serie F tiene la mejor relación precio/rendimiento en la cartera de Azure según en la unidad de proceso de Azure (ACU) por núcleo. 

    La serie F también presenta un nuevo estándar en lo referente a la nomenclatura de tamaño de máquina virtual para Azure. Para esta serie y los tamaños de máquina virtual lanzados en el futuro, el valor numérico después de la letra del nombre de familia coincidirá con el número de núcleos de CPU. La designación de funcionalidades adicionales, como la optimización para almacenamiento premium, se realizará mediante letras a continuación del número de núcleos de CPU. Este formato de nombres se utilizará en los futuros tamaños de máquina virtual lanzados, pero no tendrá efecto retroactivo sobre los nombres de los tamaños de máquina virtual existentes que se hayan lanzado. Estos permanecen igual.


*   Las máquinas virtuales de la serie G ofrecen la mayor cantidad de memoria y se ejecutan en hosts con procesadores de la familia Intel Xeon E5 V3.


*   Las máquinas virtuales de las series DS, DSv2, Fs y GS pueden usar Almacenamiento premium, lo que proporciona almacenamiento de alto rendimiento y baja latencia para cargas de trabajo con uso intensivo de E/S. Estas VM utilizan unidades de estado sólido (SSD) para hospedar los discos de una máquina virtual y también proporcionan una memoria caché de disco SSD local. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)


*   Las máquinas virtuales de la serie A se pueden implementar en diversos procesadores y tipos de hardware. Según el hardware, el tamaño es una limitación para ofrecer un rendimiento coherente del procesador para la instancia en ejecución, independientemente del hardware en que se implementó. Con el fin de determinar el hardware físico en que se implementó este tamaño, cree una consulta para el hardware virtual desde dentro de la máquina virtual.

*   El tamaño A0 está sobresuscrito en el hardware físico. Solo en este tamaño específico, las implementaciones de otros clientes podrían afectar el rendimiento de la carga de trabajo en ejecución. A continuación, se indica el rendimiento relativo como la línea base esperada, sujeta a una variabilidad aproximada de 15 por ciento.


El tamaño de la máquina virtual afecta a los precios. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Los costes de almacenamiento se calculan por separado según las páginas utilizadas en la cuenta de almacenamiento. Para más información, consulte [Precios de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 


Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:


* Los tamaños A8-A11 los de la serie H también se conocen como *instancias de proceso intensivo*. El hardware que ejecuta estos tamaños está diseñado y optimizado para aplicaciones de proceso intensivo que consumen muchos recursos de red, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HPC), el modelado y las simulaciones. La serie A8-A11 utiliza Intel Xeon E5-2670 @ 2,6 GHz y la serie H, Intel Xeon E5-2667 v3 @ 3,2 GHz. Para más información y consideraciones sobre el uso de estos tamaños, consulte [Acerca de las máquinas virtuales de la serie H y A de proceso intensivo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 



* Las series Dv2, D, G y DS/GS son ideales para las aplicaciones que requieren CPU más rápidas, mejor rendimiento de disco local, o tienen mayor demanda de memoria.  Ofrecen una combinación eficaz para muchas aplicaciones de clase empresarial.

* Las máquinas virtuales de la serie F son una opción excelente para cargas de trabajo que exigen CPU más rápidas, pero que no necesitan tanta memoria o SSD local por núcleo de CPU.  Las cargas de trabajo, como análisis, servidores de juegos, servidores web y procesamiento por lotes se beneficiarán del valor de la serie F.

*   Puede que algunos de los hosts físicos de los centros de datos de Azure no admitan tamaños de máquinas virtuales grandes, como A5 – A11. En consecuencia, puede ver el mensaje de error **No se pudo configurar la máquina virtual <machine name>** o **No se pudo crear la máquina virtual <machine name>** al cambiar el tamaño de una máquina virtual existente a un nuevo tamaño, al crear una nueva máquina virtual en una red virtual creada antes de 16 de abril de 2013 o al agregar una nueva máquina virtual a un servicio en la nube existente. Consulte [Error: "No se pudo configurar la máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) en el foro de soporte técnico para ver una lista de soluciones alternativas para cada escenario de implementación.  

* La suscripción también podría limitar el número de núcleos que se pueden implementar en ciertas familias de tamaños. Para aumentar una cuota, póngase en contacto con el soporte técnico de Azure.


## <a name="performance-considerations"></a>Consideraciones sobre rendimiento

Creamos el concepto de unidad de proceso de Azure (ACU) para brindar una forma de comparar el rendimiento de los procesos (CPU) en todas las SKU de Azure. Esto le ayudará a identificar fácilmente el SKU que tiene más probabilidades de satisfacer sus necesidades de rendimiento.  Actualmente, una ACU está estandarizada en una máquina virtual pequeña (Standard_A1) como 100 y todas las demás SKU representan, aproximadamente, qué tanto más rápido esa SKU puede ejecutar una prueba comparativa estándar. 

>[AZURE.IMPORTANT] La ACU es solo una referencia.  Los resultados de la carga de trabajo pueden variar. 

<br>

|Familia de SKU |ACU/núcleo |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8-A11](#a-series)    |225*|
|[D1-14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210 - 250*|
|[14 DS1](#ds-series)   |160 |
|[15v2 DS1](#dsv2-series)   |210-250* |
|[F1-F16](#f-series) | 210-250*|
|[F1s F16s](#fs-series) | 210-250*|
|[G1 5](#g-series)  |180 - 240*|
|[GS1 5](#gs-series)    |180 - 240*|
|[H](#h-series) |290 - 300*|


Las ACU marcadas con un asterisco * usan la tecnología Intel® Turbo para incrementar la frecuencia de CPU y brindar una mejora del rendimiento.  El volumen de la mejora puede variar según el tamaño de la máquina virtual, la carga de trabajo y las otras cargas de trabajo que se ejecutan en el mismo host.

## <a name="size-tables"></a>Tablas de tamaño

Las siguientes tablas muestran los tamaños y las capacidades que ofrecen.

* La capacidad de almacenamiento se muestra en unidades de GiB o 1024^3 bytes. Cuando compare discos que se miden en GB (1000^3 bytes) con discos que se miden en GiB (1024^3), recuerde que los números que representan la capacidad en GiB pueden parecer más pequeños. Por ejemplo, 1023 GiB = 1098.4 GB

* Se midió el rendimiento de disco en operaciones de entrada/salida por segundo (E/S por segundo) y Mbps, donde Mbps = 10^6 bytes/s.

* Los discos de datos pueden funcionar en modo en caché o en modo no en caché.  En el caso de la operación de disco de datos en caché, el modo de caché del host está establecido en **ReadOnly** o **ReadWrite**.  En el caso de la operación de disco de datos no en caché, el modo de caché del host está definido en **None**.


* El ancho de banda de red máximo es el ancho de banda agregado máximo que se asigna por cada tipo de VM. El ancho de banda máximo proporciona una orientación a la hora de seleccionar el tipo de VM correcto a fin de garantizar la disponibilidad de la capacidad de red adecuada. Al cambiar a Bajo, Moderado, Alto y Muy alto, el rendimiento aumentará en consecuencia. El rendimiento de red real dependerá de muchos factores (como, por ejemplo, las cargas de la red y de la aplicación y la configuración de red de la aplicación).


## <a name="a-series"></a>Serie A

| Tamaño        | Núcleos de CPU | Memoria: GiB | HDD local: GiB | Discos de datos máx. | Rendimiento de discos de datos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0,768        | 20 |                    | 1              | 1x500              | 1 / bajo                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / moderado              |
| Standard_A2 | 2         | 3,5 GB       | 135                   | 4              | 4x500              | 1 / moderado              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8x500              | 2 / alto                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16x500             | 4 / alto                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4x500              | 1 / moderado              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8x500              | 2 / alto                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16x500             | 4 / alto                  |

<br>
## <a name="a-series---compute-intensive-instances"></a>Serie A: instancias de proceso intensivo

Para información y consideraciones sobre el uso de estos tamaños, consulte [Acerca de las máquinas virtuales de la serie H y A de proceso intensivo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).



| Tamaño         | Núcleos de CPU | Memoria: GiB | HDD local: GiB | Discos de datos máx. | Rendimiento de discos de datos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8*  | 8         | 56           | 382                   | 16             | 16x500             | 2 / alto                  |
| Standard_A9* | 16        | 112          | 382                   | 16             | 16x500             | 4 / muy alto             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16x500             | 2 / alto                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16x500             | 4 / muy alto             |

*Compatible con RDMA

<br>
## <a name="d-series"></a>Serie D


| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de discos de datos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3,5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4x500              | 2 / alto                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8x500              | 4 / alto                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16x500             | 8 / alto                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4x500              | 2 / alto                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8x500              | 4 / alto                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16x500             | 8 / alto                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32x500             | 8 / muy alto             |

<br>
## <a name="dv2-series"></a>Serie Dv2

| Tamaño            | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de discos de datos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3,5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4x500              | 2 / alto                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8x500              | 4 / alto                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16x500             | 8 / alto                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32x500             | 8 / extremadamente alto        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4x500              | 2 / alto                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8x500              | 4 / alto                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16x500             | 8 / alto                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32x500             | 8 / extremadamente alto        |
| Standard_D15_v2 | 20 |        | 140          | 1000                | 40             | 40 x 500             | 8 / extremadamente alto        |

<br>
## <a name="ds-series*"></a>Serie DS*


| Tamaño          | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché máx.: E/S por segundo / Mbps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
|---------------|-----------|--------------|--------------------------------|----------------|--------------------------------------------|----------------------------------------------|-----------------------|
| Standard_DS1  | 1   | 3,5          | 7       | 2     | 4000 / 32 (43)               | 3200 / 32    | 1 / moderado              |
| Standard_DS2  | 2   | 7            | 14      | 4     | 8000 / 64 (86)               | 6400 / 64    | 2 / alto                  |
| Standard_DS3  | 4   | 14           | 28      | 8     | 16 000 / 128 (172)            | 12 800 / 128  | 4 / alto                  |
| Standard_DS4  | 8   | 28           | 56      | 16    | 32 000 / 256 (344)            | 25 600 / 256  | 8 / alto                  |
| Standard_DS11 | 2   | 14           | 28      | 4     | 8000 / 64 (72)               | 6400 / 64    | 2 / alto                  |
| Standard_DS12 | 4   | 28           | 56      | 8     | 16 000 / 128 (144)            | 12 800 / 128  | 4 / alto                  |
| Standard_DS13 | 8   | 56           | 112     | 16    | 32 000 / 256 (288)            | 25 600 / 256  | 8 / alto                  |
| Standard_DS14 | 16  | 112          | 224     | 32    | 64 000 / 512 (576)            | 51 200 / 512  | 8 / muy alto             |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie DS puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)



<br>
## <a name="dsv2-series*"></a>Serie DSv2*


| Tamaño             | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché máx.: E/S por segundo / Mbps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
|------------------|-----------|--------------|---------------------------|----------------|-------------------------------------------------|-------------------------------------------------|------------------------------|
| Standard_DS1_v2  | 1         | 3,5          | 7                         | 2              | 4000 / 32 (43)                        | 3200 / 48                                 | 1 moderado                   |
| Standard_DS2_v2  | 2         | 7            | 14                        | 4              | 8000 / 64 (86)                        | 6400 / 96                                 | 2 alto                       |
| Standard_DS3_v2  | 4         | 14           | 28                        | 8              | 16 000 / 128 (172)                     | 12 800 / 192                               | 4 alto                       |
| Standard_DS4_v2  | 8         | 28           | 56                        | 16             | 32 000 / 256 (344)                     | 25 600 / 384                               | 8 alto                       |
| Standard_DS5_v2  | 16        | 56           | 112                       | 32             | 64 000 / 512 (688)                     | 51 200 / 768                               | 8 extremadamente alto             |
| Standard_DS11_v2 | 2         | 14           | 28                        | 4              | 8000 / 64 (72)                        | 6400 / 96                                 | 2 alto                       |
| Standard_DS12_v2 | 4         | 28           | 56                        | 8              | 16 000 / 128 (144)                     | 12 800 / 192                               | 4 alto                       |
| Standard_DS13_v2 | 8         | 56           | 112                       | 16             | 32 000 / 256 (288)                     | 25 600 / 384                               | 8 alto                       |
| Standard_DS14_v2 | 16        | 112          | 224                       | 32             | 64 000 / 512 (576)                     | 51 200 / 768                               | 8 extremadamente alto             |
| Standard_DS15_v2 | 20        | 140 GB       | 280                       | 40             | 80 000 / 640 (720)                     | 64 000 / 960                               | 8 extremadamente alto             |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie DSv2 puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)


<br>
## <a name="f-series"></a>Serie F


| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de discos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_F1  | 1         | 2            | 16                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_F2  | 2         | 4            | 32                   | 4              | 4x500              | 2 / alto                  |
| Standard_F4  | 4         | 8            | 64                   | 8              | 8x500              | 4 / alto                  |
| Standard_F8  | 8         | 16           | 128                  | 16             | 16x500             | 8 / alto                  |
| Standard_F16 | 16        | 32           | 256                  | 32             | 32x500             | 8 / extremadamente alto        |

<br>
## <a name="fs-series*"></a>Serie Fs*

| Tamaño             | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché máx.: E/S por segundo / Mbps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
|---------------|-------|-----|----------|--------|------------------------------|---------------------------------|---------------|
| Standard_F1s  | 1     | 2   | 4        | 2      | 4000 / 32 (12)         | 3200 / 48        | 1 / moderado       |
| Standard_F2s  | 2     | 4   | 8        | 4      | 8000 / 64 (24)         | 6400 / 96        | 2 / alto           |
| Standard_F4s  | 4     | 8   | 16       | 8      | 16 000 / 128 (48)       | 12 800 / 192      | 4 / alto           |
| Standard_F8s  | 8     | 16  | 32       | 16     | 32 000 / 256 (96)       | 25 600 / 384      | 8 / alto           |
| Standard_F16s | 16    | 32  | 64       | 32     | 64 000 / 512 (192)      | 51 200 / 768      | 8 / extremadamente alto |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie Fs puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)


<br>
## <a name="g-series"></a>Serie G

| Tamaño        | Núcleos de CPU | Memoria: GiB  | SSD local: GiB  | Discos de datos máx. | Rendimiento de discos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / alto                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / alto                  |
| Standard_G3 | 8         | 112          | 1536                | 16             | 16 x 500           | 4 / muy alto             |
| Standard_G4 | 16        | 224          | 3072                | 32             | 32 x 500           | 8 / extremadamente alto        |
| Standard_G5 | 32        | 448          | 6144                | 64             | 64 x 500           | 8 / extremadamente alto        |



<br>
## <a name="gs-series*"></a>Serie GS*


| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché máx.: E/S por segundo / Mbps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
|--------------|-----------|--------------|---------------------------|--------------------------------|----------------|--------------------------------------------|----------------------------------------------|-----------------------|
| Standard_GS1 | 2         | 28      | 56       | 4   | 10 000 / 100 (264)       | 5000 / 125     | 1 / alto                  |
| Standard_GS2 | 4         | 56      | 528      | 8   | 20 000 / 200 (528)       | 10 000 / 250    | 2 / alto                  |
| Standard_GS3 | 8         | 112     | 1056    | 16  | 40 000 / 400 (1056)     | 20 000 / 500    | 4 / muy alto             |
| Standard_GS4 | 16        | 224     | 2112    | 32  | 80 000 / 800 (2112)     | 40 000 / 1000  | 8 / extremadamente alto        |
| Standard_GS5 | 32        | 448     | 4224    | 64  | 160 000 / 1600 (4224)  | 80 000 / 2000  | 8 / extremadamente alto        |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie GS puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados. 

<br>
## <a name="h-series"></a>Serie H

Las máquinas virtuales de la serie H son las de próxima generación informática de alto rendimiento que abordan las necesidades informáticas de gama alta, como el modelado molecular y la dinámica de fluidos computacional. Estas máquinas virtuales de 8 y 16 núcleos se basan en la tecnología de procesador Intel Haswell E5-2667 V3 con memoria DDR4 y almacenamiento basado en SSD local. 

Además de una potencia de CPU notable, la serie H ofrece varias opciones para las redes RDMA de baja latencia con FDR InfiniBand y varias configuraciones de memoria para admitir requisitos computacionales de uso intensivo de la memoria.


| Tamaño           | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de discos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / alto                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muy alto                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / alto                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muy alto                 |
| Standard_H16r* | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muy alto                  |
| Standard_H16mr* | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muy alto                  |


*Compatible con RDMA

<br>
## <a name="n-series-(preview)"></a>Serie N (versión preliminar)

Los tamaños de NC y NV también se conocen como instancias habilitadas para GPU. Se trata de máquinas virtuales especializadas que incluyen tarjetas GPU de NVIDIA, optimizadas para diferentes escenarios y casos de uso. Los tamaños de NV están optimizados y diseñados para la visualización remota, streaming, juegos, codificación y escenarios VDI mediante marcos como OpenGL y DirectX. Los tamaños de NC están más optimizados para las aplicaciones de proceso y red intensivos, algoritmos, incluidas simulaciones y aplicaciones basadas en CUDA y OpenCL. 


### <a name="nv-instances"></a>Instancias de NV
Las instancias de NV disponen de tecnología de GPU Tesla M60 de NVIDIA y NVIDIA GRID para aplicaciones aceleradas de escritorio y escritorios virtuales donde los clientes podrán visualizar sus datos o simulaciones. Los usuarios podrán visualizar sus flujos de trabajo con muchos gráficos en las instancias de NV para obtener una excelente capacidad gráfica y ejecutar además cargas de trabajo de precisión sencilla como la codificación y la representación. Tesla M60 ofrece 4096 núcleos CUDA con un diseño de GPU dual con hasta 36 secuencias de H.264 1080p.


| Tamaño          | Núcleos de CPU | Memoria: GiB  | SSD local: GiB | GPU            |
|---------------|-----------|--------------|---------------------------|----------------|
| Standard_NV6  | 6         | 56           | 380                       | 1 x NVIDIA M60 |
| Standard_NV12 | 12        | 112          | 680                       | 2 x NVIDIA M60 |
| Standard_NV24 | 24        | 224          | 1440                      | 4 x NVIDIA M60 |



### <a name="nc-instances"></a>Instancias de NC

Las instancias de NC disponen de tecnología Tesla K80 de NVIDIA. Los usuarios ahora pueden trabajar con datos con mayor rapidez aprovechando CUDA para las aplicaciones de exploración de energía, simulaciones de accidentes, representación de trazado de rayos, aprendizaje profundo y mucho más. Tesla K80 ofrece 4992 núcleos CUDA con un diseño de GPU dual, hasta 2,91 Teraflops de doble precisión y hasta 8,93 Teraflops de rendimiento de precisión sencilla. 


| Tamaño          | Núcleos de CPU | Memoria: GiB  | SSD local: GiB  | GPU            |
|---------------|-----------|--------------|---------------------------|----------------|
| Standard_NC6  | 6         | 56           | 380                       | 1 x NVIDIA K80 |
| Standard_NC12 | 12        | 112          | 680                       | 2 x NVIDIA K80 |
| Standard_NC24 | 24        | 224          | 1440                      | 4 x NVIDIA K80 |



<br>
## <a name="notes:-standard-a0---a4-using-cli-and-powershell"></a>Notas: Standard_A0 - A4 con CLI y PowerShell 


En el modelo de implementación clásica, algunos nombres de tamaños de VM varían ligeramente en la CLI y en PowerShell:

* Standard_A0 es ExtraSmall 
* Standard_A1 es Small
* Standard_A2 es Medium
* Standard_A3 es Large
* Standard_A4 es ExtraLarge


## <a name="next-steps"></a>Pasos siguientes

- Conozca los [límites, cuotas y restricciones de suscripción y servicios de Azure](../articles/azure-subscription-service-limits.md).
- Más información [cerca de las máquinas virtuales de la serie H y A de proceso intensivo](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) para cargas de trabajo, como la informática de alto rendimiento (HPC).





<!--HONumber=Oct16_HO2-->


