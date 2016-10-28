<properties
	pageTitle="Creación de un laboratorio en Azure DevTest Labs | Microsoft Azure"
	description="Creación de un laboratorio en Azure DevTest Labs para máquinas virtuales"
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
	ms.topic="get-started-article"
	ms.date="09/12/2016"
	ms.author="tarcher"/>

# Creación de un laboratorio con Laboratorios de desarrollo y pruebas de Azure

## Requisitos previos

Para crear un laboratorio necesitará:

- Una suscripción de Azure. Para obtener información sobre las opciones de compra de Azure, consulte [Instrucciones para contratar Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/). Debe ser el propietario de la suscripción para crear el laboratorio.

## Pasos para crear un laboratorio con Azure DevTest Labs

Los pasos siguientes muestran cómo usar Azure Portal para crear un laboratorio en Azure DevTest Labs.

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios** y luego seleccione **DevTest Labs** en la lista.

1. En la hoja **DevTest Labs**, pulse **Agregar**.

    ![Incorporación de un laboratorio](./media/devtest-lab-create-lab/add-lab-button.png)

1. En la hoja **Crear un laboratorio de desarrollo y pruebas**:

    1. Escriba un **Nombre de laboratorio** para el nuevo laboratorio.
    
	1. Seleccione una **suscripción** para asociar al laboratorio.
    
	1. Seleccione una **Ubicación** en la que se va a almacenar el laboratorio.
    
	1. Seleccione **Apagado automático** para especificar si desea habilitar y definir los parámetros para el cierre automático de todas las máquinas virtuales del laboratorio.
	
	1. Seleccione el **tipo de almacenamiento** para indicar el tipo de disco de almacenamiento para las máquinas virtuales del laboratorio.
    
	1. Seleccione **Crear**.

    ![Creación de una hoja de laboratorio](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Pasos siguientes

Una vez creado el laboratorio, le presentamos algunos pasos que se deben tener en cuenta:

- [Acceso seguro a un laboratorio](devtest-lab-add-devtest-user.md)

- [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md)

- [Creación de una plantilla de laboratorio](devtest-lab-create-template.md)

- [Creación de artefactos personalizados para máquinas virtuales](devtest-lab-artifact-author.md)

- [Incorporación de una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md)

<!---HONumber=AcomDC_0914_2016-->