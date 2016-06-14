<properties
	pageTitle="Creación de un laboratorio con Azure DevTest Labs | Microsoft Azure"
	description="Creación de un nuevo laboratorio con Azure DevTest Labs para máquinas virtuales"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Creación de un laboratorio con Laboratorios de desarrollo y pruebas de Azure

## Requisitos previos

Para crear un laboratorio necesitará:

- Una suscripción de Azure. Para obtener información sobre las opciones de compra de Azure, consulte [Instrucciones para contratar Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/). Debe ser el propietario de la suscripción para crear el laboratorio.
- Un grupo de recursos de Azure para el laboratorio. Consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md) y [de Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

## Creación de un laboratorio

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Examinar**.

1. Pulse **DevTest Labs** en la lista.

1. En la hoja **DevTest Labs**, pulse **Agregar**.

    ![Incorporación de un laboratorio](./media/devtest-lab-create-lab/add-lab-button.png)

1. En la hoja **Crear un laboratorio de desarrollo y pruebas**:

    1. Escriba un **Nombre de laboratorio** para el nuevo laboratorio.
    1. Seleccione una **Suscripción** para asociar al laboratorio.
    1. Seleccione una **Ubicación** en la que se va a almacenar el laboratorio.
    1. Seleccione **Crear**.

    ![Creación de una hoja de laboratorio](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## Pasos siguientes

Una vez creado el laboratorio, le presentamos algunos pasos que se deben tener en cuenta:

- [Acceso seguro a un laboratorio](devtest-lab-add-devtest-user.md)

- [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md)

- [Creación de una plantilla de laboratorio](devtest-lab-create-template.md)

- [Creación de artefactos personalizados para máquinas virtuales](devtest-lab-artifact-author.md)

- [Incorporación de una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md)

<!---HONumber=AcomDC_0608_2016-->