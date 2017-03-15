---
title: "Guía de Azure Data Lake Store para la migración entre regiones | Microsoft Docs"
description: "Guía de Azure Data Lake Store para la migración entre regiones"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 3c83a76c589178d5a2a01ed4edd208e42fb2e83e
ms.openlocfilehash: 785f97a28c060f88a0e0f3a5aa9cf6555ed2b5a4
ms.lasthandoff: 02/22/2017


---
# <a name="guidance-to-migrate-azure-data-lake-store-across-regions"></a>Guía para migrar Azure Data Lake Store entre regiones

A medida que Azure Data Lake Store está disponible en nuevas regiones, puede decidir realizar una migración de una sola vez para aprovechar las ventajas de una nueva región.  Aquí hay algunas instrucciones sobre lo que debe considerar al planear y realizar la migración.

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Azure Data Lake Store en dos regiones diferentes**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**.  Para más información, consulte la [introducción a Azure Data Factory](../data-factory/data-factory-introduction.md).


## <a name="guidance-for-migration"></a>Guía para la migración

Se recomienda identificar primero la estrategia de migración adecuada para la aplicación que está escribiendo, leyendo o procesando datos en Azure Data Lake Store. Al elegir una estrategia, debe tener en cuenta los requisitos de disponibilidad de la aplicación (es decir, el tiempo de inactividad); por ejemplo, el enfoque más sencillo sería "levantar y mover" los datos.  Esto significa poner en pausa las aplicaciones en la región antigua mientras todos los datos se copian en la nueva región.  Una vez que se ha completado el proceso de copia, puede reanudar la aplicación en la nueva región y eliminar la cuenta antigua de Azure Data Lake Store.  Sin embargo, habrá un cierto tiempo de inactividad durante esta migración.

O bien, para reducir el tiempo de inactividad, puede empezar a ingerir nuevos datos en la nueva región de forma inmediata y empezar a ejecutar las aplicaciones en la nueva región en cuanto tenga los datos mínimos necesarios.  En segundo plano, puede copiar los datos más antiguos de la antigua cuenta de Azure Data Lake Store a la nueva en la nueva región.  Esto permite hacer el cambio a la nueva región con poco tiempo de inactividad.  Una vez que se han copiado todos los datos antiguos, puede eliminar la cuenta antigua de ADLS.

Otros detalles importantes a tener en cuenta al planear la migración son:

* **Volumen de datos**: el volumen de datos (GB, número de archivos y carpetas, etc.) afectará al tiempo y los recursos necesarios para el traslado.

* **Nombre de la cuenta de Azure Data Lake Store**: el nombre de la nueva cuenta en la nueva región tendrá que ser globalmente único; por ejemplo, si contosoeastus2.azuredatalakestore.net es el nombre de la antigua cuenta de almacén en la zona horaria Este de EE. UU. 2, puede llamar a su cuenta en Norte Europa (UE) contosonortheu.azuredatalakestore.net

* **Elección de herramientas**: para copiar los archivos de Azure Data Lake Store recomendamos usar la [actividad de copia de Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md).   Azure Data Factory admite el movimiento de datos con alto rendimiento y confiabilidad.  Tenga en cuenta que Azure Data Factory solo copiará la jerarquía de carpetas y el contenido de los archivos. Las listas de control de acceso que haya aplicado deberán aplicarse manualmente en la nueva cuenta.  La [guía de optimización y rendimiento de Azure Data Factory](../data-factory/data-factory-copy-activity-performance.md) es una buena referencia con objetivos de rendimiento para escenarios óptimos.  Si desea que los datos se copien en menos tiempo, puede ser necesario utilizar unidades de movimiento de datos de nube adicionales.  Tenga en cuenta que otras herramientas, como ADLCopy, no admiten la copia de datos entre regiones.  

* **[Cargos por ancho de banda](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)**: serán aplicables cargos, ya que los datos se transferirán fuera de una región de Azure.

* **ACL de los datos**: proteja los datos en la nueva región al aplicar ACL a archivos y carpetas.  Puede ver una guía sobre esto [aquí](data-lake-store-secure-data.md).  Se recomienda considerar esta migración como una oportunidad para actualizar y ajustar las ACL.  Sin embargo, si desea usar configuración similar, puede ver las ACL aplicadas a cualquier archivo mediante el portal, [cmdlets de PowerShell](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.datalakestore/v3.1.0/get-azurermdatalakestoreitempermission) o SDK.  

* **Ubicación de los servicios de análisis**: para obtener el mejor rendimiento, los servicios de análisis, como Data Lake Analytics o HDInsight, deben colocarse en la misma región que los datos.  

## <a name="see-also"></a>Consulte también
* [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)

