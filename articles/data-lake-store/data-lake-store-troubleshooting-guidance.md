---
title: "Preguntas más frecuentes sobre Azure Data Lake Store | Microsoft Docs"
description: "Instrucciones sobre la solución de problemas o sobre como mitigarlos con Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf7fd555-3e30-43ce-b28c-c3ad0a241fdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f426cf4b91941f19714c33abc21ef0c777f5b233
ms.openlocfilehash: 38f3ca24b7fa136bdb4b64b6cae77078b7ea3c97


---
# <a name="frequently-asked-questions-for-azure-data-lake-store"></a>Preguntas más frecuentes sobre Azure Data Lake Store
En este artículo puede revisar las preguntas más frecuentes relacionadas con Azure Data Lake Store.

## <a name="how-can-i-further-protect-my-data-from-region-wide-disasters-or-accidental-deletions"></a>¿Cómo puedo proteger mejor mis datos contra los desastres de toda la región o eliminaciones accidentales?
Los datos de su cuenta de Azure Data Lake Store resisten a los errores transitorios de hardware dentro de una región a través de las réplicas automatizadas. Esto garantiza la durabilidad y alta disponibilidad, en cumplimiento con el Acuerdo de Nivel de Servicio de Azure Data Lake Store. Aquí tiene algunas instrucciones sobre cómo proteger aún más los datos en caso de las poco frecuentes interrupciones de toda la región o de eliminaciones accidentales.

### <a name="disaster-recovery-guidance"></a>Guía de recuperación ante desastres
Es fundamental que todos los clientes preparen su propio plan de recuperación ante desastres. Consulte la documentación de Azure para crear su plan de recuperación ante desastres. Aquí tiene algunos recursos que pueden ayudarle a crear su propio plan.

* [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Guía técnica sobre resistencia en Azure](../resiliency/resiliency-technical-guidance.md)

#### <a name="best-practices"></a>Prácticas recomendadas
Se recomienda que copie los datos críticos en otra cuenta de Data Lake Store en otra región con una frecuencia que esté en consonancia con las necesidades de su plan de recuperación ante desastres. Hay una variedad de métodos para copiar datos entre los que se incluyen [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) o [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md). Azure Data Factory es un servicio útil para crear e implementar las canalizaciones de movimiento de datos de forma periódica.

Si se produce una interrupción regional, podrá tener acceso a los datos en la región donde se han copiado. Puede supervisar el [panel de Estado de Azure](https://azure.microsoft.com/status/) para determinar el estado del servicio de Azure en todo el mundo.

### <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Guía para la recuperación ante un problema de datos dañados o eliminación accidental
Tenga en cuenta que aunque Azure Data Lake Store ofrece resistencia de datos mediante réplicas automatizadas, esto no evita que su aplicación (o los desarrolladores o usuarios) puedan dañar o eliminar de forma no intencionada los datos.

#### <a name="best-practices"></a>Prácticas recomendadas
Para evitar la eliminación accidental, se recomienda establecer directivas de acceso correctas para su cuenta de Data Lake Store usando las [características de seguridad de Data Lake Store](data-lake-store-security-overview.md). También se recomienda que establezca una rutina de creación de copias de los datos críticos mediante [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) o [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) en otra cuenta de Data Lake Store, otra carpeta o suscripción de Azure.  Esto se puede usar para recuperar después de un incidente de daños o eliminación de datos. Azure Data Factory es un servicio útil para crear e implementar las canalizaciones de movimiento de datos de forma periódica.

Las organizaciones también pueden habilitar un [registro de diagnóstico](data-lake-store-diagnostic-logs.md) para su cuenta de Azure Data Lake Store y recopilar seguimientos de auditoría de acceso a datos que proporcionen información sobre quién puede eliminar o actualizar un archivo.

## <a name="next-steps"></a>Pasos siguientes
* [Introducción Azure Data Lake Store](data-lake-store-get-started-portal.md)
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)




<!--HONumber=Nov16_HO5-->


