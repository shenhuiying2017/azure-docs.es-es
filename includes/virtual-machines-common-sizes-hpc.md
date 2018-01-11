
Los tamaños A8-A11 los de la serie H también se conocen como *instancias de proceso intensivo*. El hardware que ejecuta estos tamaños está diseñado y optimizado para aplicaciones de proceso intensivo que consumen muchos recursos de red, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HPC), el modelado y las simulaciones. La serie A8-A11 utiliza Intel Xeon E5-2670 a 2,6 GHz y la serie H, Intel Xeon E5-2667 v3 a 3,2 GHz.  En este artículo, se proporciona información acerca del número de vCPU, discos de datos y tarjetas de interfaz de red, así como del rendimiento del almacenamiento y del ancho de banda de red para cada tamaño de esta agrupación. 

Las máquinas virtuales de la serie H son las de próxima generación informática de alto rendimiento que abordan las necesidades informáticas de gama alta, como el modelado molecular y la dinámica de fluidos computacional. Estas VM de 8 y 16 vCPU se basan en la tecnología de procesador Intel Haswell E5-2667 V3 con memoria DDR4 y almacenamiento temporal basado en SSD. 

Además de una potencia de CPU notable, la serie H ofrece varias opciones para las redes RDMA de baja latencia con FDR InfiniBand y varias configuraciones de memoria para admitir requisitos computacionales de uso intensivo de la memoria.



## <a name="h-series"></a>Serie H

ACU: 290-300

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Discos de datos máx. | Rendimiento de discos máx.: E/S por segundo | Nº máx. NIC |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> Para las aplicaciones MPI, la red de back-end RDMA dedicada está habilitada por la red InfiniBand FDR, que ofrece una latencia sumamente baja y un alto ancho de banda.

<br>



## <a name="a-series---compute-intensive-instances"></a>Serie A: instancias de proceso intensivo

ACU: 225

| Tamaño | vCPU | Memoria: GiB | Almacenamiento temporal (HDD): GiB | Discos de datos máx. | Rendimiento de discos de datos máx.: E/S por segundo | Nº máx. NIC|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>Para las aplicaciones MPI, la red de back-end RDMA dedicada está habilitada por la red InfiniBand FDR, que ofrece una latencia sumamente baja y un alto ancho de banda.

<br>



