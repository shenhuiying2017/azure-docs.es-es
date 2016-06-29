<properties
 pageTitle="Tamaños de los servicios en la nube"
 description="Enumera los diferentes tamaños de máquina virtual para los roles web y de trabajo del servicio en la nube de Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="05/25/2016"
 ms.author="adegeo"/>

# Tamaños de los servicios en la nube

En este tema se describen las opciones y los tamaños disponibles para las instancias de rol de servicio en la nube (roles web y roles de trabajo). También ofrece consideraciones de implementación que hay que tener en cuenta a la hora de planear usar estos recursos.

Servicios en la nube es uno de los distintos tipos de recursos de proceso que ofrece Azure. Para obtener más información sobre Servicios en la nube, haga clic [aquí](cloud-services-choose-me.md).

> [AZURE.NOTE]Para ver límites relacionados de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

## Tamaños de instancias de roles web y de trabajo

Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:

* Las instancias de máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

* Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,1 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.

* Los roles web y los roles de trabajo requieren más espacio en el disco temporal que las máquinas virtuales de Azure debido a los requisitos del sistema. Los archivos del sistema reservan 4 GB de espacio para el archivo de paginación de Windows y 2 GB de espacio para el archivo de volcado de memoria de Windows.

* El disco del sistema operativo contiene el sistema operativo invitado de Windows e incluye la carpeta Archivos de programa (incluidas las instalaciones realizadas a través de tareas de inicio a menos que especifique otro disco), cambios del Registro, la carpeta System32 y .NET Framework.

* El **disco de almacenamiento temporal** contiene registros de Azure y archivos de configuración, Diagnósticos de Azure (que incluye sus registros de IIS) y cualquier recurso de almacenamiento local que defina.

* El **disco de aplicaciones** es donde se extrae su .cspkg e incluye su sitio web, los archivos binarios, el proceso de host de rol, las tareas de inicio, web.config, etc.

* Los tamaños de máquina virtual A8/A10 y A9/A11 tienen las mismas capacidades. Las instancias de máquina virtual A8 y A9 incluyen otro adaptador de red que se conecta a una red de acceso de memoria directa remota (RDMA) para una rápida comunicación entre máquinas virtuales. Las instancias A8 y A9 están diseñadas para aplicaciones informáticas de alto rendimiento que requieren una comunicación constante y de baja latencia entre nodos durante la ejecución, por ejemplo, aplicaciones que utilizan la interfaz de paso de mensajes (MPI). Las instancias de máquina virtual A10 y A11 no incluyen el adaptador de red adicional. Las instancias A10 y A11 están diseñadas para aplicaciones informáticas de alto rendimiento que no requieren la comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.

    >[AZURE.NOTE] Si está considerando la posibilidad de usar tamaños de A8 a A11, lea [esta información](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).

>[AZURE.NOTE] Todos los tamaños de máquina proporcionan un **disco de aplicaciones** que almacena todos los archivos del paquete de servicio de la nube. Tiene aproximadamente 1,5 GB de tamaño.

Asegúrese de consultar los [precios](https://azure.microsoft.com/pricing/details/cloud-services/) de cada tamaño de servicio en la nube.

## Uso general

Para sitios web, bases de datos pequeñas y medianas y otras aplicaciones habituales.

>[AZURE.NOTE] La capacidad de almacenamiento se representa mediante 1024^3 bytes como unidad de medida para GB. En ocasiones, esto se conoce como gibibyte o definición de base 2 Al comparar los tamaños que utilizan distintos sistemas de base, tenga en cuenta que los tamaños de base 2 podrían parecer más pequeños que los de base 10. No obstante, para cualquier tamaño específico (como 1 GB), un sistema de base 2 ofrece más capacidad que un sistema de base 10, ya que 1024^3 es mayor que 1000^3.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | -------: |
| ExtraSmall | 1 | 0,75 GB | Bajo | 19 GB |
| Pequeña | 1 | 1,75 GB | Moderado | 224 GB |
| Mediano | 2 | 3,5 GB | Moderado | 489 GB |
| Grande | 4 | 7 GB | Alto | 999 GB |
| ExtraLarge | 8 | 14 GB | Alto | 2039 GB |

>[AZURE.NOTE] Los tamaños **ExtraSmall** a **ExtraLarge** también se pueden denominar **A0 a A4**, respectivamente.

## Memoria intensiva

Para bases de datos grandes, granjas de servidores de SharePoint y aplicaciones de alto rendimiento.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | ------:  |
| A5 | 2 | 14 GB | Moderado | 489 GB |
| A6 | 4 | 28 GB | Alto | 999 GB |
| A7 | 8 | 56 GB | Alto | 2039 GB |

## Red optimizada con soporte para InfiniBand

Disponible en ciertos centros de datos. Las máquinas virtuales A8 y A9 tienen [procesadores Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Incluye una red **InfiniBand** de 32 Gbit/s con tecnología de acceso directo a memoria remota (RDMA). Ideal para aplicaciones de interfaz de paso de mensajes (MPI), clústeres de alto rendimiento, modelado y simulación, codificación de vídeo y otros escenarios con un uso intensivo de recursos de proceso y de red.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| A8 | 8 | 56 GB | Alto | 382 GB |
| A9 | 16 | 112 GB | Muy alto | 382 GB |

## Proceso intensivo

Disponible en ciertos centros de datos. Las máquinas virtuales A10 y A11 tienen [procesadores Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Para clústeres de alto rendimiento, modelado y simulación, codificación de vídeo y otros escenarios con un uso intensivo de recursos de proceso o de red. Similar a la configuración de instancias A8 y A9 sin la red InifiniBand y tecnología RDMA.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| A10 | 8 | 56 GB | Alto | 382 GB |
| A11 | 16 | 112 GB | Muy alto | 382 GB |

## Serie D: proceso optimizado

Las máquinas virtuales de la serie D incluyen unidades de estado sólido (SSD) y procesadores más rápidos que los de la serie A (un 60 % más rápidos) y también están disponibles para roles web o de trabajo en Servicios en la nube de Azure. Esta serie resulta ideal para las aplicaciones que exigen CPU más rápidas, un mejor rendimiento del disco local o mayor capacidad de memoria.

## Uso general (D)

Para sitios web, bases de datos pequeñas y medianas y otras aplicaciones habituales.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| Standard\_D1 | 1 | 3,5 GB | Moderado | 50 GB |
| Standard\_D2 | 2 | 7 GB | Alto | 100 GB* |
| Standard\_D3 | 4 | 14 GB | Alto | 200 GB |
| Standard\_D4 | 8 | 28 GB | Alto | 400 GB |

## Memoria intensiva (D)

Para bases de datos grandes, granjas de servidores de SharePoint y aplicaciones de alto rendimiento.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| Standard\_D11 | 2 | 14 GB | Alto | 100 GB* |
| Standard\_D12 | 4 | 28 GB | Alto | 200 GB |
| Standard\_D13 | 8 | 56 GB | Alto | 400 GB |
| Standard\_D14 | 16 | 112 GB | Muy alto | 800 GB |

## Serie Dv2: proceso optimizado

Las instancias de la serie Dv2 son la próxima generación de la serie D, que se pueden usar como máquinas virtuales o como servicios en la nube. Las instancias de la serie Dv2 disponen de CPU con mayor capacidad que son un 35 % más rápidas por término medio que en las instancias de la serie D, y cuentan con la misma configuración de memoria y discos que la de la serie D. Las instancias de la serie Dv2 se basan un procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de última generación y, con Intel Turbo Boost Technology 2.0, pueden llegar a 3,1 GHz. Tanto la serie Dv2 como la serie D son ideales para aplicaciones que requieren CPU más rápidas, mejor rendimiento de los discos locales o memorias superiores, y ofrecen una eficaz combinación para muchas aplicaciones de nivel empresarial.

## Uso general (Dv2)

Para sitios web, bases de datos pequeñas y medianas y otras aplicaciones habituales.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| Standard\_D1\_v2 | 1 | 3,5 GB | Moderado | 50 GB |
| Standard\_D2\_v2 | 2 | 7 GB | Alto | 100 GB* |
| Standard\_D3\_v2 | 4 | 14 GB | Alto | 200 GB |
| Standard\_D4\_v2 | 8 | 28 GB | Alto | 400 GB |
| Standard\_D5\_v2 | 16 | 56 GB | Muy alto | 800 GB |

## Memoria intensiva (Dv2)

Para bases de datos grandes, granjas de servidores de SharePoint y aplicaciones de alto rendimiento.

| Tamaño (id.) | Núcleos | RAM | Ancho de banda neto | Tamaño total del disco |
| --------------- | :-------: | ------: | :-----------: | -------: |
| Standard\_D11\_v2 | 2 | 14 GB | Alto | 100 GB* |
| Standard\_D12\_v2 | 4 | 28 GB | Alto | 200 GB |
| Standard\_D13\_v2 | 8 | 56 GB | Alto | 400 GB |
| Standard\_D14\_v2 | 16 | 112 GB | Muy alto | 800 GB |

## Configuración de tamaños para los Servicios en la nube

Puede especificar el tamaño de la máquina virtual de una instancia de rol como parte del modelo de servicio descrito por el [archivo de definición de servicio](cloud-services-model-and-package.md#csdef). El tamaño del rol determina la cantidad de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna a una instancia en ejecución. Elija el tamaño del rol en función del requisito de recursos de la aplicación.

Este es un ejemplo de cómo establecer el tamaño del rol como [Standard\_D2](#general-purpose-d) para una instancia de rol web:

```xml
<WebRole name="WebRole1" vmsize="<mark>Standard_D2</mark>">
...
</WebRole>
```

<!---HONumber=AcomDC_0615_2016-->