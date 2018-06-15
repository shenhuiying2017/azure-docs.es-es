---
title: Disponibilidad de SAP HANA entre regiones de Azure | Microsoft Docs
description: Información general de las consideraciones sobre disponibilidad que tener en cuenta cuando se ejecuta SAP HANA en máquinas virtuales de Azure en varias regiones de Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbd1885dd529e4ccd38f2012d56865a2147f64d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842278"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilidad de SAP HANA entre regiones de Azure

En este artículo se describen escenarios relacionados con la disponibilidad de SAP HANA en diferentes regiones de Azure. Debido a la distancia entre las regiones de Azure, configurar la disponibilidad de SAP HANA en varias regiones de Azure implica consideraciones especiales.

## <a name="why-deploy-across-multiple-azure-regions"></a>Motivos para la implementación en varias regiones de Azure

A menudo, las regiones de Azure están separadas por grandes distancias. Dependiendo de la región geopolítica, la distancia entre regiones de Azure puede ser de cientos de kilómetros o incluso de varios miles de kilómetros, como en Estados Unidos. Debido a la distancia, el tráfico de red entre recursos implementados en dos regiones de Azure diferentes tiene una latencia de ida y vuelta de red importante. La latencia es suficientemente importante como para excluir el intercambio sincrónico de datos entre dos instancias de SAP HANA en cargas de trabajo típicas de SAP. 

Por otro lado, las organizaciones suelen tener un requisito de distancia entre la ubicación del centro de datos principal y un centro de datos secundario. Un requisito de distancia ayuda a proporcionar disponibilidad si se produce un desastre natural en una ubicación geográfica más amplia. Por ejemplo, los huracanes que castigaron el área del Caribe y Florida en septiembre y octubre de 2017. Su organización podría tener al menos un requisito de distancia mínima. Para la mayoría de los clientes de Azure, la definición de una distancia mínima obliga a diseñar una disponibilidad que comprenda varias [regiones de Azure](https://azure.microsoft.com/regions/). Puesto que la distancia entre dos regiones de Azure es demasiado grande para usar el modo de replicación sincrónica de HANA, los requisitos de RTO y el RPO podrían obligarle a implementar configuraciones de disponibilidad dentro de una región, y complementarlas con implementaciones adicionales en una segunda región.

Otro aspecto que debe tenerse en cuenta en este escenario es la conmutación por error y la redirección de cliente. Se supone que una conmutación por error entre instancias de SAP HANA en dos regiones diferentes de Azure es siempre una conmutación por error manual. Puesto que la replicación del sistema de SAP HANA tiene el modo de replicación establecido en asincrónico, es posible que haya datos confirmados en la instancia principal de HANA que no hayan llegado aún a la instancia secundaria de HANA. Por lo tanto, la conmutación por error automática no es una opción para configuraciones en las que la replicación es asincrónica. Incluso con una conmutación por error controlada manualmente, como en un ejercicio de conmutación por error, es necesario tomar medidas para asegurarse de que todos los datos confirmados en el lado principal hayan llegado a la instancia secundaria, antes de pasar manualmente a la otra región de Azure.
 
Azure Virtual Network usa un intervalo de direcciones IP distinto. Las direcciones IP se implementan en la segunda región de Azure. Por lo tanto, tendrá que cambiar la configuración del cliente de SAP HANA o, preferiblemente, deberá crear pasos para cambiar la resolución de nombres. De esta manera, los clientes se redirigen a la nueva dirección IP del servidor del sitio secundario. Para más información, consulte el artículo de SAP sobre [recuperación de la conexión de cliente tras una adquisición](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilidad simple entre dos regiones de Azure

Puede decidir no contar con ninguna configuración de disponibilidad dentro de una única región, pero seguir teniendo la necesidad de tener la carga de trabajo atendida en caso de un desastre. Los sistemas que no son de producción son un caso clásico de este tipo. Aunque pueda ser admisible que el sistema permanezca inactivo durante medio día o incluso un día entero, no puede permitir que no esté disponible durante 48 horas o más. Para que la configuración sea menos costosa, ejecute otro sistema que sea aún menos importante en la máquina virtual. El otro sistema funciona como destino. También puede reducir el tamaño de la máquina virtual en la región secundaria y decidir no cargar previamente datos. Dado que la conmutación por error es manual y conlleva muchos pasos más para realizar también la conmutación por error de la pila de la aplicación completa, el tiempo adicional para apagar la máquina virtual, cambiar su tamaño y reiniciarla es aceptable.

> [!NOTE]
> Incluso si no utiliza datos precargados en el destino de replicación del sistema de HANA, necesitará al menos 64 GB de memoria. También necesitará suficiente memoria además de 64 GB para mantener los datos del almacén de filas en la memoria de la instancia de destino.

![Diagrama de dos máquinas virtuales en dos regiones](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> En esta configuración, no puede proporcionar un RPO = 0, dado que el modo de replicación del sistema de HANA es asincrónico. Si tiene que proporcionar un RPO = 0, esta configuración no es una opción.

Un pequeño cambio que puede hacerse en la configuración sería configurar datos como carga previa. De todas formas, dada la naturaleza manual de la conmutación por error y el hecho de que los niveles de aplicación tengan que moverse también a la segunda región, puede que la carga previa de los datos no tenga sentido. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinación de disponibilidad dentro de una región y entre regiones 

Una combinación de disponibilidad dentro de una región y entre regiones puede estar determinada por estos factores:

- Requisito de RPO = 0 dentro de una región de Azure.
- La organización no desea o no puede permitir que las operaciones globales se vean afectadas por una catástrofe natural importante que afecte a una región más grande. Este ha sido el caso de algunos huracanes cuyos efectos se han dejado sentir en el Caribe en los últimos años.
- Normativa que demanda distancias entre los sitios principal y secundario que se encuentran claramente por encima de las que las zonas de disponibilidad de Azure pueden proporcionar.

En tales casos, puede configurar lo que en SAP se llama una [SAP HANA multitier system replication configuration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) (Replicación del sistema de varios niveles de SAP HANA) mediante la replicación del sistema de HANA. La arquitectura tendría este aspecto:

![Diagrama de tres máquinas virtuales en dos regiones](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Esta configuración proporciona un RPO = 0, con un RTO bajo, dentro de la región principal. La configuración también proporciona un RPO aceptable en caso de que se necesite un traslado a la segunda región. Los tiempos de RTO en la segunda región dependen de si los datos se cargan previamente. Muchos clientes usan la máquina virtual en la región secundaria para ejecutar un sistema de prueba. En ese caso de uso, los datos no se pueden cargar previamente.

> [!NOTE]
> Dado que está utilizando el modo de operación **logreplay** para la replicación del sistema de HANA que va del nivel 1 al nivel 2 (replicación sincrónica en la región principal), la replicación entre el nivel 2 y el nivel 3 (replicación al sitio secundario) no puede realizarse en el modo de operación **delta_datashipping**. Para consultar detalles de los modos de operación y algunas restricciones, vea el artículo de SAP [Operation modes for SAP HANA system replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) (Modos de operación para la replicación de sistema de SAP HANA). 

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones paso a paso sobre cómo configurar estas configuraciones en Azure, vea:

- [Configuración de la replicación del sistema de SAP HANA en máquinas virtuales de Azure](sap-hana-high-availability.md)
- [High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (Alta disponibilidad en SAP HANA mediante la replicación del sistema)

 



 
  
