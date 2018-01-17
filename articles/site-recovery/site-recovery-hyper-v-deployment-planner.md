---
title: Azure Site Recovery Deployment Planner en el escenario de Hyper-V a Azure | Microsoft Docs
description: "Esta es la guía del usuario de Azure Site Recovery Deployment Planner en el escenario de Hyper-V en Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 0baf595266e71fad2df16996d63af3ba7d23a6ac
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Azure Site Recovery Deployment Planner en el escenario de Hyper-V en Azure
Este artículo es la guía del usuario de Azure Site Recovery Deployment Planner para implementaciones de producción de Hyper-V a Azure.

## <a name="overview"></a>Información general
Antes de empezar a proteger cualquier máquina virtual (VM) de Hyper-V mediante Site Recovery, asigne suficiente ancho de banda, según la frecuencia diaria de cambio de datos, para cumplir el objetivo de punto de recuperación (RPO) deseado y asignar suficiente espacio de almacenamiento en cada volumen del almacenamiento local de Hyper-V.

También es preciso que cree el tipo y número correctos de cuentas de Azure Storage de destino. Cree cuentas de almacenamiento Estándar o Premium, para lo que debe tener en cuenta el crecimiento en los servidores de producción de origen debido al aumento del uso con el paso del tiempo. Elija el tipo de almacenamiento por máquina virtual, en función de las características de la carga de trabajo (por ejemplo, operaciones de E/S por segundo [IOPS] de lectura/escritura o actividad de datos) y de los límites de Azure Site Recovery. 

Azure Site Recovery Deployment Planner es una herramienta de línea de comandos para escenarios de recuperación ante desastres tanto de Hyper-V a Azure como de VMware a Azure. Puede generar de forma remota un perfil para las máquinas virtuales de Hyper-V presentes en varios hosts Hyper-V mediante esta herramienta (sin ningún efecto en la producción) para averiguar el ancho de banda y los requisitos de Azure Storage necesarios para realizar una correcta replicación y conmutación por error de prueba. La herramienta se puede ejecutar sin que sea preciso instalar localmente ningún componente de Azure Site Recovery. Sin embargo, para obtener resultados precisos del rendimiento obtenido, se recomienda ejecutar el programador en una instancia de Windows Server que tenga la misma configuración de hardware que alguno de los servidores Hyper-V que se va a usar para habilitar la protección de recuperación ante desastres en Azure. 

La herramienta proporciona los detalles siguientes:

**Evaluación de compatibilidad**

* Evaluación de la idoneidad de las máquinas virtuales en función del número de discos, el tamaño de estos, las IOPS, la actividad de datos y algunas otras características de las máquinas virtuales.

**Necesidad de ancho de banda de red frente a evaluación de RPO**

* El ancho de banda de red necesario para la replicación diferencial
* Rendimiento que Azure Site Recovery puede obtener desde el entorno local en Azure
* RPO que se puede conseguir para un ancho de banda determinado
* Impacto en el RPO deseado si se aprovisiona un ancho de banda inferior.

    
**Requisitos de infraestructura de Azure**

* El tipo de almacenamiento (cuenta de almacenamiento Estándar o Premium) de cada máquina virtual
* El número total de cuentas de almacenamiento Estándar y Premium que se van a configurar para la replicación
* Sugerencias de nomenclatura de las cuentas de almacenamiento, según la guía de Azure Storage
* La posición de las cuentas de almacenamiento de todas las máquinas virtuales
* El número de núcleos de Azure que se deben configurar antes de realizar una conmutación por error, de prueba o real, en la suscripción
* El tamaño de máquina virtual de Azure que se recomienda para cada máquina virtual local

**Requisitos de la infraestructura local**
* El espacio de almacenamiento disponible requerido en cada volumen del almacenamiento de Hyper-V para la replicación inicial correcta y la replicación diferencial a fin de asegurarse de que la replicación de las máquinas virtuales no provocará ningún tiempo de inactividad no deseado para las aplicaciones en producción
* Frecuencia de copias máxima que se debe establecer para la replicación de Hyper-V

**Guía para el procesamiento por lotes de la replicación inicial** 
* Número de lotes de máquina virtual que se van a utilizar para protección
* Lista de máquinas virtuales en cada lote
* Orden en que cada lote se va a proteger
* Tiempo estimado para completar la replicación inicial de cada lote

**Costo estimado de la recuperación ante desastres en Azure**
* Costo total estimado de la recuperación ante desastres en Azure: costo de licencia de Azure Site Recovery, almacenamiento, red y proceso
* Detalles del análisis del costo por máquina virtual



>[!IMPORTANT]
>
>Dado que es probable que el uso aumente con el tiempo, al realizar todos los cálculos anteriores de la herramienta, se supone un factor de crecimiento de las características de carga de trabajo de un 30 % y se usa un valor del percentil 95 de todas las métricas de la generación de perfiles (IOPS de lectura y escritura, actividad de datos, etc.). Ambos elementos (el cálculo del percentil y el factor de crecimiento) se pueden configurar. Para más información acerca del factor de crecimiento, consulte la sección "Consideraciones acerca del factor de crecimiento". Para más información acerca del valor de percentil, consulte la sección "Valor del percentil usado para el cálculo".
>

## <a name="support-matrix"></a>Matrices compatibles

| | **VMware a Azure** |**Hyper-V en Azure**|**De Azure a Azure**|**De Hyper-V a un sitio secundario**|**Sitio VMware en un sitio secundario**
--|--|--|--|--|--
Escenarios admitidos |Sí|Sí|Sin |Sí*|Sin 
Versión admitida | vCenter 6.5, 6.0 o 5.5| Windows Server 2016, Windows Server 2012 R2 | N/D |Windows Server 2016, Windows Server 2012 R2|N/D
Configuración admitida|vCenter, ESXi| Clúster de Hyper-V, host de Hyper-V|N/D|Clúster de Hyper-V, host de Hyper-V|N/D|
Número de servidores cuyo perfil puede generarse por instancia en ejecución de Azure Site Recovery Deployment Planner |Único (los perfiles de las máquinas virtuales que pertenecen a una instancia de vCenter Server o a un servidor ESXi se pueden generar a la vez)|Varios (los perfiles de las máquinas virtuales en varios hosts o clústeres de hosts se pueden generar a la vez)| N/D |Varios (los perfiles de las máquinas virtuales en varios hosts o clústeres de hosts se pueden generar a la vez)| N/D

*La herramienta es principalmente para un escenario de recuperación ante desastres de Hyper-V en Azure. En un escenario de recuperación ante desastres de Hyper-V en un sitio secundario, solo se puede usar para conocer recomendaciones sobre el origen como el ancho de banda de red requerido, el espacio de almacenamiento disponible necesario en cada uno de los servidores Hyper-V de origen y las cifras referentes al procesamiento por lotes de replicación inicial y a las definiciones de los lotes.  Omita las recomendaciones de Azure y los costos del informe. Además, la operación Get Throughput (Obtención de rendimiento) no es aplicable al escenario de recuperación ante desastres de Hyper-V a un sitio secundario.

## <a name="prerequisites"></a>Requisitos previos
La herramienta tiene tres fases principales de Hyper-V: obtener la lista de máquinas virtuales, generación de perfiles y generación de informes. También hay una cuarta opción para calcular solo el rendimiento. Los requisitos para el servidor en el que se deben ejecutar las distintas fases se presentan en la tabla siguiente:

| Requisito del servidor | DESCRIPCIÓN |
|---|---|
|Obtención de la lista de máquinas virtuales, generación de perfiles y medición de rendimiento |<ul><li>Sistema operativo: Microsoft Windows Server 2016 o Microsoft Windows Server 2012 R2 </li><li>Configuración del equipo: 8 vCPU, 16 GB de RAM y 300 GB de HDD</li><li>[Microsoft .NET 4.5 Framework](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ Redistributable para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acceso a través de Internet a Azure desde este servidor</li><li>Cuenta de almacenamiento de Azure</li><li>Acceso de administrador en el servidor</li><li>Mínimo de 100 GB de espacio libre en disco (asumiendo 1000 máquinas virtuales con un promedio de tres discos cada una, con perfil para 30 días)</li><li>La máquina virtual desde donde se ejecuta la herramienta Azure Site Recovery Deployment Planner debe agregarse a la lista TrustedHosts de todos los servidores Hyper-V.</li><li>Las máquinas virtuales de todos los servidores Hyper-V deben agregarse a la lista TrustedHosts de la máquina virtual del cliente desde donde se esté ejecutando la herramienta. [Más información sobre cómo agregar servidores a la lista TrustedHosts](#steps-to-add-servers-into-trustedhosts-list). </li><li> La herramienta debe ejecutarse con privilegios de administrador desde PowerShell o la consola de línea de comandos en el cliente</ul></ul>|
| Generación de informes | Un PC con Windows o Windows Server con Microsoft Excel 2013, o cualquier versión posterior |
| Permisos de usuario | Cuenta de administrador para tener acceso al clúster o al host Hyper-V durante la obtención de una lista de máquinas virtuales y las operaciones de generación de perfiles.<br>Todos los hosts cuyo perfil debe generarse deben tener una cuenta de administrador de dominio con las mismas credenciales, es decir, el nombre de usuario y la contraseña
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Pasos para agregar servidores a la lista TrustedHosts
1.  La máquina virtual desde donde la herramienta se va a implementar debe tener todos los hosts cuyo perfil se va a generar en su lista TrustedHosts. Para agregar el cliente a la lista Trustedhosts, ejecute el siguiente comando desde una instancia de PowerShell con privilegios elevados en la máquina virtual. La máquina virtual puede ser Windows Server 2012 R2 o Windows Server 2016. 

            set-item wsman:\localhost\Client\TrustedHosts -value <ComputerName>[,<ComputerName>]

2.  Cada host Hyper-V cuyo perfil debe realizarse debe tener:

    a. La máquina virtual en la que la herramienta se va a ejecutar en su lista TrustedHosts. Ejecute el siguiente comando desde una sesión de PowerShell con privilegios elevados en el host Hyper-V.

            set-item wsman:\localhost\Client\TrustedHosts -value <ComputerName>[,<ComputerName>]

    b. Comunicación remota de PowerShell habilitada.

            Enable-PSRemoting -Force

## <a name="download-and-extract-the-deployment-planner-tool"></a>Descarga y extracción de Deployment Planner Tool

1.  Descargue la versión más reciente de [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
La herramienta está empaquetada en una carpeta en formato zip. La misma herramienta admite VMware tanto en Azure como en Hyper-V en escenarios de recuperación ante desastres de Azure. Puede utilizar esta herramienta para el escenario de recuperación ante desastres de Hyper-V en el sitio secundario también, pero omitir la recomendación de infraestructura de Azure del informe.

2.  Copie la carpeta .zip en la instancia de Windows Server desde la que desea ejecutar la herramienta. Puede ejecutar la herramienta en un equipo Windows Server 2012 R2 o Windows Server 2016. El servidor debe tener acceso a la red para conectarse al clúster de Hyper-V o al host de Hyper-V que contiene las máquinas virtuales cuyo perfil se va a generar. Se recomienda que tenga la misma configuración de hardware de la máquina virtual, donde la herramienta se vaya a ejecutar, que la del servidor Hyper-V que desee proteger. Dicha configuración garantiza que el rendimiento obtenido que indica la herramienta coincide con el rendimiento real que Azure Site Recovery puede lograr durante la replicación. El cálculo del rendimiento depende del ancho de banda de red disponible en el servidor y de la configuración del hardware (CPU, almacenamiento, etc.) del servidor. El rendimiento se calcula desde el servidor donde se ejecuta la herramienta en Azure. Si la configuración del servidor difiere de la del servidor Hyper-V, el rendimiento obtenido del que informa la herramienta puede ser impreciso.
Configuración recomendada de la máquina virtual: 8 vCPU, 16 GB de RAM, disco duro de 300 GB.

3.  Extraiga la carpeta .zip.
La carpeta contiene varios archivos y subcarpetas. El archivo ejecutable es ASRDeploymentPlanner.exe y está en la carpeta primaria.

Ejemplo: copie el archivo .zip en la unidad E:\ y extráigalo. E:\ASR Deployment Planner_v2.1.zip

E:\ASR Deployment Planner_v2.1\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Actualización a la versión más reciente de Deployment Planner
Si tiene una versión anterior de la herramienta, realice una de las siguientes acciones:
 * Si la versión más reciente no contiene una corrección de la generación de perfiles y la generación de perfiles ya está en curso en la versión actual del programador, continúe con la generación de perfiles.
 * Si la versión más reciente contiene una corrección de la generación de perfiles, se recomienda detener la versión actual de la generación de perfiles y reiniciar la generación de perfiles con la nueva versión.


  >[!NOTE]
  >
  >Al iniciar la generación de perfiles con la nueva versión, pase la misma ruta de acceso del directorio de salida, con el fin de que la herramienta anexe los datos del perfil a los archivos existentes. Para generar el informe, se usará un conjunto completo de datos de la generación de perfiles. Si pasa otro directorio de salida, se crean nuevos archivos y los datos anteriores de generación de perfiles no se usan al generar el informe.
  >
  >Cada nueva instancia de Deployment Planner es una actualización acumulativa del archivo zip. No es preciso copiar los archivos más recientes en la carpeta anterior. Se puede crear y usar una carpeta nueva.

## <a name="version-history"></a>Historial de versiones
La versión más reciente de la herramienta ASR Deployment Planner es la 2.1.
Para saber qué correcciones se agregan en cada actualización, consulte [ASR Deployment Planner Version History](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) (Historial de versiones de ASR Deployment Planner).


## <a name="next-steps"></a>pasos siguientes
* [Ejecución de Deployment Planner](site-recovery-hyper-v-deployment-planner-run.md).