---
title: Disponibilidad de SAP HANA entre regiones de Azure | Microsoft Docs
description: Describe la información general de las consideraciones sobre disponibilidad a tener en cuenta cuando se ejecuta SPA HANA en máquinas virtuales de Azure
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
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilidad de SAP HANA entre regiones de Azure
En este artículo se describen y explican escenarios relacionados con la disponibilidad de SAP HANA en diferentes regiones de Azure. Dado que las regiones independientes de Azure tienen mayor distancia entre ellas, existen consideraciones especiales que se enumeran en este artículo.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>Motivos para la implementación en varias regiones de Azure
Las diferentes regiones de Azure están separadas por una distancia mayor. Dependiendo de la región geopolítica, esta distancia puede ser de cientos de millas o incluso varios miles millas, como en Estados Unidos. Debido a la distancia entre las diferentes regiones de Azure, el tráfico de red entre recursos implementados en dos regiones diferentes tiene una latencia de ida y vuelta de red importante. Suficientemente importante como para excluir el intercambio sincrónico de datos entre dos instancias de SAP HANA en una carga de trabajo típica de SAP. Por otro lado, a menudo se presenta el hecho de que existe un requisito definido en relación a la distancia entre el centro de datos principal y un centro de datos secundario, que tiene como finalidad proporcionar disponibilidad en caso de un desastre natural en un área amplia. Por ejemplo, los huracanes que castigaron el área del Caribe y Florida en septiembre y octubre de 2017. O puede al menos haber un requisito de distancia mínima entre los centros. En la mayoría de los casos de cliente, esta definición de distancia mínima le obliga a diseñar una disponibilidad que comprenda varias [regiones de Azure](https://azure.microsoft.com/regions/). Puesto que la distancia es demasiado grande entre dos regiones de Azure para usar el modo de replicación sincrónica de HANA, los requisitos de RTO y el RPO podrían obligarle a implementar configuraciones de disponibilidad dentro de una región, y complementarlas con implementaciones adicionales en una segunda región.

Otro aspecto a tener en cuenta en estas configuraciones es la conmutación por error y la redirección de cliente. Se supone que una conmutación por error entre instancias de SAP HANA en dos regiones diferentes de Azure es siempre una conmutación por error manual. Puesto que la replicación del sistema de SAP HANA tiene el modo de replicación establecido en asincrónico, es posible que haya datos confirmados en la instancia principal de HANA que no hayan llegado aún a la instancia secundaria de HANA. Por lo tanto, no se puede aceptar la conmutación automática por error para configuraciones en las que la replicación es asincrónica. Incluso con una conmutación por error manual controlada, como en un ejercicio de conmutación por error, es necesario tomar medidas para asegurarse de que todos los datos confirmados en el lado principal han llegado a la instancia secundaria, antes de pasar manualmente a la otra región de Azure.
 
Como las redes virtuales de Azure que se implementan en la segunda región de Azure se utilizan con un intervalo de direcciones IP diferente, hay que cambiar la configuración de los clientes de SAP HANA o, preferiblemente, hay que establecer unos pasos para cambiar la resolución de nombres, de forma que los clientes se redirijan a la nueva dirección IP del servidor de la instancia secundaria. El artículo de SAP sobre [recuperación de la conexión de cliente tras una adquisición](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) proporciona una información más detallada.   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilidad simple entre dos regiones de Azure
En este escenario, ha decidido no contar con ninguna configuración de disponibilidad dentro de una única región. Pero tiene la necesidad de tener la carga de trabajo atendida en caso de un desastre. Casos clásicos de sistemas de este tipo son los sistemas que no son de producción. Aunque pueda admitir que el sistema permanezca inactivo durante medio día o incluso un día entero, no puede dejar que no esté disponible durante 48 horas o más. Para realizar una instalación menos costosa, ejecuta otro sistema que es aún menos importante en la máquina virtual que funciona como un destino, o le da un tamaño más pequeño a la máquina virtual en la región secundaria y escoge no cargar previamente los datos. Dado que la conmutación por error va a ser manual y conlleva muchos pasos más para realizar también la conmutación por error de la pila de la aplicación completa, el tiempo adicional para desactivar la máquina virtual, cambiar su tamaño e volverla a iniciar, es aceptable.

> [!NOTE]
> Incluso sin datos precargados en el destino de replicación del sistema HANA, necesitaría al menos 64 GB de memoria y, además, memoria suficiente para mantener los datos del almacén de filas en la memoria de la instancia de destino.

![Dos máquinas virtuales en dos regiones](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> En esta configuración, no puede proporcionar un RPO = 0, dado que el modo de replicación del sistema de HANA es asincrónico. Si tiene que proporcionar un RPO = 0, esta configuración no es una opción.

Se podría realizar un pequeño cambio en la configuración configurando la carga previa de datos. De todas formas dada la naturaleza manual de la conmutación por error y el hecho de que los niveles de aplicación tengan que moverse también a la segunda región, puede que la carga previa de los datos no tenga sentido. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinación de disponibilidad dentro de una región y entre regiones 
Una combinación de disponibilidad dentro de una región y entre regiones pueden estar determinada por:

- Requisito de RPO = 0 dentro de una región de Azure.
- No querer o no poder permitir que las operaciones globales de la empresa se vean afectadas por una catástrofe natural importante que afecte a una región más grande. Tal y como ha sido el caso de algunos huracanes cuyos efectos ha sufrido el Caribe en los últimos años.
- Normativa que demanda distancias entre los sitios principal y secundario que se encuentran claramente por encima de las que las zonas de disponibilidad de Azure pueden proporcionar.

 
En tales casos, debe utilizar lo que en SAP se llama una [configuración SAP HANA Multitier System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) (Replicación del sistema de varios niveles de SAP HANA) con replicación del sistema de HANA. La arquitectura tendría este aspecto:

![tres máquinas virtuales en dos regiones](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Esta configuración proporciona un RPO = 0 con tiempos RTO pequeños dentro de la región principal y además proporciona RPO descendente en caso de un movimiento a la segunda región. Los tiempos RTO en la segunda región dependen de si se configura la carga previa o no. En el caso de muchos clientes, la máquina virtual en la región secundaria se usa para realizar un sistema de prueba. Debido a este uso no se puede hacer una carga previa de los datos.

> [!NOTE]
> Como está utilizando el modo de operación logreplay para la replicación del sistema de HANA que va del nivel 1 al nivel 2 (replicación sincrónica en la región principal), la replicación entre el nivel 2 y el nivel 3 (replicación al sitio secundario) no puede realizarse en el modo de operación delta_datashipping. SAP ofrece detalles de los modos de operación y algunas restricciones en [Operation Modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) (Modos de operación para la replicación de sistema de SAP HANA). 

## <a name="next-steps"></a>Pasos siguientes
Si necesita instrucciones paso a paso sobre cómo establecer una configuración así en Azure, lea los artículos:

- [Configuración de la replicación del sistema de SAP HANA en máquinas virtuales de Azure](sap-hana-high-availability.md)
- [Your SAP on Azure – Part 4 – High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (Su SAP en Azure [Parte 4]: alta disponibilidad en SAP HANA mediante la replicación del sistema)

 



 
  
