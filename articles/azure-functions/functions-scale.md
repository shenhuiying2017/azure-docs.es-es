---
title: Plan de consumo y plan de App Service de Azure Functions | Microsoft Docs
description: "Comprenda cómo se escala Azure Functions para satisfacer las necesidades de las cargas de trabajo orientadas a eventos."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 70e77e09b2e2116153159167af61776398904a3c
ms.contentlocale: es-es
ms.lasthandoff: 08/23/2017

---
# <a name="azure-functions-consumption-and-app-service-plans"></a>Plan de consumo y plan de App Service de Azure Functions 

## <a name="introduction"></a>Introducción

Azure Functions se puede ejecutar de dos formas diferentes: plan de consumo y plan de Azure App Service. El plan de consumo asigna automáticamente capacidad de proceso cuando se ejecuta el código, se amplía horizontalmente cuando es necesario para gestionar la carga y se reduce horizontalmente cuando no se ejecuta código. Por lo tanto, no tiene que pagar por máquinas virtuales inactivas y no tiene que reservar capacidad de antemano. Este artículo se centra en el plan de consumo. Para más información acerca del funcionamiento del plan de App Service, consulte [Introducción detallada sobre los planes de Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Si todavía no está familiarizado con Azure Functions, vea la [Información general sobre Azure Functions](functions-overview.md).

Cuando se crea una aplicación de función, debe configurar un plan de hospedaje para las funciones que contiene la aplicación. En ambos modos, las funciones se ejecutan en una instancia del *host de Azure Functions*. El tipo de plan controla:

* Cómo se escalan horizontalmente las instancias de host.
* Los recursos que están disponibles para cada host.

Actualmente, debe elegir el tipo de plan durante la creación de la aplicación de función. No se puede cambiar después. 

Puede escalar entre niveles en el plan de App Service. En el plan de consumo, Azure Functions controla automáticamente toda la asignación de recursos.

## <a name="consumption-plan"></a>Plan de consumo

Cuando se usa un plan de consumo, las instancias del host de Azure Functions se agregan y quitan de forma dinámica según el número de eventos de entrada. Este plan se escala automáticamente y solo se le cobra por los recursos de proceso cuando se ejecutan las funciones. En un plan de consumo, una función puede ejecutarse durante un máximo de 10 minutos. 

> [!NOTE]
> El tiempo de espera predeterminado para las funciones en un plan de consumo es de cinco minutos. El valor se puede aumentar a 10 minutos para la aplicación de función si se cambia la propiedad `functionTimeout` en [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).

La facturación se basa en el tiempo de ejecución y memoria que se usa, y se agrega para todas las funciones de una aplicación de función. Para más información, consulte la [página de precios de Azure Functions].

El plan de consumo es el valor predeterminado y ofrece las siguientes ventajas:
- Pague solo cuando se ejecutan las funciones.
- Escale horizontalmente de forma automática, incluso durante períodos de gran carga.

## <a name="app-service-plan"></a>Plan de App Service

En el plan de App Service, las aplicaciones de función se ejecutan en máquinas virtuales dedicadas en las SKU de los niveles Básico, Estándar y Premium, de un modo similar a Web Apps. Se asignan máquinas virtuales dedicadas a las aplicaciones de App Service, lo que significa que el host de Functions siempre está en ejecución.

Considere el plan de App Service en los casos siguientes:
- Tiene máquinas virtuales infrautilizadas que ya ejecutan otras instancias de App Service.
- Espera que la aplicación de función se ejecute de forma continua, o casi continua.
- Necesita más opciones de CPU o memoria que las que proporciona el plan de consumo.
- Necesita ejecuciones que superan el tiempo de ejecución máximo permitido en el plan de consumo.

Una máquina virtual desvincula el costo del tiempo de ejecución y el tamaño de la memoria. Como resultado, no tendrá que pagar más que el coste de la instancia de máquina virtual que asigna. Para más información acerca del funcionamiento del plan de App Service, consulte [Introducción detallada sobre los planes de Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Con un plan de App Service, se puede escalar horizontalmente de forma manual mediante la incorporación de más instancias de máquina virtual o se puede habilitar el escalado automático. Para obtener más información, consulte [Escalación del recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). También puede escalar verticalmente eligiendo un plan de App Service diferente. Vea [Escalado vertical de aplicaciones en Azure](../app-service-web/web-sites-scale.md) para obtener más información. Si va a ejecutar funciones de JavaScript en un plan de App Service, debería elegir un plan con menos núcleos. Para obtener más información, consulte la [referencia de JavaScript relativa a las funciones](functions-reference-node.md#choose-single-core-app-service-plans).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### Always On

Si se ejecuta en un plan de App Service, debe habilitar la configuración **Always On** para que la aplicación de función se ejecute correctamente. En un plan de App Service, el tiempo de ejecución de las funciones quedará inactivo después de unos minutos de inactividad, por lo que solo los desencadenadores HTTP podrán "reactivar" las funciones. Del mismo modo, WebJobs debe tener habilitado Always On. 

Always On solo está disponible en un plan de App Service. En un plan de consumo, la plataforma activa automáticamente las aplicaciones de función.

## <a name="storage-account-requirements"></a>Requisitos de la cuenta de almacenamiento

Tanto en el plan de consumo como en el plan de App Service, una aplicación de función requiere una cuenta de Azure Storage que admita almacenamiento de Azure en blobs, colas y tablas. A nivel interno, Azure Functions usa Azure Storage para operaciones como la administración de desencadenadores y las ejecuciones de la función de registro. Algunas cuentas de almacenamiento no son compatibles con colas ni tablas, como las cuentas de almacenamiento solo para blob (incluido Premium Storage) y las cuentas de almacenamiento de uso general con replicación de almacenamiento con redundancia de zona. Estas cuentas se filtran en la hoja **Cuenta de almacenamiento** al crear una aplicación de función.

Para obtener más información sobre los tipos de cuenta de almacenamiento, vea [Introducción de los servicios Azure Storage](../storage/common/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Funcionamiento del plan de consumo

El plan de consumo escala automáticamente los recursos de CPU y la memoria mediante la incorporación de instancias del host de Functions, según el número de eventos desencadenados por las funciones. Cada instancia del host de Functions se limita a 1,5 GB de memoria.

Al usar el plan de consumo, los archivos de código de función se almacenan en recursos compartidos de Azure Files en la cuenta de almacenamiento principal. Si elimina la cuenta de almacenamiento principal, este contenido se suprimirá y no se podrá recuperar.

> [!NOTE]
> Al usar un desencadenador de blobs en un plan de consumo, puede haber un retraso de hasta 10 minutos en el procesamiento de nuevos blobs si una aplicación de función ha quedado inactiva. Después de que la aplicación de función se ejecute, los blobs se procesan inmediatamente. Para evitar este retraso inicial, considere una de las siguientes opciones:
> - Usar un plan de App Service con Always On habilitado
> - Usar otro mecanismo para desencadenar el procesamiento de blobs, por ejemplo, un mensaje de la cola que contenga el nombre del blob Para obtener un ejemplo, vea [Desencadenador de cola con enlace de entrada de blob](functions-bindings-storage-blob.md#input-sample).

### <a name="runtime-scaling"></a>Escalado del entorno de tiempo de ejecución

Azure Functions usa un componente denominado *controlador de escala* para supervisar la tasa de eventos y determinar si se debe ampliar o reducir verticalmente. El controlador de escala usa la heurística para cada tipo de desencadenador. Por ejemplo, al usar un desencadenador de Azure Queue Storage, se escala en función de la longitud de la cola y la antigüedad del mensaje más antiguo de la cola.

La unidad de escalado es la aplicación de función. Al escalar horizontalmente la aplicación de función, se asignan más recursos para ejecutar varias instancias del host de Azure Functions. Por el contrario, si la demanda se reduce, el controlador de escala elimina instancias del host de la función. El número de instancias se reduce verticalmente hasta cero cuando no se ejecuta ninguna función en la aplicación de función.

![Controlador de escala que supervisa los eventos y la creación de instancias](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>Modelo de facturación

La facturación del plan de consumo se describe de manera detallada en la [página de precios de Azure Functions]. El uso se agrega en el nivel de la aplicación de función y solo se cuenta el tiempo que el código de la función está en ejecución. Estas son las unidades de facturación: 
* **Consumo de recursos en gigabytes-segundo (GB-s)**. Se calcula como una combinación del tamaño de la memoria y el tiempo de ejecución de todas las funciones de una aplicación de función. 
* **Ejecuciones**. Se cuenta cada vez que se ejecuta una función en respuesta a un evento, desencadenado por un enlace.

[página de precios de Azure Functions]: https://azure.microsoft.com/pricing/details/functions

