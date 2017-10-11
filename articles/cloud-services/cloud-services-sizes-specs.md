---
title: "Tamaños de máquina virtual para Azure Cloud Services | Microsoft Docs"
description: "Enumera los diferentes tamaños (e ids.) de máquina virtual para los roles web y de trabajo del servicio en la nube de Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 1ba56eb9539a4295fdaaab523cfd2a7e1587ef54
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="sizes-for-cloud-services"></a>Tamaños de los servicios en la nube
En este tema se describen las opciones y los tamaños disponibles para las instancias de rol de servicio en la nube (roles web y roles de trabajo). También ofrece consideraciones de implementación que hay que tener en cuenta a la hora de planear usar estos recursos. Cada tamaño tiene un identificador que pondrá en su [archivo de definición de servicio](cloud-services-model-and-package.md#csdef). Los precios para cada tamaño están disponibles en la página [Precios de Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/).

> [!NOTE]
> Para ver límites relacionados de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamaños de instancias de roles web y de trabajo
Hay varios tamaños estándar para elegir en Azure. Entre las consideraciones para algunos de estos tamaños, se incluyen:

* Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.
* Las máquinas virtuales de la serie G ofrecen la mayor cantidad de memoria y se ejecutan en hosts con procesadores de la familia Intel Xeon E5 V3.
* Las máquinas virtuales de la serie A se pueden implementar en diversos procesadores y tipos de hardware. Según el hardware, el tamaño es una limitación para ofrecer un rendimiento coherente del procesador para la instancia en ejecución, independientemente del hardware en que se implementó. Con el fin de determinar el hardware físico en que se implementó este tamaño, cree una consulta para el hardware virtual desde dentro de la máquina virtual.
* El tamaño A0 está sobresuscrito en el hardware físico. Solo en este tamaño específico, las implementaciones de otros clientes podrían afectar el rendimiento de la carga de trabajo en ejecución. A continuación, se indica el rendimiento relativo como la línea base esperada, sujeta a una variabilidad aproximada de 15 por ciento.

El tamaño de la máquina virtual afecta a los precios. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Los costes de almacenamiento se calculan por separado según las páginas utilizadas en la cuenta de almacenamiento. Para más información, consulte [Detalles de precios de Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/) y [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:

* Los tamaños A8-A11 los de la serie H también se conocen como *instancias de proceso intensivo*. El hardware que ejecuta estos tamaños está diseñado y optimizado para aplicaciones de proceso intensivo que consumen muchos recursos de red, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HPC), el modelado y las simulaciones. La serie A8-A11 utiliza Intel Xeon E5-2670 a 2,6 GHz y la serie H, Intel Xeon E5-2667 v3 a 3,2 GHz. Para más información y consideraciones sobre el uso de estos tamaños, consulte [Tamaños de máquina virtual de informática de alto rendimiento](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Las series Dv2, D y G son ideales para las aplicaciones que requieren CPU más rápidas, mejor rendimiento de disco local, o tienen mayor demanda de memoria. Ofrecen una combinación eficaz para muchas aplicaciones de clase empresarial.
* Puede que algunos de los hosts físicos de los centros de datos de Azure no admitan tamaños de máquinas virtuales grandes, como A5 – A11. En consecuencia, puede ver el mensaje de error **No se pudo configurar la máquina virtual {nombre de la máquina}** o **No se pudo crear la máquina virtual {nombre de la máquina}** al cambiar el tamaño de una máquina virtual existente por un nuevo tamaño, al crear una nueva máquina virtual en una red virtual creada antes del 16 de abril de 2013 o al agregar una nueva máquina virtual a un servicio en la nube existente. Consulte [Error: "No se pudo configurar la máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) en el foro de soporte técnico para ver una lista de soluciones alternativas para cada escenario de implementación.
* La suscripción también podría limitar el número de núcleos que se pueden implementar en ciertas familias de tamaños. Para aumentar una cuota, póngase en contacto con el soporte técnico de Azure.

## <a name="performance-considerations"></a>Consideraciones sobre rendimiento
Creamos el concepto de unidad de proceso de Azure (ACU) para brindar una forma de comparar el rendimiento de los procesos (CPU) en todas las SKU de Azure e identificar qué SKU es más probable que satisfaga nuestras necesidades de rendimiento.  Actualmente, una ACU está estandarizada en una máquina virtual pequeña (Standard_A1) como 100 y todas las demás SKU representan, aproximadamente, qué tanto más rápido esa SKU puede ejecutar una prueba comparativa estándar.

> [!IMPORTANT]
> La ACU es solo una referencia. Los resultados de la carga de trabajo pueden variar.
>
>

<br>

| Familia de SKU | ACU/núcleo |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Small-ExtraLarge](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [Standard_A1-8v2](#av2-series) |100 |
| [Standard_A2m-8mv2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1 15v2](#dv2-series) |210 - 250* |
| [G1 5](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

Las ACU marcadas con un asterisco * usan la tecnología Intel® Turbo para incrementar la frecuencia de CPU y brindar una mejora del rendimiento. El volumen de la mejora puede variar según el tamaño de la máquina virtual, la carga de trabajo y las otras cargas de trabajo que se ejecutan en el mismo host.

## <a name="size-tables"></a>Tablas de tamaño
Las siguientes tablas muestran los tamaños y las capacidades que ofrecen.

* La capacidad de almacenamiento se muestra en unidades de GiB o 1024^3 bytes. Cuando compare discos que se miden en GB (1000^3 bytes) con discos que se miden en GiB (1024^3), recuerde que los números que representan la capacidad en GiB pueden parecer más pequeños. Por ejemplo, 1023 GiB = 1098.4 GB
* Se midió el rendimiento de disco en operaciones de entrada/salida por segundo (E/S por segundo) y Mbps, donde Mbps = 10^6 bytes/s.
* Los discos de datos pueden funcionar en modo en caché o en modo no en caché. En el caso de la operación de disco de datos en caché, el modo de caché del host está establecido en **ReadOnly** o **ReadWrite**. En el caso de la operación de disco de datos no en caché, el modo de caché del host está definido en **None**.
* El ancho de banda de red máximo es el ancho de banda agregado máximo que se asigna por cada tipo de VM. El ancho de banda máximo proporciona una orientación a la hora de seleccionar el tipo de VM correcto a fin de garantizar la disponibilidad de la capacidad de red adecuada. Al cambiar a Bajo, Moderado, Alto y Muy alto, el rendimiento aumentará en consecuencia. El rendimiento de red real dependerá de muchos factores (como, por ejemplo, las cargas de la red y de la aplicación y la configuración de red de la aplicación).

## <a name="a-series"></a>Serie A
| Tamaño            | Núcleos de CPU | Memoria: GiB  | HDD local: GiB       | Ancho de banda de red/NIC máx. |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0,768        | 20 |                   | 1 / bajo |
| Pequeña           | 1         | 1,75         | 225                  | 1 / moderado |
| Mediano          | 2         | 3,5 GB       | 490                  | 1 / moderado |
| Grande           | 4         | 7            | 1000                 | 2 / alto |
| ExtraLarge      | 8         | 14           | 2040                 | 4 / alto |
| A5              | 2         | 14           | 490                  | 1 / moderado |
| A6              | 4         | 28           | 1000                 | 2 / alto |
| A7              | 8         | 56           | 2040                 | 4 / alto |

## <a name="a-series---compute-intensive-instances"></a>Serie A: instancias de proceso intensivo
Para más información y consideraciones sobre el uso de estos tamaños, consulte [Tamaños de máquina virtual de informática de alto rendimiento](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamaño            | Núcleos de CPU | Memoria: GiB  | HDD local: GiB       | Ancho de banda de red/NIC máx. |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2 / alto |
| A9*             |16         | 112          | 1817                 | 4 / muy alto |
| A10             |8          | 56           | 1817                 | 2 / alto |
| A11             |16         | 112          | 1817                 | 4 / muy alto |

\*Compatible con RDMA

## <a name="av2-series"></a>Serie Av2

| Tamaño            | Núcleos de CPU | Memoria: GiB  | SSD local: GiB       | Ancho de banda de red/NIC máx. |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1 / moderado                 |
| Standard_A2_v2  | 2         | 4            | 20 |                   | 2 / moderado                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4 / alto                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8 / alto                     |
| Standard_A2m_v2 | 2         | 16           | 20 |                   | 2 / moderado                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4 / alto                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8 / alto                     |


## <a name="d-series"></a>Serie D
| Tamaño            | Núcleos de CPU | Memoria: GiB  | SSD local: GiB       | Ancho de banda de red/NIC máx. |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3,5          | 50                   | 1 / moderado |
| Standard_D2     | 2         | 7            | 100                  | 2 / alto |
| Standard_D3     | 4         | 14           | 200                  | 4 / alto |
| Standard_D4     | 8         | 28           | 400                  | 8 / alto |
| Standard_D11    | 2         | 14           | 100                  | 2 / alto |
| Standard_D12    | 4         | 28           | 200                  | 4 / alto |
| Standard_D13    | 8         | 56           | 400                  | 8 / alto |
| Standard_D14    | 16        | 112          | 800                  | 8 / muy alto |

## <a name="dv2-series"></a>Serie Dv2
| Tamaño            | Núcleos de CPU | Memoria: GiB  | SSD local: GiB       | Ancho de banda de red/NIC máx. |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3,5          | 50                   | 1 / moderado |
| Standard_D2_v2  | 2         | 7            | 100                  | 2 / alto |
| Standard_D3_v2  | 4         | 14           | 200                  | 4 / alto |
| Standard_D4_v2  | 8         | 28           | 400                  | 8 / alto |
| Standard_D5_v2  | 16        | 56           | 800                  | 8 / extremadamente alto |
| Standard_D11_v2 | 2         | 14           | 100                  | 2 / alto |
| Standard_D12_v2 | 4         | 28           | 200                  | 4 / alto |
| Standard_D13_v2 | 8         | 56           | 400                  | 8 / alto |
| Standard_D14_v2 | 16        | 112          | 800                  | 8 / extremadamente alto |
| Standard_D15_v2 | 20 |        | 140          | 1000                | 8 / extremadamente alto |

## <a name="g-series"></a>Serie G
| Tamaño            | Núcleos de CPU | Memoria: GiB  | SSD local: GiB       | Ancho de banda de red/NIC máx. |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1 / alto |
| Standard_G2     | 4         | 56           | 768                  |2 / alto |
| Standard_G3     | 8         | 112          | 1536                |4 / muy alto |
| Standard_G4     | 16        | 224          | 3072                |8 / extremadamente alto |
| Standard_G5     | 32        | 448          | 6144                |8 / extremadamente alto |

## <a name="h-series"></a>Serie H
Las máquinas virtuales de la serie H son las de próxima generación informática de alto rendimiento que abordan las necesidades informáticas de gama alta, como el modelado molecular y la dinámica de fluidos computacional. Estas máquinas virtuales de 8 y 16 núcleos se basan en la tecnología de procesador Intel Haswell E5-2667 V3 con memoria DDR4 y almacenamiento basado en SSD local.

Además de una potencia de CPU notable, la serie H ofrece varias opciones para las redes RDMA de baja latencia con FDR InfiniBand y varias configuraciones de memoria para admitir requisitos computacionales de uso intensivo de la memoria.

| Tamaño            | Núcleos de CPU | Memoria: GiB  | SSD local: GiB       | Ancho de banda de red/NIC máx. |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / alto |
| Standard_H16    | 16        | 112          | 2000                 | 8 / muy alto |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / alto |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / muy alto |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / muy alto |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / muy alto |

\*Compatible con RDMA

## <a name="configure-sizes-for-cloud-services"></a>Configuración de tamaños para los Servicios en la nube
Puede especificar el tamaño de la máquina virtual de una instancia de rol como parte del modelo de servicio descrito por el [archivo de definición de servicio](cloud-services-model-and-package.md#csdef). El tamaño del rol determina la cantidad de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna a una instancia en ejecución. Elija el tamaño del rol en función del requisito de recursos de la aplicación.

Este es un ejemplo de cómo establecer el tamaño del rol como [Standard_D2](#general-purpose-d) para una instancia de rol web:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Cambio de tamaño de un rol existente

A medida que cambia la naturaleza de la carga de trabajo o los tamaños de la nueva máquina virtual están disponibles, puede que desee cambiar el tamaño de su rol. Para ello, debe cambiar el tamaño de la máquina virtual en el archivo de definición de servicio (tal y como se muestra arriba), volver a empaquetar Cloud Service e implementarlo. No es posible cambiar los tamaños de máquina virtual directamente desde el portal o PowerShell.

>[!TIP]
> Puede que desee utilizar diferentes tamaños de máquina virtual para su rol en diferentes entornos (p. ej. prueba frente a producción). Una manera de hacer esto es crear varios archivos de definición de servicio (.csdef) en el proyecto, luego cree diferentes paquetes de servicio en la nube por entorno durante la compilación automatizada con la herramienta CSPack. Para obtener más información acerca de los elementos de un paquete de Cloud Services y cómo crearlos, vea [¿Qué es el modelo de Cloud Services y cómo empaquetarlo?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Obtención de una lista de tamaños
Puede usar PowerShell o la API de REST para obtener una lista de tamaños. La API de REST de Storm se documenta [aquí](https://msdn.microsoft.com/library/azure/dn469422.aspx). El código siguiente es un comando de PowerShell que enumerará todos los tamaños disponibles actualmente para el servicio en la nube.

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize
```

## <a name="next-steps"></a>Pasos siguientes
* Conozca los [límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md).
* Más información [sobre los tamaños de máquinas virtuales de procesos de alto rendimiento](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para cargas de trabajo HPC.
