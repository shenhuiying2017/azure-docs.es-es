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

Este tema describe consideraciones de diseño para conjuntos de escalado de máquinas virtuales. Para obtener información acerca de lo que son los conjuntos de escalado de máquinas virtuales, consulte [Virtual Machine Scale Sets Overview (Información general de conjuntos de escalado de máquinas virtuales)](virtual-machine-scale-sets-overview.md).


## Almacenamiento

Un conjunto de escalado usa cuentas de almacenamiento para almacenar los discos del sistema operativo de las VM en el conjunto. Se recomienda una proporción de 20 VM por cuenta de almacenamiento o menos. También se recomienda usar las diferentes letras del alfabeto para los caracteres del principio de los nombres de cuenta de almacenamiento. Esto carga ayuda a distribuir la carga en diferentes sistemas internos. Por ejemplo, en la plantilla siguiente, se usa la función de plantilla ARM uniqueString para generar hashes de prefijo que se anteponen a los nombres de cuenta de almacenamiento: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## Aprovisionamiento en exceso

A partir de la versión de API 2016-03-30, los conjuntos de escalado de VM "aprovisionan en exceso" VM de forma predeterminada. Esto significa que el conjunto de escalado realmente pondrá en marcha más máquinas virtuales de las solicitadas y después eliminará las VM que giraron por última vez. Esto mejora las tasas de éxito del aprovisionamiento porque aunque una VM no se aprovisione correctamente, Azure Resource Manager considerá "fallida" toda la implementación. No se le cobrará por estas VM adicionales y no contarán para sus límites de cuota.

Aunque esto mejora las tasas de éxito de aprovisionamiento, puede provocar un comportamiento confuso para una aplicación que no está diseñada para controlar VM que desaparecen sin previo aviso. Para desactivar el aprovisionamiento en exceso, asegúrese de que tiene la cadena siguiente en la plantilla: "overprovision": "false"

Si desactiva el aprovisionamiento en exceso, puede hacerlo con una mayor proporción de VM por cuenta de almacenamiento, pero no se recomienda superar la cifra de 40.


## Límites
Un conjunto de escalas basado en una imagen personalizada (una compilada por usted) debe crear todos los VHD del disco del sistema operativo dentro de una cuenta de almacenamiento. Como resultado, el número máximo recomendado de máquinas virtuales de un conjunto de escalas basado en una imagen personalizada es 20. Si se desactiva el aprovisionamiento en exceso, puede aumentar la cifra hasta 40.

Un conjunto de escalas basado en una imagen de plataforma se limita actualmente a 100 máquinas virtuales; se recomiendan 5 cuentas de almacenamiento para esta escala.

Para que se permitan un número de máquinas virtuales superior a estos límites, debe implementar varios conjuntos de escalas. [Para ver un ejemplo de cómo hacerlo, consulte esta plantilla.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)

<!---HONumber=AcomDC_0803_2016-->