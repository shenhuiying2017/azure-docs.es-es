<!-- F-series, Fs-series* -->

La serie F se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, que puede alcanzar velocidades de reloj de hasta 3,1 GHz con Intel Turbo Boost Technology 2.0. Este es el mismo rendimiento de CPU que el de la serie Dv2 de máquinas virtuales.  Con un precio en lista inferior por hora, la serie F tiene la mejor relación precio/rendimiento en la cartera de Azure según en la unidad de proceso de Azure (ACU) por núcleo. 

Las máquinas virtuales de la serie F son una opción excelente para cargas de trabajo que exigen CPU más rápidas, pero que no necesitan tanta memoria o SSD local por núcleo de CPU.  Las cargas de trabajo, como análisis, servidores de juegos, servidores web y procesamiento por lotes se beneficiarán del valor de la serie F.

La serie Fs proporciona todas las ventajas de la serie F, además de Premium Storage.

## <a name="fs-series"></a>Serie Fs*

ACU: 210 - 250

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché y local máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4000 / 32 (12) |3200 / 48 |1 / moderado |
| Standard_F2s |2 |4 |8 |4 |8000 / 64 (24) |6400 / 96 |2 / alto |
| Standard_F4s |4 |8 |16 |8 |16 000 / 128 (48) |12 800 / 192 |4 / alto |
| Standard_F8s |8 |16 |32 |16 |32 000 / 256 (96) |25 600 / 384 |8 / alto |
| Standard_F16s |16 |32 |64 |32 |64 000 / 512 (192) |51 200 / 768 |8 / extremadamente alto |

MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes.

*El rendimiento de disco máx. (E/S por segundo o Mbps) posible con una VM de la serie Fs puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados.  Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md)

<br>
## <a name="f-series"></a>Serie F

ACU: 210 - 250

| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Rendimiento máximo del disco local: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Ancho de banda de red/NIC máx. |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 2 / 2x500                         | 1 / moderado                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / alto                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / alto                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / alto                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8 / extremadamente alto           |
<br>


