Los tamaños de VM optimizadas para almacenamiento proporcionan un alto rendimiento de disco y de E/S, y son ideales para macrodatos, SQL y bases de datos NoSQL. En este artículo, se proporciona información acerca del número de vCPU, discos de datos y tarjetas de interfaz de red, así como del rendimiento del almacenamiento y del ancho de banda de red para cada tamaño de esta agrupación. 

La serie LS ofrece hasta 32 vCPU, con el [procesador Intel® Xeon® de la familia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie LS obtiene el mismo rendimiento de CPU que las series G/GS y viene con 8 GiB de memoria por vCPU.  

## <a name="ls-series"></a>Serie Ls

ACU: 180-240
 
| Tamaño          | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento máximo de almacenamiento temporal: IOPS/MBps | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Nº máx. de NIC/ancho de banda de red esperado (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 8    | 20 000/200   | 10 000/250        | 2/4000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 16   | 40 000/400   | 20 000/500       | 4/8000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 32   | 90 000/800   | 10 000/1000       | 8/6000 - 16 000 &#8224; | 
| Standard_L32s* | 32   | 256  | 5,630 | 64   | 160 000/1600   | 90 000/2 000     | 8/20 000 | 
 

El rendimiento máximo de disco que es posible con una máquina virtual de la serie Ls puede estar limitado por el número, el tamaño y la fragmentación de cualquier disco asociado. Para obtener más información, consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](../articles/virtual-machines/windows/premium-storage.md). 

*La instancia está aislada en el hardware dedicado a un solo cliente.

