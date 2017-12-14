---
title: "Capacidad dedicada para trabajos de servicio de ejecución de lotes de Machine Learning | Microsoft Docs"
description: "Información general de los servicios de Azure Batch para trabajos de Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 7f7498c63db89a77121d33afc9d48a4132b1a51d
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>Servicio de Azure Batch para trabajos de Machine Learning

El procesamiento de grupo de lote de Machine Learning proporciona una escala administrada por el cliente para el servicio de ejecución de lotes de Azure Machine Learning. El procesamiento por lotes clásico para aprendizaje automático tiene lugar en un entorno de varios inquilinos, lo que limita el número de trabajos simultáneos que se pueden enviar, y los trabajos se ponen en una cola que funciona según el principio de "el primero en entrar es el primero en salir". Esta incertidumbre significa que no se puede predecir con exactitud cuándo se ejecutará el trabajo.

El procesamiento de grupo de lote permite crear grupos en los que se pueden enviar trabajos por lotes. Usted controla el tamaño del grupo y a qué grupo se envía el trabajo. El trabajo BES se ejecuta en su propio espacio de procesamiento, proporcionando un rendimiento del procesamiento predecible y la capacidad para crear grupos de recursos que corresponden a la carga de procesamiento que se envía.

## <a name="how-to-use-batch-pool-processing"></a>Cómo utilizar el procesamiento de grupo de lote

La configuración del procesamiento de grupo de Batch no está actualmente disponible a través de Azure Portal. Para usar el procesamiento de grupo de lote, tiene que:

-   Llamar a CSS para crear una cuenta de grupo de lote y obtener una dirección URL de servicio de grupo y una clave de autorización
-   Crear un servicio web basado en el nuevo Resource Manager y un plan de facturación

Para crear su cuenta, llame al Soporte técnico y el servicio al cliente de Microsoft (CSS) y proporcione el identificador de su suscripción. CSS trabajará con usted para determinar la capacidad adecuada para su escenario. A continuación, CSS configura la cuenta con el número máximo de grupos que puede crear y el número máximo de máquinas virtuales que se pueden colocar en cada grupo. Una vez que su cuenta está configurada, se le proporciona la dirección URL del servicio de grupo y una clave de autorización.

Después de crear su cuenta, utilice la dirección URL del servicio de grupo y la clave de autorización para realizar operaciones de administración de grupo en el grupo de lote.

![Arquitectura del servicio de grupo de lote.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Cree grupos mediante una llamada a la operación Create Pool (Crear grupo) en la dirección URL de servicio de grupo que le proporcionó CSS. Cuando cree un grupo, especifique el número de máquinas virtuales y la dirección URL de swagger.json de un servicio web de Machine Learning basado en el nuevo Resource Manager. Este servicio web se proporciona para establecer la asociación de facturación. El servicio de grupo de lote utiliza el swagger.json para asociar el grupo a un plan de facturación. Puede ejecutar cualquier servicio web BES, tanto basado en el nuevo Resource Manager como en el clásico, que elija en el grupo.

Puede usar cualquier servicio web basado en el nuevo Resource Manager, pero tenga en cuenta que la facturación de los trabajos se cobra con respecto al plan de facturación asociado a ese servicio. Es posible que desee crear un servicio web y un nuevo plan de facturación específicamente para ejecutar trabajos de grupo de Batch.

Para más información sobre la implementación de servicios web, vea el artículo [Implementar un servicio web de Azure Machine Learning](publish-a-machine-learning-web-service.md).

Una vez que ha creado un grupo, envíe el trabajo BES usando la dirección URL de las solicitudes de lote para el servicio web. Puede optar por enviarlo a un grupo o al procesamiento por lotes clásico. Para enviar un trabajo para procesamiento en un grupo de lote, agregue el siguiente parámetro en el cuerpo de la solicitud de envío del trabajo:

"AzureBatchPoolId": "&lt;ID de grupo&gt;"

Si no se agrega el parámetro, el trabajo se ejecuta en el entorno de procesamiento por lotes clásico. Si el grupo tiene recursos disponibles, el trabajo empieza a ejecutarse inmediatamente. Si el grupo no tiene recursos libres, el trabajo se coloca en cola hasta que un recurso esté disponible.

Si se da cuenta de que periódicamente alcanza la capacidad de los grupos y necesita una mayor capacidad, puede llamar a CSS y trabajar con un representante para aumentar las cuotas.

Solicitud de ejemplo:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Consideraciones al usar el procesamiento de grupo de lote

El procesamiento de grupo de lote es un servicio facturable siempre activado que requiere estar asociado a un plan de facturación basado en Resource Manager. Solo se le facturará por el número de horas de proceso que se esté ejecutando el grupo; independientemente del número de trabajos que se ejecutan durante ese grupo de tiempo. Si crea un grupo, se le facturará según las horas de proceso de cada máquina virtual en el grupo hasta que se elimina el grupo, incluso si no hay trabajos por lotes ejecutándose en el grupo. La facturación para las máquinas virtuales se inicia cuando han terminado de aprovisionarse y se detiene cuando se han eliminado. Puede usar cualquiera de los planes que se encuentra en la [página Machine Learning Precios](https://azure.microsoft.com/pricing/details/machine-learning/).

Ejemplo de facturación:

Si crea un grupo de lote con 2 máquinas virtuales y lo elimina después de 24 horas, el plan de facturación se realiza con un cargo de 48 horas de proceso; independientemente de cuántos trabajos se hayan ejecutado durante ese período.

Si crea un grupo de lote con 4 máquinas virtuales y lo elimina después de 12 horas, el plan de facturación también se realiza con un cargo de 48 horas de proceso.

Se recomienda sondear el estado del trabajo para determinar cuándo se completan los trabajos. Cuando se hayan acabado de ejecutar todos los trabajos, llame a la operación Resize Pool (Cambiar el tamaño del grupo) para establecer el número de máquinas virtuales en el grupo a cero. Si no le llegan los recursos del grupo y necesita crear un nuevo grupo, por ejemplo, para facturar en un plan de facturación diferente, puede eliminar el grupo cuando haya terminado de ejecutar todos los trabajos.


| **Use procesamiento de grupo de lote cuando**    | **Use procesamiento por lotes clásico cuando**  |
|---|---|
|Tiene que ejecutar un gran número de trabajos<br>O<br/>Tiene que asegurarse de que los trabajos se ejecutarán inmediatamente<br/>O<br/>Necesita rendimiento garantizado. Por ejemplo, tiene que ejecutar una serie de trabajos en un determinado período de tiempo y desea escalar horizontalmente los recursos de proceso para satisfacer sus necesidades.    | Tiene unos pocos trabajos para ejecutar<br/>y<br/> No necesita que los trabajos se ejecuten inmediatamente |
