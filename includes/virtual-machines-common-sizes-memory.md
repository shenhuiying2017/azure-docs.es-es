

* Las series Dv2, D, G y DS/GS son ideales para las aplicaciones que requieren CPU más rápidas, mejor rendimiento de disco local, o tienen mayor demanda de memoria.  Ofrecen una combinación eficaz para muchas aplicaciones de clase empresarial.

Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.


## <a name="gs-series"></a>Serie GS*

ACU: 180 - 240

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché y local máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10 000 / 100 (264) |5000 / 125 |2 / alto |
| Standard_GS2 |4 |56 |112 |8 |20 000 / 200 (528) |10 000 / 250 |2 / alto |
| Standard_GS3 |8 |112 |224 |16 |40 000 / 400 (1056) |20 000 / 500 |4 / muy alto |
| Standard_GS4 |16 |224 |448 |32 |80 000 / 800 (2112) |40 000 / 1000 |8 / extremadamente alto |
| Standard_GS5** |32 |448 |896 |64 |160 000 / 1600 (4224) |80 000 / 2000 |8 / extremadamente alto |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie GS puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md) 

**La instancia está aislada en el hardware dedicado a un solo cliente.
<br>

## <a name="g-series"></a>Serie G

ACU: 180 - 240

| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Rendimiento máximo del disco local: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Ancho de banda de red/NIC máx. |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 4 / 4 x 500                       | 2 / alto                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 8 / 8 x 500                       | 2 / alto                     |
| Standard_G3  | 8         | 112         | 1536          | 24000 / 375 / 187                                        | 16 / 16 x 500                     | 4 / muy alto                |
| Standard_G4  | 16        | 224         | 3072          | 48000 / 750 / 375                                        | 32 / 32 x 500                     | 8 / extremadamente alto           |
| Standard_G5* | 32        | 448         | 6144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / extremadamente alto           |

*La instancia está aislada en el hardware dedicado a un solo cliente.
<br>


## <a name="dsv2-series"></a>Serie DSv2*

ACU: 210 - 250

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché y local máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 |2 |14 |28 |4 |8000 / 64 (72) |6400 / 96 |2 alto |
| Standard_DS12_v2 |4 |28 |56 |8 |16 000 / 128 (144) |12 800 / 192 |4 alto |
| Standard_DS13_v2 |8 |56 |112 |16 |32 000 / 256 (288) |25 600 / 384 |8 alto |
| Standard_DS14_v2 |16 |112 |224 |32 |64 000 / 512 (576) |51 200 / 768 |8 extremadamente alto |
| Standard_DS15_v2*** |20 | |140 |280 |40 |80 000 / 640 (720) |64 000 / 960 |8 extremadamente alto** |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie DSv2 puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)

** En algunas regiones, Accelerated Networking está disponible para el tamaño Standard_DS15_v2. Para más información acerca del uso y la disponibilidad, consulte [Accelerated Networking is in preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (Accelerated Networking está en la fase de versión preliminar) y [Accelerated Networking for a virtual machine](../articles/virtual-network/virtual-network-create-vm-accelerated-networking.md) (Accelerated Networking para una máquina virtual).

***La instancia está aislada en el hardware dedicado a un solo cliente.
<br>
Bps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie DS puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)

## <a name="dv2-series"></a>Serie Dv2

ACU: 210 - 250

| Tamaño              | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Rendimiento máximo del disco local: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Ancho de banda de red/NIC máx. |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / alto                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / alto                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / alto                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8 / extremadamente alto           |
| Standard_D15_v2** | 20 |        | 140         | 1000          | 60000 / 937 / 468                                        | 40 / 40x500                       | 8 / extremadamente alto*          |

* En algunas regiones, Accelerated Networking está disponible para el tamaño Standard_D15_v2. Para más información acerca del uso y la disponibilidad, consulte [Accelerated Networking is in preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (Accelerated Networking está en la fase de versión preliminar) y [Accelerated Networking for a virtual machine](../articles/virtual-network/virtual-network-create-vm-accelerated-networking.md) (Accelerated Networking para una máquina virtual).

**La instancia está aislada en el hardware dedicado a un solo cliente.

<br>

## <a name="ds-series"></a>Serie DS*

ACU: 160

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché y local máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |4 |8000 / 64 (72) |6400 / 64 |2 / alto |
| Standard_DS12 |4 |28 |56 |8 |16 000 / 128 (144) |12 800 / 128 |4 / alto |
| Standard_DS13 |8 |56 |112 |16 |32 000 / 256 (288) |25 600 / 256 |8 / alto |
| Standard_DS14 |16 |112 |224 |32 |64 000 / 512 (576) |51 200 / 512 |8 / muy alto |


MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie DS puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)

## <a name="d-series"></a>Serie D

ACU: 160

| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Rendimiento máximo del disco local: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Ancho de banda de red/NIC máx. |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / alto                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / alto                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / alto                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8 / muy alto                |
<br>


<br>

