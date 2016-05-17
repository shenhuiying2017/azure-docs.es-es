<properties
	pageTitle="Acerca de varias máquinas virtuales | Microsoft Azure"
	description="Opciones para la creación varias máquinas virtuales en Windows"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="guybo"/>

# Creación de varias máquinas virtuales de Azure

Existen muchos escenarios en los que necesita crear un gran número de máquinas virtuales similares, por ejemplo, en la informática de alto rendimiento (HPC), el análisis de datos de gran escala, los servidores de nivel intermedio o back-end escalables y a menudo sin estado (como servidores web) y bases de datos distribuidas.

En este artículo se describen las opciones disponibles para crear varias máquinas virtuales en Azure que van más allá de los casos sencillos de creación manual de una serie de máquinas virtuales, lo que hace que el escalado no se realice correctamente si necesita crear más de unas cuantas máquinas virtuales.

Una manera de optimizar la creación de muchas máquinas virtuales similares es utilizar la construcción de Azure Resource Manager de _bucles de recursos_.

## Bucles de recursos

Los bucles de recursos son una forma abreviada sintáctica en las plantillas de Azure Resource Manager que le permiten crear un conjunto de recursos configurados de forma similar en un bucle. Puede utilizar bucles de recursos para crear varias cuentas de almacenamiento, interfaces de red y máquinas virtuales, por ejemplo. Para obtener más información sobre los bucles de recursos, consulte [Creación de máquinas virtuales en conjuntos de disponibilidad mediante bucles de recursos](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## Desafíos de la escala

Aunque los bucles de recursos facilitan la construcción de la infraestructura de nube a escala y permiten que las plantillas sean más concisas, algunos desafíos permanecen. Por ejemplo, cuando se crean 100 máquinas virtuales mediante un bucle de recursos, al definir las máquinas virtuales, necesitará correlacionar las NIC con las máquinas virtuales y las cuentas de almacenamiento. Es probable que sea un número diferente de máquinas virtuales para el número de cuentas de almacenamiento y, por lo tanto, tamaños de bucle de recursos diferentes. Se trata de solucionar problemas, pero la complejidad aumenta significativamente con la escala.

Otro desafío se produce cuando se necesita una infraestructura de escalado elástica, por ejemplo el escalado automático, que aumenta o disminuye automáticamente el número de máquinas virtuales en respuesta a la carga de trabajo. Las máquinas virtuales no proporcionan un mecanismo integrado para variar en número (escalado horizontal y reducción horizontal). Si se realiza la reducción horizontal mediante la eliminación de las máquinas virtuales, la garantía de la alta disponibilidad asegurándose de que las máquinas virtuales son dominios de error y actualizaciones equilibradas es otra tarea complicada.

Por último, aunque los bucles de recursos son una forma abreviada sintáctica, al usar una, varias llamadas para crear recursos se dirigen al entramado subyacente. Cuando varias llamadas crean recursos similares, hay una posibilidad implícita de Azure de mejorar este diseño y realizar optimizaciones para mejorar el rendimiento y la confiabilidad de la implementación. Aquí es donde entra en luego los _conjuntos de escalado de máquinas virtuales_.

## Conjuntos de escalado de máquinas virtuales

Los conjuntos de escalado de máquinas virtuales son un recurso de Proceso de Azure para implementar y administrar un conjunto de máquinas virtuales idénticas. Con todas las máquinas virtuales configuradas de la misma forma, es fácil reducir horizontalmente y realizar el escalado horizontal de los conjuntos de escalado de máquinas virtuales cambiando simplemente el número de máquinas virtuales en el conjunto y puede realizarse la configuración para el escalado automático basado en las exigencias de la carga de trabajo.

Para las aplicaciones que necesiten escalar y reducir horizontalmente los recursos de proceso, las operaciones de escala se equilibran implícitamente en dominios de actualización y de error.

En lugar de correlacionar varios recursos como NIC y las máquinas virtuales, un conjunto de escalado tiene propiedades de extensión, máquina virtual, almacenamiento y red que pueden configurar de forma centralizada.

Para obtener una introducción a los conjuntos de escalado de máquinas virtuales, consulte la [página del producto](https://azure.microsoft.com/services/virtual-machine-scale-sets/), y para obtener información más detallada, vaya a la [documentación](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0504_2016-->