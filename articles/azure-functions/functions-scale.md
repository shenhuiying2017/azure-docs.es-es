---
title: Escalado de Azure Functions | Microsoft Docs
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
ms.date: 08/03/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 1a8e8bdb95b1eff6feb992727d28051463710813
ms.openlocfilehash: fce766358c61494e77464a4506dcc0e466e4e1e4


---
# <a name="scaling-azure-functions"></a>Escalado de Azure Functions

## <a name="introduction"></a>Introducción

La plataforma Azure Functions asigna potencia de proceso cuando se ejecuta código, se escala horizontalmente cuando es necesario para gestionar la carga y se reduce horizontalmente cuando no se ejecuta código. Como resultado, no paga por las máquinas virtuales inactivas ni tiene que reservar capacidad antes de que sea necesario. El mecanismo de esta funcionalidad es el plan de servicio de consumo. En este artículo se proporciona información general sobre cómo funciona el plan de servicio de consumo. 

Si todavía no está familiarizado con Azure Functions, consulte el artículo [Información general sobre Azure Functions](functions-overview.md).

## <a name="choose-a-service-plan"></a>Selección de un plan de servicio

Cuando se crea una aplicación de función, debe configurar un plan de hospedaje para las funciones contenidas en la aplicación. Los planes de hospedaje disponibles son: el **Plan de consumo** y el [Plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Actualmente esta elección debe realizarse durante la creación de la aplicación de función. No se puede cambiar entre estas dos opciones tras la creación. Puede escalar entre niveles en el [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Actualmente no se admite ningún cambio para el plan de consumo dado que el escalado es dinámico.

### <a name="consumption-plan"></a>Plan de consumo

En el **plan de consumo**, las aplicaciones de función se asignan a una instancia de procesamiento de proceso. Si es necesario, se agregan o se quitan más instancias de forma dinámica. Además, las funciones se ejecutan en paralelo, con lo que se reduce al máximo el tiempo total necesario para procesar las solicitudes. La aplicación de función contenedora suma el tiempo de ejecución de cada función. El costo se basa en el tamaño de la memoria, y el tiempo total entre todas las funciones de una aplicación de función se mide en gigabytes por segundo. Se trata de una opción excelente si los requisitos de proceso son intermitentes o las horas de trabajo suelen ser muy reducidas, ya que permite pagar solo por los recursos de proceso cuando se estén usando realmente. 

### <a name="app-service-plan"></a>Plan del Servicio de aplicaciones

En el **plan de App Service**, las aplicaciones de función se ejecutan en máquinas virtuales dedicadas, igual que funcionan actualmente las aplicaciones web en SKU básicas, estándar y premium. Las máquinas virtuales dedicadas se asignan a las aplicaciones de App Service y a Function Apps y siempre están disponibles tanto si se está ejecutando código como si no. Se trata de una buena opción si tiene máquinas virtuales infrautilizadas que ya estén ejecutando otro código o si piensa ejecutar funciones de forma continua o casi continua. Una máquina virtual desvincula el costo del tiempo de ejecución y el tamaño de la memoria. Como resultado, puede limitar el costo de muchas funciones de ejecución prolongada al correspondiente a la máquina o máquinas virtuales donde se ejecutan.

## <a name="consumption-service-plan"></a>Plan de servicio de consumo

El plan de servicio de consumo escala automáticamente recursos de CPU y memoria al agregar instancias de procesamiento adicionales basadas en las necesidades de tiempo de ejecución de las funciones en la aplicación de función. A cada instancia de procesamiento de la aplicación de función se le asignan hasta 1,5 GB de recursos de memoria.

### <a name="runtime-scaling"></a>Escalado del entorno de tiempo de ejecución

La plataforma Azure Functions emplea un agente de escucha central para evaluar las necesidades de proceso en función de los desencadenadores configurados y para decidir cuándo escalar o reducir horizontalmente. El agente de escucha central procesa constantemente sugerencias de requisitos de memoria y puntos de datos específicos del desencadenador. Por ejemplo, en el caso de un desencadenador de Queue Storage, esos puntos de datos incluyen la longitud y el tiempo de cola de la entrada más antigua.

![](./media/functions-scale/central-listener.png)

La unidad de escalado es la aplicación de la función. En este caso, escalar horizontalmente significa agregar más instancias de una aplicación de función. Y a la inversa, a medida que se reduce la demanda de procesos, se eliminan instancias de Function App, reduciéndose verticalmente al final a cero cuando no se ejecuta ninguna. 

### <a name="billing-model"></a>Modelo de facturación

La facturación del plan de consumo se describe de manera detallada en la [página de precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions). El uso se notifica por aplicación de función, solo durante el tiempo en el que se ejecuta código. Estas son las unidades de facturación: 
* **Consumo de recursos en GB/s (gigabytes por segundo)**: se calcula como una combinación del tamaño de memoria y el tiempo de ejecución de todas las funciones que se ejecutan en una aplicación de función. 
* **Ejecuciones**: las ejecuciones se cuentan cada vez que se ejecuta una función en respuesta a un evento, desencadenado por un enlace.



<!--HONumber=Nov16_HO4-->


