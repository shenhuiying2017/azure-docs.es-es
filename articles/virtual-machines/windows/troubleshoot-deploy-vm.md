---
title: "Solución de problemas de implementación de máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Solución de problemas de implementación de máquinas virtuales Windows en el modelo de implementación de Azure Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: adbe54dec363b454d64340bf3a862cdead2e5435
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Solución de problemas de implementación de máquinas virtuales Windows en Azure

Para solucionar problemas de implementación de la máquina virtual (VM) de Azure, revise la sección [Principales problemas](#top-issues) para conocer errores comunes y sus soluciones.

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**.

## <a name="top-issues"></a>Principales problemas
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>El clúster no admite el tamaño de máquina virtual solicitado
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Vuelva a intentar la solicitud con un tamaño de máquina virtual menor.
- Si no se puede cambiar el tamaño de la máquina virtual solicitada:
    - Detenga todas las máquinas virtuales en el conjunto de disponibilidad. Haga clic en **Grupos de recursos** > su grupo de recursos > **Recursos** > su conjunto de disponibilidad > **Máquinas virtuales** > su máquina virtual > **Detener**.
    - Después de detener todas las máquinas virtuales, cree la máquina virtual con el tamaño deseado.
    - Inicie la nueva máquina virtual en primer lugar y luego seleccione cada una de las máquinas virtuales detenidas y haga clic en Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>El clúster no tiene recursos disponibles
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Vuelva a intentar realizar la solicitud más tarde.
- Si la nueva máquina virtual puede formar parte de un conjunto de disponibilidad diferente
    - Cree una máquina virtual en un conjunto de disponibilidad diferente (en la misma región).
    - Agregue la nueva máquina virtual a la misma red virtual.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>¿Cómo puedo usar e implementar una imagen de cliente de Windows en Azure?

Puede usar Windows 7, Windows 8 o Windows 10 en Azure para escenarios de desarrollo y pruebas siempre que tenga una suscripción adecuada a Visual Studio (anteriormente MSDN). En este [artículo](client-images.md) se describen los requisitos de idoneidad para ejecutar el cliente Windows en Azure y usar las imágenes de la galería de Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>¿Cómo puedo implementar una máquina virtual con la ventaja de uso híbrido (HUB)?

Existen diversas formas de implementar máquinas virtuales Windows con la ventaja de uso híbrido de Azure.

Con una suscripción con un contrato Enterprise:

•  Implementar máquinas virtuales desde imágenes de Marketplace específicas que estén preconfiguradas con la ventaja de uso híbrido de Azure.

Con el contrato Enterprise:

•  Cargar una máquina virtual personalizada e implementar mediante una plantilla de Resource Manager o Azure PowerShell.

Para obtener más información, consulte los siguientes recursos:

 - [Información general sobre la ventaja de uso híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Preguntas más frecuentes descargables](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Ventaja de uso híbrido de Azure para Windows Server y cliente de Windows](hybrid-use-benefit-licensing.md).

 - [Cómo puedo usar la ventaja de uso híbrido de Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Cómo se activa mi crédito mensual para Visual Studio Enterprise (BizSpark)

Para activar el crédito mensual, consulte este [artículo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>¿Cómo se agrega a mi Contrato Enterprise (EA) la oferta Desarrollo/pruebas - Enterprise para obtener acceso a imágenes de cliente Windows?

La posibilidad de crear suscripciones basadas en la oferta Desarrollo/pruebas - Enterprise se restringe a los propietarios de la cuenta a los que un administrador de Enterprise haya concedido permiso para ello. El propietario de la cuenta crea suscripciones a través del portal de cuentas de Azure. Después, debe agregar a los suscriptores activos de Visual Studio como coadministradores. Así, pueden administrar y utilizar los recursos necesarios para el desarrollo y las pruebas. Para más información, consulte [Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Faltan los controladores para mi máquina virtual de la serie N de Windows

Los controladores para las máquinas virtuales basadas en Windows se encuentran [aquí](n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>No puedo encontrar una instancia de la GPU en mi máquina virtual de la serie N

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan Windows Server 2016 o Windows Server 2012 R2, debe instalar los controladores de gráficos de NVIDIA en cada máquina virtual después de la implementación. La información de instalación del controlador está disponible para las [máquinas virtuales Windows](n-series-driver-setup.md) y las [máquinas virtuales Linux](../linux/n-series-driver-setup.md).

## <a name="are-client-images-supported-for-n-series"></a>¿Se admiten imágenes de cliente para la serie N?

Actualmente, Azure solo admite la serie N en las máquinas virtuales que ejecutan los sistemas operativos Windows Server o Linux.

## <a name="is-n-series-vms-available-in-my-region"></a>¿Hay máquinas virtuales de la serie N en mi región?

Puede comprobar la disponibilidad en la [tabla de productos disponibles por región](https://azure.microsoft.com/regions/services) y el precio [aquí](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>¿Qué imágenes de cliente puedo usar e implementar en Azure, y cómo las consigo?

Puede usar Windows 7, Windows 8 o Windows 10 en Azure para escenarios de desarrollo y pruebas siempre que tenga una suscripción adecuada a Visual Studio (anteriormente MSDN). 

- Ciertas imágenes de Windows 10 están disponibles en la galería de Azure dentro de las [ofertas de desarrollo y pruebas a las que puede tener acceso](client-images.md#eligible-offers). 
- Los suscriptores de Visual Studio dentro de cualquier tipo de oferta también pueden [preparar y crear apropiadamente](prepare-for-upload-vhd-image.md) una imagen de Windows 7, Windows 8 o Windows 10 de 64 bits y, luego, [cargarla en Azure](upload-generalized-managed.md). El uso sigue limitado a desarrollo y pruebas por parte de los suscriptores activos de Visual Studio.

En este [artículo](client-images.md) se describen los requisitos de idoneidad para ejecutar el cliente Windows en Azure y usar las imágenes de la galería de Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Al cambiar el tamaño de la máquina virtual, no puedo ver la familia de tamaño de máquina virtual que deseo.

Cuando una máquina virtual se está ejecutando, se implementa en un servidor físico. Los servidores físicos en regiones de Azure se agrupan en clústeres de hardware físico común. El modo de cambiar el tamaño de una máquina virtual que requiere que esta se mueva a clústeres de hardware diferentes difiere en función del modelo de implementación que se usara para implementarla.

- En el caso de las máquinas virtuales implementadas en el modelo de implementación clásico, la implementación del servicio de nube debe quitarse y volver a implementarse para cambiar su tamaño por uno de otra familia de tamaño.

- En el caso de las máquinas virtuales implementadas en el modelo de implementación Resource Manager, debe detener todas las máquinas virtuales dentro del conjunto de disponibilidad antes de cambiar el tamaño de cualquiera de estas.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>El tamaño de máquina virtual enumerado no se admite mientras se implementa en el conjunto de disponibilidad.

Elija un tamaño que se admita en el clúster del conjunto de disponibilidad. Al crear un conjunto de disponibilidad, se recomienda elegir el mayor tamaño de máquina virtual que piense que va a necesitar y tiene que ser la primera implementación para el conjunto de disponibilidad.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>¿Puedo agregar una máquina virtual clásica a un conjunto de disponibilidad?

Sí. Puede agregar una máquina virtual clásica existente a un conjunto de disponibilidad nuevo o existente. Para más información, consulte [Incorporación de una máquina virtual existente a un conjunto de disponibilidad](classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>pasos siguientes
Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/).

Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**.
