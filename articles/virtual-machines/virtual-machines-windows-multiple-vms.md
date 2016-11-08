---
title: Acerca de varias máquinas virtuales | Microsoft Docs
description: Opciones para la creación varias máquinas virtuales en Windows
services: virtual-machines-windows
documentationcenter: ''
author: gbowerman
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2016
ms.author: guybo

---
# Creación de varias máquinas virtuales de Azure
Existen numerosas situaciones en las que se necesita crear un gran número de máquinas virtuales (VM) similares. Por ejemplo, la informática de alto rendimiento (HPC), los análisis de datos a gran escala, los servidores back-end o de nivel intermedio escalables y, con frecuencia, sin estado (como servidores web), y las bases de datos distribuidas.

En este artículo se describen las opciones disponibles para crear diversas máquinas virtuales en Azure. Estas opciones van más allá de los casos más sencillos donde se crea manualmente una serie de máquinas virtuales. Para crear numerosas máquinas virtuales, los procesos que utiliza normalmente no se adaptan bien a los casos en los que se precisa crear más de unas cuantas máquinas virtuales.

Una manera de crear de numerosas máquinas virtuales similares es utilizar la construcción de Azure Resource Manager de *bucles de recursos*.

## Bucles de recursos
Los bucles de recursos son una forma abreviada sintáctica presente en plantillas de Azure Resource Manager. Los bucles de recursos pueden crear un conjunto de recursos configurados de manera similar en un bucle. Puede utilizar bucles de recursos para crear varias cuentas de almacenamiento, interfaces de red y máquinas virtuales, por ejemplo. Para obtener más información sobre los bucles de recursos, consulte [Create VMs in availability sets using resource loops](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/) (Creación de máquinas virtuales en conjuntos de disponibilidad mediante bucles de recursos).

## Desafíos de la escala
Aunque los bucles de recursos facilitan la construcción de una infraestructura de nube a escala y generan plantillas más concisas, aún quedan pendientes determinados desafíos. Por ejemplo, si usa un bucle de recursos para crear 100 máquinas virtuales, tendrá que correlacionar los controladores de interfaz de red (NIC) con las máquinas virtuales y las cuentas de almacenamiento correspondientes. Como es probable que el número de máquinas virtuales sea diferente del de cuentas de almacenamiento, también tendrá que hacer frente a tamaños de bucle de recursos diferentes. Se trata de solucionar problemas, pero la complejidad aumenta significativamente con la escala.

Otro desafío surge cuando se necesita una infraestructura que se escale de manera flexible. Por ejemplo, es posible que desee una infraestructura de escalado automático que aumente o disminuya el número de máquinas virtuales automáticamente en respuesta a la carga de trabajo. Las máquinas virtuales no proporcionan un mecanismo integrado para variar en número (escalado horizontal y reducción horizontal). Si se realiza la reducción horizontal mediante la eliminación de las máquinas virtuales, es difícil garantizar una alta disponibilidad asegurándose de que las máquinas virtuales estén equilibradas en los dominios de error y actualizaciones.

Por último, cuando se usa un bucle de recursos, el tejido subyacente recibe diversas llamadas para crear recursos. Cuando varias llamadas crean recursos similares, Azure tiene una oportunidad implícita para mejorar este diseño y optimizar el rendimiento y la confiabilidad de la implementación. Aquí es donde entran en luego los *conjuntos de escalado de máquinas virtuales*.

## Conjuntos de escalado de máquinas virtuales
Los conjuntos de escalado de máquinas virtuales son un recurso de Servicios de nube de Azure para implementar y administrar un conjunto de máquinas virtuales idénticas. Si todas las máquinas virtuales están configuradas del mismo modo, resulta sencillo reducir y escalar horizontalmente los conjuntos de escalado de VM. Basta con cambiar el número de máquinas virtuales del conjunto. También puede configurar los conjuntos de escalado de máquinas virtuales para que se escalen automáticamente según las exigencias de la carga de trabajo.

Para las aplicaciones que necesiten escalar y reducir horizontalmente los recursos de proceso, las operaciones de escala se equilibran implícitamente en dominios de actualización y de error.

En lugar de correlacionar varios recursos, como los NIC y las máquinas virtuales, un conjunto de escalado tiene propiedades de extensión, máquina virtual, almacenamiento y red que puede configurar de forma centralizada.

Para obtener una introducción a los conjuntos de escalado de máquinas virtuales, consulte la [página de producto de dichos conjuntos](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Para obtener información más detallada, consulte la [documentación de los conjuntos de escalado de máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0518_2016-->