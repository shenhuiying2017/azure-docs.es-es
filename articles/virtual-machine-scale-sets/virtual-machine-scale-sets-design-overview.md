<properties
	pageTitle="Diseño de conjuntos de escalado de máquinas virtuales para escala | Microsoft Azure"
	description="Obtenga información sobre cómo diseñar los conjuntos de escalado de máquinas virtuales para escala"
	keywords="máquina virtual Linux,conjuntos de escalado de máquina virtual" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="gatneil"/>

# Diseño de conjunto de escalado de VM para escala

Este tema analiza consideraciones de diseño para conjuntos de escalado de máquinas virtuales. Para información sobre qué son los conjuntos de escalado de máquinas virtuales, consulte [Información general de conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-overview.md).


## Almacenamiento

Un conjunto de escalado usa cuentas de almacenamiento para almacenar los discos del sistema operativo de las VM en el conjunto. Se recomienda una proporción de 20 VM por cuenta de almacenamiento o menos. También se recomienda usar las diferentes letras del alfabeto para los caracteres del principio de los nombres de cuenta de almacenamiento. Esto ayuda a distribuir la carga en diferentes sistemas internos. Por ejemplo, en la plantilla siguiente, se usa la función de plantilla de Resource Manager uniqueString para generar hashes de prefijo que se anteponen a los nombres de cuenta de almacenamiento: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## Aprovisionamiento en exceso

A partir de la versión de API 2016-03-30, los conjuntos de escalado de VM "aprovisionan en exceso" máquinas virtuales de forma predeterminada. Con el aprovisionamiento en exceso activado, el conjunto de escalado en realidad ejecuta más máquinas virtuales de las que solicita y, luego, elimina las adicionales que se ejecutaron al final. El aprovisionamiento en exceso mejora las tasas de éxito del aprovisionamiento. No se le cobra por estas máquinas virtuales adicionales y no cuentan para sus límites de cuota.

Aunque el aprovisionamiento en exceso mejora las tasas de éxito de aprovisionamiento, puede provocar un comportamiento confuso para una aplicación que no está diseñada para controlar VM que desaparecen sin previo aviso. Para desactivar el aprovisionamiento en exceso, asegúrese de que tiene la cadena siguiente en la plantilla: "overprovision": "false". Puede encontrar más detalles en la [documentación de API de REST de conjuntos de escalado de máquinas virtuales](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Si desactiva el aprovisionamiento en exceso, puede hacerlo con una mayor proporción de VM por cuenta de almacenamiento, pero no se recomienda superar la cifra de 40.


## Límites
Un conjunto de escalas basado en una imagen personalizada (una compilada por usted) debe crear todos los VHD del disco del sistema operativo dentro de una cuenta de almacenamiento. Como resultado, el número máximo recomendado de máquinas virtuales de un conjunto de escalas basado en una imagen personalizada es 20. Si se desactiva el aprovisionamiento en exceso, puede aumentar la cifra hasta 40.

Un conjunto de escalas basado en una imagen de plataforma se limita actualmente a 100 máquinas virtuales; se recomiendan 5 cuentas de almacenamiento para esta escala.

Para poder tener más máquinas virtuales que las que permiten estos límites, debe implementar varios conjuntos de escalado, tal como se muestra en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

<!---HONumber=AcomDC_0817_2016-->