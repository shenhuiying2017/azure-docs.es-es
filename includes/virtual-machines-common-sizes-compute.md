<!-- F-series, Fs-series* -->

Los tamaños de máquina virtual optimizados para el proceso tiene un alto ratio entre CPU y memoria, y son adecuados para servidores web de tráfico medios, aplicaciones de red, procesos por lotes y servidores de aplicaciones. En este artículo, se proporciona información sobre el número de vCPU, discos de datos y NIC, así como sobre el ancho de banda de red y almacenamiento para cada tamaño de esta agrupación.

La serie Fsv2 usa el procesador Intel® Xeon® Platinum 8168, que ofrece una frecuencia de núcleo base de 2,7 GHz con una frecuencia turbo máxima de un solo núcleo individual de 3,7 GHz. Las instrucciones de Intel® AVX-512, que son nuevas en los procesadores de Intel escalables, pueden llegar a duplicar el rendimiento en las cargas de trabajo de procesamiento vectorial en las operaciones de número de punto flotante de precisión individual y doble. En otras palabras, son muy rápidos para cualquier carga de trabajo de cálculo. 

Dado que su precio por hora es inferior, la serie Fsv2 tiene la mejor relación precio/rendimiento de la cartera de Azure según la unidad de proceso de Azure (ACU) por vCPU. 

La serie F se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, que puede alcanzar velocidades de reloj de hasta 3,1 GHz con Intel Turbo Boost Technology 2.0. Este es el mismo rendimiento de CPU que el de la serie Dv2 de máquinas virtuales.  

Las máquinas virtuales de la serie F son una opción excelente para cargas de trabajo que exigen CPU más rápidas, pero que no necesitan tanta memoria o almacenamiento temporal por vCPU.  Las cargas de trabajo, como análisis, servidores de juegos, servidores web y procesamiento por lotes se beneficiarán del valor de la serie F.

La serie Fs proporciona todas las ventajas de la serie F, además de Premium Storage.

## <a name="fsv2-series-sup1sup"></a>Serie Fsv2 <sup>1</sup>

ACU: 195 - 210

| Tamaño             | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de almacenamiento temporal en caché y máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | Moderado                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | Moderado                                       |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | Alto                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | Alto                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | Extremadamente alto                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | Extremadamente alto                                 |
| Standard_F72s_v2<sup>2</sup> | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | Extremadamente alto                                 |

<sup>1</sup>Las VM de la serie 1Fsv2 cuentan con la tecnología Hyper-Threading de Intel®

<sup>2</sup> Si hay más de 64 vCPU, se necesita uno de estos sistemas operativos invitados compatibles: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2, Red Hat Enterprise, CentOS 7.3 y Oracle Linux 7.3 con LIS 4.2.1

## <a name="fs-series-sup1sup"></a>Serie Fs <sup>1</sup>

ACU: 210 - 250

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de almacenamiento temporal en caché y máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12 000 |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

<sup>1</sup> El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie Fs puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Premium Storage: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/virtual-machines/windows/premium-storage.md)


<br>

## <a name="f-series"></a>Serie F

ACU: 210 - 250

| Tamaño         | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Rendimiento máximo del almacenamiento temporal: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12 000           |


<br>


