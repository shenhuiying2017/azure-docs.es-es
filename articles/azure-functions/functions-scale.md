---
title: "Elección de un plan de hospedaje de Azure Functions | Microsoft Docs"
description: "Comprenda cómo se realiza la escalación de Funciones de Azure para satisfacer las necesidades de las cargas de trabajo orientadas a eventos."
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/04/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0feeca446a756a076461f381123f63079e0a13db
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="choose-the-correct-hosting-plan-for-azure-functions"></a>Elección del plan de hospedaje correcto para Azure Functions

## <a name="introduction"></a>Introducción

Azure Functions tiene dos planes de servicio diferentes: plan de consumo y plan de App Service. El plan de consumo asigna automáticamente potencia de proceso cuando se ejecuta código, se escala horizontalmente cuando es necesario para gestionar la carga y se reduce horizontalmente cuando no se ejecuta código. Como resultado, no paga por las máquinas virtuales inactivas ni tiene que reservar capacidad antes de que sea necesario. Este artículo se centra en el plan de servicio de consumo. Para más información acerca del funcionamiento del plan de App Service, consulte [Introducción detallada sobre los planes de Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Si todavía no está familiarizado con Azure Functions, consulte el artículo [Información general sobre Azure Functions](functions-overview.md).

## <a name="service-plan-options"></a>Opciones del plan de servicio

Cuando se crea una aplicación de función, debe configurar un plan de hospedaje para las funciones contenidas en la aplicación. Los planes de hospedaje disponibles son: el **Plan de consumo** y el [Plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Actualmente la selección del plan debe realizarse durante la creación de la aplicación de función. Después de su creación no se puede cambiar de opción. Puede escalar entre niveles en el [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Actualmente no se admite ningún cambio para el plan de consumo dado que el escalado es dinámico.

### <a name="consumption-plan"></a>Plan de consumo

En el **plan de consumo**, se asignan aplicaciones de función a una instancia de procesamiento de procesos. Si sea necesario, se agregan o se quitan más instancias de forma dinámica. Además, las funciones se ejecutan en paralelo, con lo que se reduce al máximo el tiempo total necesario para procesar las solicitudes. La aplicación de función contenedora suma el tiempo de ejecución de cada función. El costo se basa en el tamaño de la memoria, y el tiempo total de ejecución entre todas las funciones de una aplicación de función. Use un plan de consumo cuando sus necesidades de proceso sean intermitentes o cuando los tiempos de ejecución de los trabajos sean breves. Este plan permite pagar solo por los recursos de proceso cuando se utilicen. En la siguiente sección se proporcionan más información sobre el funcionamiento del plan de consumo.

### <a name="app-service-plan"></a>Plan de App Service

En el **plan de App Service**, las aplicaciones de función se ejecutan en máquinas virtuales dedicadas, de la misma forma que Web Apps funciona actualmente en las SKU de los niveles Básico, Estándar y Premium. Se asignan máquinas virtuales dedicadas a aplicaciones de App Service y a aplicaciones de función, y siempre están disponibles tanto si se está ejecutando código como si no. Se trata de una buena opción si tiene máquinas virtuales infrautilizadas que ya estén ejecutando otro código o si piensa ejecutar funciones de forma continua o casi continua. Una máquina virtual desvincula el costo del tiempo de ejecución y el tamaño de la memoria. Como consecuencia, puede limitar el costo de muchas funciones de ejecución prolongada al correspondiente a las máquinas virtuales en que se ejecutan. Para más información acerca del funcionamiento del plan de App Service, consulte [Introducción detallada sobre los planes de Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Con un plan de App Service, se puede escalar horizontalmente de forma manual mediante la adición de más instancias de máquina virtual de un solo núcleo o habilitar el escalado automático. Para obtener más información, consulte [Escalación del recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). También puede escalar verticalmente eligiendo un plan de App Service diferente. Vea [Escalado vertical de aplicaciones en Azure](../app-service-web/web-sites-scale.md) para obtener más información. Si va a ejecutar funciones de JavaScript en un plan de App Service, debería elegir un plan con menos núcleos. Para obtener más información, consulte la [referencia de JavaScript relativa a las funciones](functions-reference-node.md#choose-single-core-app-service-plans).  

## <a name="how-the-consumption-plan-works"></a>Funcionamiento del plan de consumo

El plan de consumo escala automáticamente recursos de CPU y memoria al agregar instancias de procesamiento adicionales basadas en las necesidades de las funciones que se ejecutan en la aplicación de función. A cada instancia de procesamiento de la aplicación de función se le asignan hasta 1,5 GB de recursos de memoria.

Cuando se ejecuta en un plan de consumo, si una instancia de Function App pasa a estar inactiva, los nuevos blobs pueden tardar en procesarse hasta 10 minutos. Una vez que Function App se ejecuta, los blobs se procesan más rápidamente. Para evitar este retraso inicial, use un plan de App Service normal con AlwaysOn habilitado o utilice otro mecanismo para desencadenar el procesamiento de blobs, por ejemplo, un mensaje de la cola que contenga el nombre del blob. 

Al crear una Function App, debe crear o vincular una cuenta de Azure Storage de uso general compatible con Blob, Queue y Table Storage. A nivel interno, Azure Functions usa Azure Storage para operaciones como la administración de desencadenadores y las ejecuciones de la función de registro. Algunas cuentas de almacenamiento no son compatibles con colas ni tablas, como las cuentas de almacenamiento solo para blob (incluido Premium Storage) y las cuentas de almacenamiento de propósito general con replicación ZRS. Estas cuentas se filtran en la hoja Cuenta de almacenamiento al crear una Function App.

Al utilizar el plan de hospedaje de consumo, el contenido de Function App (como archivos de código de función y la configuración de enlace) se almacena en recursos compartidos de Azure Files en la cuenta de almacenamiento principal. Si elimina la cuenta de almacenamiento principal, este contenido se suprimirá y no se podrá recuperar.

Para más información sobre los tipos de cuenta de almacenamiento, consulte [Introducción a los servicios de Azure Storage] (../ storage/storage-introduction.md#introducing-the-azure-storage-services).

### <a name="runtime-scaling"></a>Escalado del entorno de tiempo de ejecución

Functions emplea un controlador de escala para evaluar las necesidades de proceso en función de los desencadenadores configurados y para decidir cuándo escalar o reducir horizontalmente. El controlador de escala procesa de forma continua sugerencias de requisitos de memoria y puntos de datos específicos del desencadenador. Por ejemplo, en el caso de un desencadenador de Azure Queue Storage, los puntos de datos incluyen la longitud de la cola y el tiempo de permanencia en la cola de la entrada más antigua.

![](./media/functions-scale/central-listener.png)

La unidad de escalado es la aplicación de función. En este caso, escalar horizontalmente significa agregar más instancias de una aplicación de función. Inversamente, a medida que se reduce la demanda de procesos, se eliminan instancias de Function App. Finalmente, el número de instancias se reduce verticalmente hasta cero cuando no se ejecuta ninguna función. 

### <a name="billing-model"></a>Modelo de facturación

La facturación del plan de consumo se describe de manera detallada en la [página de precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions). El uso se notifica por aplicación de función, solo durante el tiempo en que se ejecuta código. Estas son las unidades de facturación: 
* **Consumo de recursos en GB/s (gigabytes por segundo)**: se calcula como una combinación del tamaño de memoria y el tiempo de ejecución de todas las funciones que se ejecutan en una aplicación de función. 
* **Ejecuciones**: las ejecuciones se cuentan cada vez que se ejecuta una función en respuesta a un evento, desencadenado por un enlace.

