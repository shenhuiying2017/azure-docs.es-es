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

Una de las ventajas de Funciones de Azure es que solo se usan los recursos según lo necesite el código de ejecución. Esto significa que no paga por máquinas virtuales inactivas o que no tiene que reservar capacidad para cuando la necesite. En vez de esto, la plataforma asigna potencia de computación cuando se ejecuta el código, se realiza el escalado vertical según sea necesario para administrar la carga y se vuelve al estado inicial cuando el código no se está ejecutando.

El mecanismo para esta nueva funcionalidad es el Plan de servicio dinámico. Este nuevo plan de servicio proporciona un contenedor dinámico para el código que realiza la escalación vertical a petición, mientras que se le cobra solo la cantidad de memoria que usa el código y el tiempo que tarda en ejecutarse, ya que se mide en segundos por gigabyte.

Este artículo proporciona una visión general de cómo funciona el Plan de servicio dinámico y cómo se realiza la escalación de la plataforma a petición para ejecutar el código.

Si todavía no está familiarizado con Funciones de Azure, asegúrese de comprobar la [Información general sobre Funciones de Azure](functions-overview.md) para comprender mejor sus capacidades.

## Configuración del contenedor de aplicaciones

Hay dos configuraciones principales relacionadas con la escalación:

* El [Plan de servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) o el Plan de servicio dinámico 
* Tamaño de memoria para el entorno de ejecución 

El costo de los cambios de función cambia según el tipo de plan de servicio que seleccione. Con los planes de servicio dinámico, el costo es una función de tiempo de ejecución, el tamaño de la memoria y el número de ejecuciones. Los cambios solo se acumulan cuando está utilizando realmente un código de ejecución.

Los planes de servicio normales le permiten hospedar las funciones en las máquinas virtuales existentes que también pueden usarse para ejecutar otro código. Después de pagar por estas máquinas virtuales al mes, no hay ningún costo adicional para las funciones de ejecución en ellas.

## Selección de un Plan de servicio

Al crear aplicaciones de funciones, puede seleccionar ejecutarlas en un Plan de servicio dinámico (novedad) o en un [Plan de servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) normal. En el Plan de servicio de aplicación, las funciones se ejecutarán en una máquina virtual específica, como funcionan las aplicaciones web hoy en día (para SKU Premium, Standard o Basic). Esta máquina virtual dedicada se asigna a sus aplicaciones o funciones, y está disponible independientemente de cualquier código que se esté ejecutando activamente. Esto es una buena opción si tiene una máquina virtual existente que ya está ejecutando otro código, pero que no se utiliza al completo, o si piensa ejecutar continuamente funciones o hacerlo prácticamente de manera continua. Usar la máquina virtual desvincula el costo del tamaño de la memoria y del tiempo de ejecución, lo que le permite limitar el costo de un gran número de funciones de larga ejecución al costo de uno o más máquinas virtuales que ejecutan.

[AZURE.INCLUDE [Plan de servicio dinámico](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0427_2016-->