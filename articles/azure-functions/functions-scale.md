<properties
   pageTitle="Escalación de Funciones de Azure | Microsoft Azure"
   description="Comprenda cómo se realiza la escalación de Funciones de Azure para satisfacer las necesidades de las cargas de trabajo orientadas a eventos."
   services="functions"
   documentationCenter="na"
   authors="eduardolaureano"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="edlaure"/>

# Escalado de Funciones de Azure

## Introducción

Una ventaja de Funciones de Azure es que los recursos de procesos solo se consumen cuando son necesarios. Esto significa que no paga por las máquinas virtuales inactivas ni tiene que reservar capacidad por si la necesita. En cambio, la plataforma asigna potencia de computación cuando se ejecuta el código, realiza un escalado vertical cuando sea necesario para administrar la carga y reduce el escalado cuando el código no se está ejecutando.

El mecanismo para esta nueva funcionalidad es el plan de servicio dinámico.

En este artículo se proporciona información general sobre cómo funciona el plan de servicio dinámico y cómo se realiza el escalado de la plataforma a petición para ejecutar el código.

Si aún no conoce Funciones de Azure, asegúrese de consultar [Información general sobre Funciones de Azure](functions-overview.md) para comprender mejor sus funcionalidades.

## Configuración de Funciones de Azure

Hay dos configuraciones principales relacionadas con el escalado:

* [Plan del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) o plan de servicio dinámico
* Tamaño de memoria para el entorno de ejecución

El costo de los cambios de función cambia según el plan de servicio que seleccione. Con un plan de servicio dinámico, el costo es en función del tiempo de ejecución, el tamaño de la memoria y el número de ejecuciones. Solo se acumulan cargos cuando el código se está ejecutando.

Un plan del Servicio de aplicaciones hospeda las funciones en máquinas virtuales existentes, que también pueden usarse para ejecutar otro código. Una vez abonado el pago mensual de estas máquinas virtuales, no hay ningún costo adicional para las funciones de ejecución en ellas.

## Selección de un plan de servicio

Cuando crea funciones, puede seleccionar ejecutarlas en un plan de servicio dinámico o en un [plan del Servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). En el plan del Servicio de aplicaciones, las funciones se ejecutarán en una máquina virtual dedicada, como funcionan las aplicaciones web actualmente (para las SKU Premium, Standard o Basic). Esta máquina virtual dedicada se asigna a sus aplicaciones y funciones, y está siempre disponible, con independencia de si se está ejecutando código activamente o no. Se trata de una buena opción si tiene máquinas virtuales infrautilizadas que ya estén ejecutando otro código o si piensa ejecutar funciones de forma continua o casi continua. Una máquina virtual desvincula el costo del tiempo de ejecución y el tamaño de la memoria. Como resultado, puede limitar el costo de muchas funciones de ejecución prolongada al correspondiente a la máquina o máquinas virtuales donde se ejecutan.

[AZURE.INCLUDE [Plan de servicio dinámico](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0601_2016-->