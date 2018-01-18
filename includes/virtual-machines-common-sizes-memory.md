
Los tamaños de VM optimizadas para memoria ofrecen una relación alta de memoria a CPU que es excelente para servidores de bases de datos relacionales, memorias caché de medianas a grandes y análisis en memoria. En este artículo, se proporciona información acerca del número de vCPU, discos de datos y tarjetas de interfaz de red, así como del rendimiento del almacenamiento y del ancho de banda de red para cada tamaño de esta agrupación. 

* La serie M ofrece el mayor número de vCPU (hasta 128 vCPU) y la memoria más grande (hasta 3,8 TiB) de todas las VM en la nube.  Es ideal para bases de datos extremadamente grandes u otras aplicaciones que se benefician de un elevado número de vCPU y grandes cantidades de memoria.

* Las series Dv2, D, G y DS/GS son ideales para las aplicaciones que requieren vCPU más rápidas, mejor rendimiento del almacenamiento temporal o tienen mayor demanda de memoria.  Ofrecen una combinación eficaz para muchas aplicaciones de clase empresarial.

* Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a vCPU y una unidad de estado sólido (SSD) para el almacenamiento temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.


## <a name="esv3-series-sup1sup"></a>Serie Esv3 <sup>1</sup>

ACU: 160-190

Las instancias de la serie ESv3 se basan en el procesador Intel XEON® E5-2673 v4 (Broadwell) de 2,3 GHz y pueden llegar a 3,5 GHz con la tecnología Intel Turbo Boost Technology 2.0 y el uso de Premium Storage. Las instancias de la serie Ev3 son ideales para aplicaciones empresariales de uso intensivo de memoria.


| Tamaño             | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de almacenamiento temporal en caché y máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3  | 2      | 16          | 32             | 4              | 4 000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3  | 4      | 32          | 64             | 8              | 8 000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_E8s_v3  | 8      | 64          | 128            | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3 | 16     | 128         | 256            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8000                                       |
| Standard_E32s_v3 <sup>2</sup> | 32     | 256         | 512            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16 000                             |
| Standard_E64s_v3 <sup>2</sup> | 64     | 432         | 864            | 32             | 128 000/1024 (1600)                                                   | 80 000 / 1200                             | 8 / 30 000                             |

<sup>1</sup> Las VM de la serie Esv3 cuentan con la tecnología Hyper-Threading de Intel®<sup>2</sup> Tamaños de núcleos restringidos disponibles 

## <a name="ev3-series-sup1sup"></a>Serie Ev3 <sup>1</sup>

ACU: 160 - 190 

Las instancias de la serie Ev3 se basan en el procesador Intel XEON ® E5-2673 v4 (Broadwell) de 2,3 GHz y pueden llegar a 3,5 GHz con la tecnología Intel Turbo Boost Technology 2.0. Las instancias de la serie Ev3 son ideales para aplicaciones empresariales de uso intensivo de memoria.

El almacenamiento en disco de datos se factura de forma independiente a las máquinas virtuales. Para usar discos de Premium Storage, utilice los tamaños ESv3. Los precios y los medidores de facturación para los tamaños ESv3 son los mismos que para la serie Ev3. 


| Tamaño            | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo del almacenamiento temporal: E/S por segundo / MBps de lectura / MBps de escritura | Ancho de banda de red/NIC máx. |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16 000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30 000           |

<sup>1</sup> Las máquinas virtuales de la serie Ev3 cuentan con la tecnología Hyper-Threading de Intel®

## <a name="m-series-sup1sup"></a>Serie M <sup>1</sup>

ACU: 160-180

| Tamaño            | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de almacenamiento temporal en caché y máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M64s  | 64   | 1024        | 2048           | 64             | 80,000 / 800 (6348)       | 40 000 / 1000                            | 8 / 16000          |
| Standard_M64ms  | 64   | 1792        | 2048           | 64             | 80,000 / 800 (6348)       | 40 000 / 1000                            | 8 / 16000          |
| Standard_M128s <sup>2. 3</sup> | 128  | 2048        | 4096           | 64             | 160,000 / 1,600 (12,696) | 80 000 / 2000                            | 8 / 30 000          |
| Standard_M128ms <sup>2. 3</sup> | 128  | 3800        | 4096           | 64             | 160,000 / 1,600 (12,696) | 80 000 / 2000                            | 8 / 30 000          |

<sup>1</sup> Las VM de la serie M cuentan con la tecnología Hyper-Threading de Intel®

<sup>2</sup> Si hay más de 64 vCPU, se necesita uno de estos sistemas operativos invitados compatibles: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2, Red Hat Enterprise, CentOS 7.3 y Oracle Linux 7.3 con LIS 4.2.1 

<sup>3</sup> Tamaños de núcleos restringidos disponibles.

<br>

## <a name="gs-series-sup1sup"></a>Serie GS <sup>1</sup>

ACU: 180 - 240

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de almacenamiento temporal en caché y máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40 000 / 400 (1056) |20 000 / 500 |4 / 8000 |
| Standard_GS4 <sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2112) |40 000 / 1000 |8 / 16000 |
| Standard_GS5 <sup>2, 3</sup> |32 |448 |896 |64 |160 000 / 1600 (4224) |80 000 / 2000 |8 / 20000 |

<sup>1</sup> El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie GS puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados. Para obtener más información, consulte [Premium Storage: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/virtual-machines/windows/premium-storage.md) 

<sup>2</sup> La instancia está aislada en el hardware dedicado a un solo cliente.

<sup>3</sup> Tamaños de núcleos restringidos disponibles 

<br>

## <a name="g-series"></a>Serie G

ACU: 180 - 240

| Tamaño         | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Rendimiento máximo del almacenamiento temporal: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard_G5 <sup>1</sup> | 32        | 448         | 6144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> La instancia está aislada en el hardware dedicado a un solo cliente.
<br>


## <a name="dsv2-series-sup1sup"></a>Serie DSv2 <sup>1</sup>

ACU: 210 - 250

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de almacenamiento temporal en caché y máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1500 |
| Standard_DS12_v2 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 192 |4 / 3000 |
| Standard_DS13_v2 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 384 |8 / 6000 |
| Standard_DS14_v2 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 768 |8 / 12 000 |
| Standard_DS15_v2 <sup>2</sup> |20  |140 |280 |64 |80 000 / 640 (720) |64 000 / 960 |8 / 25 000 <sup>3</sup>

<sup>1</sup> El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie DSv2 puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Premium Storage: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/virtual-machines/windows/premium-storage.md)

<sup>2</sup> Una instancia es un nodo aislado que garantiza que la VM sea la única VM en nuestro nodo Intel Haswell.

<sup>3</sup> 25 000 Mbps con redes aceleradas.

<br>

## <a name="dv2-series"></a>Serie Dv2

ACU: 210 - 250

| Tamaño              | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Rendimiento máximo del almacenamiento temporal: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12 000          |
| Standard_D15_v2 <sup>1</sup> | 20         | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25 000 <sup>2</sup> |

<sup>1</sup> Una instancia es un nodo aislado que garantiza que la VM sea la única VM en nuestro nodo Intel Haswell.

<sup>2</sup> 25 000 Mbps con redes aceleradas.

<br>

## <a name="ds-series-sup1sup"></a>Serie DS <sup>1</sup>

ACU: 160

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de almacenamiento temporal en caché y máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8000 / 64 (72) |6400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie DS puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Premium Storage: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/virtual-machines/windows/premium-storage.md)


## <a name="d-series"></a>Serie D

ACU: 160

| Tamaño         | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Rendimiento máximo del almacenamiento temporal: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Nº máx. de NIC/ancho de banda de red esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<br>

