Los tamaños de VM optimizadas para almacenamiento proporcionan un alto rendimiento de disco y de E/S, y son ideales para macrodatos, SQL y bases de datos NoSQL. En este artículo, se proporciona información acerca del número de vCPU, discos de datos y tarjetas de interfaz de red, así como del rendimiento del almacenamiento y del ancho de banda de red para cada tamaño de esta agrupación. 

La serie LS ofrece hasta 32 vCPU, con el [procesador Intel® Xeon® de la familia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie LS obtiene el mismo rendimiento de CPU que las series G/GS y viene con 8 GiB de memoria por vCPU.  

## <a name="ls-series"></a>Serie Ls

ACU: 180-240
 
| Tamaño          | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS/MBps | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20 000/200   | 5000 / 125        | 2/4000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40 000/400   | 10 000 / 250       | 4/8000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80 000 / 800   | 20 000 / 500       | 8 / 16 000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160 000/1600   | 40 000 / 1000     | 8/20 000 | 
 

El rendimiento máximo de disco que es posible con una máquina virtual de la serie Ls puede estar limitado por el número, el tamaño y la fragmentación de cualquier disco asociado. Para obtener más información, consulte [Premium Storage: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/virtual-machines/windows/premium-storage.md) 

<sup>1</sup> La instancia está aislada en el hardware dedicado a un solo cliente.

## <a name="size-table-definitions"></a>Definiciones de tabla de tamaño

- La capacidad de almacenamiento se muestra en unidades de GiB o 1024^3 bytes. Cuando compare discos que se miden en GB (1000^3 bytes) con discos que se miden en GiB (1024^3), recuerde que los números que representan la capacidad en GiB pueden parecer más pequeños. Por ejemplo, 1023 GiB = 1098.4 GB
- Se midió el rendimiento de disco en operaciones de entrada/salida por segundo (E/S por segundo) y Mbps, donde Mbps = 10^6 bytes/s.
- Los discos de datos de la serie Ls no pueden funcionar en modo caché, el modo de caché del host debe estar establecido en **Ninguno**.
- Si desea obtener el mejor rendimiento para las VM, debe limitar el número de discos de datos a 2 discos por vCPU.
- **El ancho de banda de red esperado** es el [ancho de banda agregado máximo asignado por tipo de máquina virtual](../articles/virtual-network/virtual-machine-network-throughput.md) en todas las NIC y para todos los destinos. Los límites superiores no están garantizados pero están diseñados para proporcionar una guía a la hora de seleccionar el tipo correcto de máquina virtual para la aplicación deseada. El rendimiento de red real dependerá de diversos factores (como, por ejemplo, la congestión de la red, las cargas de la aplicación y la configuración de red). Para más información acerca de cómo optimizar el rendimiento de red, consulte [Optimización del rendimiento de red para Windows y Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para lograr el rendimiento de red esperado en Linux o Windows, puede que sea necesario seleccionar una versión específica u optimizar la máquina virtual. Para más información, consulte [Pruebas confiables para el rendimiento de máquinas virtuales](../articles/virtual-network/virtual-network-bandwidth-testing.md).