
<!-- A-series, Av2-series, D-series, Dv2-series, DS-series*, DSv2-series* -->

Las máquinas virtuales de las series A y Av2 se pueden implementar en diversos procesadores y tipos de hardware. Según el hardware, el tamaño es una limitación para ofrecer un rendimiento coherente del procesador para la instancia en ejecución, independientemente del hardware en que se implementó. Con el fin de determinar el hardware físico en que se implementó este tamaño, cree una consulta para el hardware virtual desde dentro de la máquina virtual.

Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.

Los tamaños de niveles básicos se utilizan sobre todo para cargas de trabajo de desarrollo y otras aplicaciones que no requieren equilibrio de carga, escalado automático o máquinas virtuales de uso intensivo de memoria. Para más información sobre los tamaños de máquinas virtuales más adecuados para las aplicaciones de producción, consulte (Tamaños de las máquinas virtuales) [virtual-machines-size-specs.md] y para obtener información sobre sus precios, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

> [!NOTE]
> A partir del 15 de abril todos los tamaños de las máquinas virtuales de Azure admitirán al menos 2 interfaces de red (NIC). Para información específica de la región esté atento a una [actualización del servicio](https://azure.microsoft.com/en-us/updates/). Se indica a continuación con un signo "^".

## <a name="dsv2-series"></a>Serie DSv2*

ACU: 210-250

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché máx.: E/S por segundo / Mbps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |2 |4000 / 32 (43) |3200 / 48 |2 ^ moderado |
| Standard_DS2_v2 |2 |7 |14 |4 |8000 / 64 (86) |6400 / 96 |2 alto |
| Standard_DS3_v2 |4 |14 |28 |8 |16 000 / 128 (172) |12 800 / 192 |4 alto |
| Standard_DS4_v2 |8 |28 |56 |16 |32 000 / 256 (344) |25 600 / 384 |8 alto |
| Standard_DS5_v2 |16 |56 |112 |32 |64 000 / 512 (688) |51 200 / 768 |8 extremadamente alto |



## <a name="dv2-series"></a>Serie Dv2

ACU: 210-250

| Tamaño              | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Rendimiento máximo del disco local: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Ancho de banda de red/NIC máx. |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2x500                         | 2 ^ / moderado                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / alto                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / alto                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / alto                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8 / extremadamente alto           |

<br>

## <a name="ds-series"></a>Serie DS*
| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché y local máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |2 |4000 / 32 (43) |3200 / 32 |2 ^ / moderado |
| Standard_DS2 |2 |7 |14 |4 |8000 / 64 (86) |6400 / 64 |2 / alto |
| Standard_DS3 |4 |14 |28 |8 |16 000 / 128 (172) |12 800 / 128 |4 / alto |
| Standard_DS4 |8 |28 |56 |16 |32 000 / 256 (344) |25 600 / 256 |8 / alto |

<br>
## <a name="d-series"></a>Serie D 

ACU: 160

| Tamaño         | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Rendimiento máximo del disco local: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Ancho de banda de red/NIC máx. |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2x500                         | 2 ^ / moderado                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2 / alto                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4 / alto                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8 / alto                     |

<br>


## <a name="av2-series"></a>Serie Av2

ACU: 100

| Tamaño            | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Rendimiento máximo del disco local: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Ancho de banda de red/NIC máx. |
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500                         | 2 ^ / moderado                 |
| Standard_A2_v2  | 2         | 4           | 20 |             | 2000 / 40 / 20                                           | 4 / 4x500                         | 2 / moderado                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500                         | 4 / alto                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500                       | 8 / alto                     |
| Standard_A2m_v2 | 2         | 16          | 20 |             | 2000 / 40 / 20                                           | 4 / 4X500                         | 2 / moderado                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500                         | 4 / alto                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500                       | 8 / alto                     |

<br>

## <a name="a-series"></a>Serie A

ACU: 50-100

| Tamaño | Núcleos de CPU | Memoria: GiB | HDD local: GiB | Discos de datos máx. | Rendimiento de discos de datos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0* |1 |0,768 |20 | |1 |1x500 |2 ^ / bajo |
| Standard_A1 |1 |1,75 |70 |2 |2 x 500 |2 ^ / moderado |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2 ^ / moderado |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / alto |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / alto |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 ^ / moderado |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / alto |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / alto |
<br>

*El tamaño A0 está sobresuscrito en el hardware físico. Solo en este tamaño específico, las implementaciones de otros clientes podrían afectar el rendimiento de la carga de trabajo en ejecución. A continuación, se indica el rendimiento relativo como la línea base esperada, sujeta a una variabilidad aproximada de 15 por ciento.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard_A0 - A4 con CLI y PowerShell
En el modelo de implementación clásica, algunos nombres de tamaños de VM varían ligeramente en la CLI y en PowerShell:

* Standard_A0 es ExtraSmall 
* Standard_A1 es Small
* Standard_A2 es Medium
* Standard_A3 es Large
* Standard_A4 es ExtraLarge

## <a name="basic-a"></a>A básico

|Tamaño – Tamaño\nombre |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máximo del disco temporal |Máx. discos de datos (1023 GB cada uno)|Máx. E/S (300 por disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2^| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2^| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2^| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2^| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2^| 240 GB |16|16x300|
