<properties
	pageTitle="Comparación de imágenes personalizadas y fórmulas en DevTest Labs | Microsoft Azure"
	description="Obtenga más información sobre las diferencias entre las imágenes personalizadas y las fórmulas como bases de máquina virtual para que pueda decidir cuál se adapta mejor a su entorno."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Comparación de imágenes personalizadas y fórmulas en DevTest Labs

## Información general
Se pueden usar [imágenes personalizadas](./devtest-lab-create-template.md) y [fórmulas](./devtest-lab-manage-formulas.md) como base para las [nuevas máquinas virtuales creadas](./devtest-lab-add-vm-with-artifacts.md). Pero, la diferencia clave entre imágenes personalizadas y fórmulas es que una imagen personalizada es simplemente una imagen basada en un disco duro virtual, mientras que una fórmula es una imagen basada en un disco duro virtual *además* de opciones preconfiguradas; por ejemplo, tamaño de máquina virtual, red virtual y subred, artefactos, etc. Estas opciones preconfiguradas se configuran con valores predeterminados que se pueden reemplazar en el momento de creación de máquinas virtuales. En este artículo se explican algunas de las ventajas y desventajas del uso de imágenes personalizadas en comparación con el uso de fórmulas.
 
## Ventajas y desventajas de las imágenes personalizadas
Las imágenes personalizadas ofrecen una manera estática e inmutable de crear máquinas virtuales a partir de un entorno deseado.

**Ventajas**
- El aprovisionamiento de máquinas virtuales a partir de imágenes personalizadas es rápido, ya que nada cambia después de poner en marcha la máquina virtual desde la imagen. En otras palabras, no hay ninguna configuración que aplicar, pues la imagen personalizada es tan solo una imagen sin configuración.
- Las máquinas virtuales creadas a partir de una sola imagen personalizada son idénticas.

**Desventajas**
- Si necesita actualizar algún aspecto de la imagen personalizada, hay que volver a crear la imagen.

## Ventajas y desventajas de las fórmulas
Las fórmulas ofrecen una manera dinámica de crear máquinas virtuales a partir de la configuración deseada.

**Ventajas**
- Los cambios de entorno se pueden capturar sobre la marcha mediante artefactos. Por ejemplo, si quiere que una máquina virtual se instale con los bits más recientes de la canalización de entrega de versiones o desea dar de alta el código más reciente del repositorio, basta con especificar un artefacto que implemente los bits más recientes o dé de alta el código más reciente en la fórmula junto con una imagen base de destino. Siempre que se use esta fórmula para crear máquinas virtuales, se implementa o se da de alta el código o los bits más recientes en la máquina virtual.
- Las fórmulas pueden definir la configuración predeterminada que las imágenes personalizadas no pueden proporcionar; por ejemplo, tamaños de máquina virtual y configuración de red virtual.
- La configuración guardada en una fórmula se muestra como valores predeterminados, pero se puede modificar al crear la máquina virtual.

**Desventajas**
- La creación de una máquina virtual a partir de una fórmula puede tardar más que la creación de una máquina virtual a partir de una imagen personalizada.

## Entradas blogs relacionadas

- [Custom images or formulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/) (¿Imágenes personalizadas o fórmulas?)

<!---HONumber=AcomDC_0803_2016-->