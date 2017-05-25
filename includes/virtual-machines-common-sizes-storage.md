
La serie Ls está optimizada para las cargas de trabajo que requieren almacenamiento local de baja latencia, como bases de datos NoSQL (por ejemplo, Cassandra, MongoDB, Cloudera y Redis). La serie LS ofrece hasta 32 núcleos de CPU, con el [procesador Intel® Xeon® de la familia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Se trata del mismo rendimiento de CPU de las series G/GS y viene con 8 GiB de memoria por núcleo de CPU.  

## <a name="ls-series"></a>Serie Ls

ACU: 180-240
 
| Tamaño          | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de disco en caché y local máx.: E/S por segundo / MBps (tamaño de caché en GiB) | Rendimiento de disco no en caché máx.: E/S por segundo / Mbps | Ancho de banda de red/NIC máx. | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | ND / ND (0)          | 5000 / 125                               | 2 / alto       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | ND / ND (0)          | 10 000 / 250                              | 4 / muy alto  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | ND / ND (0)          | 20 000 / 500                              | 8 / extremadamente alto | 
| Standard_L32s** | 32 | 256  | 5,630 | 64             | ND / ND (0)          | 40 000 / 1000                            | 8 / extremadamente alto | 
 
MBps = 10^6 bytes por segundo y GiB = 1024^3 bytes. 

*El rendimiento de disco máx. (E/S por segundo o MBps) posible con una máquina virtual de la serie Ls puede estar limitado por el número, el tamaño y la fragmentación de los discos conectados. Para obtener más información, consulte [Almacenamiento Premium: almacenamiento de alto rendimiento para las cargas de trabajo de la máquina virtual de Azure](../articles/storage/storage-premium-storage.md) 

**La instancia está aislada en el hardware dedicado a un solo cliente.