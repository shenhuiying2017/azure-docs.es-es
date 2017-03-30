Los tamaños de NC y NV también se conocen como instancias habilitadas para GPU. Se trata de máquinas virtuales especializadas que incluyen tarjetas GPU de NVIDIA, optimizadas para diferentes escenarios y casos de uso. Los tamaños de NV están optimizados y diseñados para la visualización remota, streaming, juegos, codificación y escenarios VDI mediante marcos como OpenGL y DirectX. Los tamaños de NC están más optimizados para las aplicaciones de uso intensivo de procesos y red, así como algoritmos, incluidas simulaciones y aplicaciones basadas en CUDA y OpenCL. 


Las instancias de NV disponen de tarjetas GPU Tesla M60 de NVIDIA y NVIDIA GRID para aplicaciones aceleradas de escritorio y escritorios virtuales donde los clientes podrán visualizar sus datos o simulaciones. Los usuarios podrán visualizar sus flujos de trabajo con muchos gráficos en las instancias de NV para obtener una excelente capacidad gráfica y ejecutar además cargas de trabajo de precisión sencilla como la codificación y la representación. Tesla M60 ofrece 4096 núcleos CUDA con un diseño de GPU dual con hasta 36 secuencias de H.264 1080p. 

Las instancias de NC disponen de una tarjeta Tesla K80 de NVIDIA. Los usuarios ahora pueden trabajar con datos con mayor rapidez aprovechando CUDA para las aplicaciones de exploración de energía, simulaciones de accidentes, representación de trazado de rayos, aprendizaje profundo y mucho más. Tesla K80 ofrece 4992 núcleos CUDA con un diseño de GPU dual, hasta 2,91 Teraflops de doble precisión y hasta 8,93 Teraflops de rendimiento de precisión sencilla.

## <a name="nv-instances"></a>Instancias de NV

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = media tarjeta M60.

**Sistemas operativos compatibles**

* Windows Server 2016 o Windows Server 2012 R2: consulte la [configuración del controlador de la serie N para Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)

## <a name="nc-instances"></a>Instancias de NC

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = media tarjeta K80.

*Compatible con RDMA

**Sistemas operativos compatibles**

* Windows Server 2016 o Windows Server 2012 R2: consulte la [configuración del controlador de la serie N para Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)
* Ubuntu 16.04 LTS: consulte la [configuración del controlador de la serie N para Linux](../articles/virtual-machines/virtual-machines-linux-n-series-driver-setup.md)

<br>

