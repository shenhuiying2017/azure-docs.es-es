
Los tamaños de máquina virtual optimizada para GPU son máquinas virtuales especializadas con GPU de NVIDIA. Estos tamaños están diseñados para cargas de trabajo de proceso intensivo, uso intensivo de gráficos y visualización. En este artículo, se proporciona información acerca del número y tipo de GPU, vCPU, discos de datos y tarjetas de interfaz de red, así como del rendimiento del almacenamiento y del ancho de banda de red para cada tamaño de esta agrupación. 

* **NC, NCv2, y ND**, son tamaños están optimizados para las aplicaciones de uso intensivo de procesos y red, así como algoritmos, incluidas aplicaciones basadas en CUDA y OpenCL y simulaciones de inteligencia artificial y aprendizaje profundo. 
* **NV**, los tamaños están optimizados y diseñados para la visualización remota, streaming, juegos, codificación y escenarios VDI mediante marcos como OpenGL y DirectX.  


## <a name="nc-instances"></a>Instancias de NC

Las instancias de NC disponen de una tarjeta [Tesla K80 de NVIDIA](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf). Los usuarios pueden trabajar con datos con mayor rapidez aprovechando CUDA para las aplicaciones de exploración de energía, simulaciones de accidentes, representación de trazado de rayos, aprendizaje profundo y mucho más. La configuración NC24r proporciona una interfaz de red de baja latencia y alto rendimiento optimizada para cargas de trabajo de computación paralelas estrechamente unidas.


| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | GPU | Número máximo de discos de datos |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = media tarjeta K80.

*Compatible con RDMA

## <a name="ncv2-instances"></a>Instancias de NCv2

Las instancias de NCv2 son la próxima generación de las máquinas de la serie NC, que utilizan GPU [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf). Estas GPU pueden duplicar el rendimiento del trabajo de computación de la serie NC actual. Los clientes pueden aprovechar estas GPU actualizadas para cargas de trabajo de HPC tradicionales, como la creación de modelos de embalses, la secuenciación de ADN, el análisis de proteínas, la realización de simulaciones Monte Carlo y otras. Igual que la serie NC, La serie NCv2 ofrece una configuración que proporciona una interfaz de red de baja latencia y alto rendimiento optimizada para cargas de trabajo de computación paralelas estrechamente unidas.

> [!IMPORTANT]
> Para esta familia de tamaño, la cuota de vCPU (core) en su suscripción está establecida inicialmente en 0 en cada región. [Solicite un aumento de cuota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta familia en una [región donde esté disponible](https://azure.microsoft.com/regions/services/).
>

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | GPU | Número máximo de discos de datos |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6_v2 |6 |112 | 336 | 1 | 12 |
| Standard_NC12_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24r_v2* |24 |1448 | 1344 | 4 | 32 |

1 GPU = una tarjeta P100.

*Compatible con RDMA

## <a name="nd-instances"></a>Instancias de ND

Las máquinas virtuales de serie ND son una novedad incorporada a la familia GPU diseñada para cargas de trabajo inteligencia artificial y aprendizaje profundo. Ofrecen un rendimiento excelente para el aprendizaje y la inferencia. Las instancias de ND disponen de tecnología de GPU [Tesla P40 de NVIDIA](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Estas instancias brindan un rendimiento excelente para operaciones de punto flotante de precisión única, para cargas de trabajo de inteligencia artificial que usan Microsoft Cognitive Toolkit, TensorFlow, Caffe y otros marcos. La serie ND también ofrece una memoria de la GPU de un tamaño muy superior (24 GB), lo que permite adaptarse a modelos de redes neurales mucho más grandes. Al igual que la serie NC, la serie ND presenta una configuración con una baja latencia secundaria, una red de alta productividad mediante RDMA y conectividad InfiniBand para que pueda ejecutar trabajos de aprendizaje a gran escala que abarquen muchas GPU.

> [!IMPORTANT]
> Para esta familia de tamaño, la cuota de vCPU (core) por región en su suscripción está establecida inicialmente en 0. [Solicite un aumento de cuota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta familia en una [región donde esté disponible](https://azure.microsoft.com/regions/services/).
>

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | GPU | Número máximo de discos de datos |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6 |6 |112 | 336 | 1 | 12 |
| Standard_ND12 |12 |224 | 672 | 2 | 24 |
| Standard_ND24 |24 |448 | 1344 | 4 | 32 |
| Standard_ND24r* |24 |1448 | 1344 | 4 | 32 |

1 GPU = una tarjeta de P40.

*Compatible con RDMA

## <a name="nv-instances"></a>Instancias de NV



Las instancias de NV disponen de tarjetas GPU [Tesla M60 de NVIDIA](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) y tecnología NVIDIA GRID para aplicaciones aceleradas de escritorio y escritorios virtuales donde los clientes pueden visualizar sus datos o simulaciones. Los usuarios pueden visualizar sus flujos de trabajo con muchos gráficos en las instancias de NV para obtener una excelente funcionalidad gráfica y ejecutar, además, cargas de trabajo de precisión únicas, como la codificación y la representación. 

| Tamaño | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | GPU | Número máximo de discos de datos |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = media tarjeta M60.


