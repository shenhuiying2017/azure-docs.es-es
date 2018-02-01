---
title: "Configuración de Integration Runtime de SSIS de Azure para conseguir un alto rendimiento | Microsoft Docs"
description: Aprenda a configurar las propiedades de Integration Runtime de SSIS de Azure para conseguir un alto rendimiento
services: data-factory
ms.date: 01/10/2018
ms.topic: article
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d0e75ad85731b10f9a993c2fa62f30c0142ed05
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configuración de Integration Runtime de SSIS de Azure para conseguir un alto rendimiento

En este artículo se describe cómo configurar una instancia de Integration Runtime (IR) de SSIS de Azure para conseguir un alto rendimiento. IR de SSIS de Azure le permite implementar y ejecutar paquetes de SQL Server Integration Services (SSIS) en Azure. Para más información acerca de IR de SSIS de Azure, consulte el artículo [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Para información sobre la implementación y la ejecución de paquetes SSIS en Azure, consulte [Levantar y mover las cargas de trabajo de SQL Server Integration Services a la nube](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Este artículo contiene los resultados de rendimiento y las observaciones de pruebas internas que han realizado los miembros del equipo de desarrollo de SSIS. Los resultados pueden variar. Realice sus propias pruebas antes de terminar su configuración, ya que esto afectará al costo y al rendimiento.

## <a name="properties-to-configure"></a>Propiedades para configurar

La parte siguiente de un script de configuración muestra las propiedades que puede configurar al crear una instancia de Integration Runtime de SSIS de Azure. Si quiere ver el script completo de PowerShell y una descripción, consulte [Implementación de paquetes de SSIS en Azure](tutorial-deploy-ssis-packages-azure.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** es la ubicación del nodo de trabajo de Integration Runtime. El nodo de trabajo mantiene una conexión constante a la base de datos de catálogo de SSIS (SSISDB) en una base de datos SQL de Azure. Establezca **AzureSSISLocation** en la misma ubicación que el servidor de SQL Database que hospeda SSISDB, ya que esto permite que Integration Runtime funcione de la manera más eficaz posible.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
La versión preliminar pública de Azure Data Factory v2, que incluye IR de SSIS de Azure, admite las siguientes opciones:
-   Estándar\_A4\_v2
-   Estándar\_A8\_v2
-   Estándar\_D1\_v2
-   Estándar\_D2\_v2
-   Estándar\_D3\_v2
-   Estándar\_D4\_v2.

En las pruebas internas no oficiales llevadas a cabo por el equipo de ingeniería de SSIS, la serie D parece ser más adecuada para la ejecución de paquetes SSIS que la serie A.

-   La proporción de precio/rendimiento de la serie D es mayor que la de la serie A.
-   El rendimiento de la serie D es mayor que el de la serie A por el mismo precio.

### <a name="configure-for-execution-speed"></a>Configuración de la velocidad de ejecución
Si no tiene que ejecutar muchos paquetes y quiere que la ejecución se realice rápidamente, use la información del gráfico siguiente para elegir un tipo de máquina virtual adecuado para su escenario.

Estos datos representan la ejecución de un único paquete en un único nodo de trabajo. El paquete carga 10 millones de registros con las columnas de nombre completo y apellido desde Azure Blob Storage, genera una columna de nombre completo y escribe los registros que tienen un nombre completo de más de 20 caracteres en Azure Blob Storage.

![Velocidad de ejecución de paquetes de Integration Runtime de SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Configuración del rendimiento global

Si tiene que ejecutar muchos paquetes y lo que más le preocupa es el rendimiento global, use la información del siguiente gráfico para elegir un tipo de máquina virtual adecuado para su escenario.

![Rendimiento global máximo de Integration Runtime de SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** ajusta la escalabilidad de Integration Runtime. El rendimiento de Integration Runtime es proporcional al valor de **AzureSSISNodeNumber**. Establezca **AzureSSISNodeNumber** en un valor pequeño al principio, supervise el rendimiento de Integration Runtime y, a continuación, ajuste el valor para su escenario. Para volver a configurar el recuento de nodos de trabajo, consulte [Administración de una instancia de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Si ya está usando un nodo de trabajo de gran potencia para ejecutar los paquetes, el hecho de aumentar **AzureSSISMaxParallelExecutionsPerNode** puede incrementar el rendimiento global de Integration Runtime. En el caso de los nodos Standard_D1_v2, se admiten ejecuciones en paralelo 1-4 por nodo. Para el resto de tipos de nodos, se admiten ejecuciones en paralelo 1-8 por nodo.
Puede calcular el valor apropiado en función del costo del paquete y de las siguientes configuraciones de los nodos de trabajo. Para más información, consulte [Tamaños de máquina virtual de uso general](../virtual-machines/windows/sizes-general.md).

| Tamaño             | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Rendimiento máximo del almacenamiento temporal: E/S por segundo / MBps de lectura / MBps de escritura | Rendimiento máximo por discos de datos: E/S por segundo | Nº máx. NIC / rendimiento de red esperado (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Estándar\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Estándar\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Estándar\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Estándar\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Estándar\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Estándar\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |

Estas son las instrucciones para configurar el valor correcto de la propiedad **AzureSSISMaxParallelExecutionsPerNode**: 

1. Establézcala en un valor pequeño en un primer momento.
2. Auméntelo un poco para comprobar si se ha mejorado el rendimiento global.
3. Deje de aumentar el valor cuando el rendimiento global alcance el valor máximo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** es el plan de tarifa de la base de datos de catálogo de SSIS (SSISDB) en una base de datos SQL de Azure. Esta configuración afecta al número máximo de trabajos en la instancia de IR, a la velocidad para poner en cola una ejecución de paquetes y a la velocidad para cargar el registro de ejecución.

-   Si no le preocupa la velocidad a la que se pone en cola una ejecución de paquetes o se carga el registro de ejecución, puede elegir el plan de tarifa de base de datos más bajo. Azure SQL Database con el plan Básico admite 8 trabajos en una instancia de Integration Runtime.

-   Si el recuento de trabajos o el recuento de núcleos es superior a 8 o 50, respectivamente, elija una base de datos con una mayor potencia que la básica. En caso contrario, la base de datos se convierte en el cuello de botella de la instancia de Integration Runtime y el rendimiento global se ve afectado de forma negativa.

También puede ajustar el plan de tarifa de base de datos en función de la información de uso de la [unidad de transacción de base de datos](../sql-database/sql-database-what-is-a-dtu.md) (DTU) disponible en Azure Portal.

## <a name="design-for-high-performance"></a>Diseño para alto rendimiento
Diseñar un paquete SSIS para que se ejecute en Azure no es lo mismo que diseñar un paquete para la ejecución local. En lugar de combinar varias tareas independientes en el mismo paquete, sepárelas en varios paquetes para una ejecución más eficiente en IR de SSIS de Azure. Cree una ejecución de paquete para cada paquete, así no tiene que esperar a que el otro finalice. Este enfoque se beneficia de la escalabilidad de Integration Runtime de SSIS de Azure y mejora el rendimiento global.

## <a name="next-steps"></a>pasos siguientes
Aprenda sobre Integration Runtime de SSIS de Azure. Consulte [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).