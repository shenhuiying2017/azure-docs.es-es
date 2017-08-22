---
title: "Aprovisionamiento de grupos de Azure Batch desde imágenes personalizadas |Microsoft Docs"
description: "Puede crear un grupo de Batch a partir de una imagen personalizada para aprovisionar los nodos de proceso que contienen el software y los datos que se necesitan para la aplicación. Las imágenes personalizadas son una manera eficaz de configurar los nodos de proceso para ejecutar las cargas de Batch."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 1248aeeaa159789b008eb003c2cd7babe0432919
ms.contentlocale: es-es
ms.lasthandoff: 08/09/2017

---

# <a name="use-a-custom-image-to-create-a-pool"></a>Uso de una imagen personalizada para crear un grupo

Al crear un grupo en Azure Batch, se especifica una imagen de máquina virtual (VM) que proporciona la configuración del sistema operativo para cada nodo de proceso en el grupo. Puede crear un grupo con una imagen de Azure Marketplace o proporcionar una personalizada que haya preparado. Al proporcionar una imagen personalizada, controla cómo se configura el sistema operativo en el momento en el que cada nodo de proceso se aprovisiona. La imagen personalizada puede incluir también los datos de referencia y las aplicaciones que están disponibles en el nodo de proceso en cuanto se aprovisiona.

El uso de una imagen personalizada puede ahorrarle tiempo a la hora de conseguir que los nodos de proceso de su grupo estén listos para ejecutar la carga de trabajo de Batch. Aunque siempre puede utilizar una imagen de Azure Marketplace e instalar el software en cada nodo de proceso una vez que se haya aprovisionado, este enfoque puede ser menos eficiente que usar una imagen personalizada. Si necesita instalar aplicaciones de gran tamaño, copiar grandes cantidades de datos o reiniciar la máquina virtual durante el proceso de instalación, considere la posibilidad de usar una imagen personalizada que esté configurada para sus necesidades.  

## <a name="prerequisites"></a>Requisitos previos

- **Una cuenta de Batch creada con el modo de asignación de grupos Suscripción de usuario.** Para utilizar imágenes personalizadas para aprovisionar los grupos de máquinas virtuales, cree la cuenta de Batch con el [modo de asignación de grupos](batch-api-basics.md#pool-allocation-mode) Suscripción de usuario. Con este modo, los grupos de Batch se asignan en la suscripción en la que reside la cuenta. Consulte la sección [Cuenta](batch-api-basics.md#account) de [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md) para obtener información acerca de cómo establecer el modo de asignación del grupo al crear una cuenta de Batch.

- **Una cuenta de Azure Storage.** Para crear un grupo de configuración de máquinas virtuales con una imagen personalizada, necesita una o varias cuentas de Azure Storage estándares para almacenar imágenes VHD personalizadas. Las imágenes personalizadas se almacenan como blobs. Para hacer referencia a sus imágenes personalizadas al crear un grupo, especifique los URI de los blobs VHD de imágenes personalizadas para la propiedad [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) de la propiedad [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf).

    Asegúrese de que las cuentas de almacenamiento cumplen los criterios siguientes:   
    
    - Las cuentas de almacenamiento que contienen los blobs VHD de imágenes personalizadas deben estar en la misma suscripción que la cuenta de Batch (la suscripción de usuario).
    - Las cuentas de almacenamiento especificadas deben estar en la misma región que la cuenta de Batch.
    - Actualmente, solo se admiten las cuentas de almacenamiento estándar de uso general. En un futuro, se admitirá Azure Premium Storage.
    - Puede especificar una cuenta de almacenamiento con varios blobs VHD personalizados o varias cuentas de almacenamiento, que tenga cada una un único blob. Se recomienda usar varias cuentas de almacenamiento para obtener un mejor rendimiento.
    - Un único blob VHD de imágenes personalizadas puede admitir hasta 40 instancias de máquinas virtuales Linux o 20 instancias de máquinas virtuales Windows. Puede crear copias del blob VHD para crear grupos con más máquinas virtuales. Por ejemplo, un grupo con 200 máquinas virtuales Windows necesita 10 blobs VHD únicos especificados para la propiedad **osDisk**.
    
## <a name="prepare-a-custom-image"></a>Preparación de una imagen personalizada

Para preparar una imagen personalizada para usarse con Batch, debe generalizar una instalación existente de Linux o Windows. Al generalizar una instalación de sistema operativo, quita la información específica de la máquina. El resultado es una imagen que se puede instalar en otros equipos o máquinas virtuales.  

Puede utilizar una imagen de Azure Marketplace como la imagen base para la imagen personalizada. Para personalizar la imagen base, cree una máquina virtual de Azure y agréguele sus aplicaciones o datos. A continuación, generalice la máquina virtual para que actúe como su imagen personalizada.   

Para obtener información acerca de cómo preparar imágenes personalizadas de Linux desde máquinas virtuales de Azure, consulte [Creación de una imagen de una máquina virtual o un disco duro virtual](../virtual-machines/linux/capture-image.md). 

Para más información acerca de cómo preparar imágenes personalizadas de Windows a partir de máquinas virtuales de Azure, consulte [Creación de imágenes de máquina virtual personalizadas con Azure PowerShell](../virtual-machines/windows/tutorial-custom-images.md) e [Introducción a Sysprep (preparación del sistema)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). 

> [!IMPORTANT]
> Al preparar la imagen personalizada, tenga en cuenta lo siguiente:
> - Asegúrese de que la imagen del sistema operativo base que se use para aprovisionar los grupos de Batch no tenga extensiones de Azure preinstaladas, como la extensión de script personalizado. Si la imagen contiene una extensión preinstalada, Azure podría experimentar problemas al implementar la máquina virtual.
> - Asegúrese de que la imagen del sistema operativo base proporcionada usa la unidad temporal predeterminada, porque el agente de nodo de Batch espera la unidad temporal predeterminada.
>
>
    
## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Creación de un grupo a partir de una imagen personalizada en el portal

Para crear un grupo a partir de una imagen personalizada mediante Azure Portal:

1. Vaya a la cuenta de Batch en Azure Portal.
2. En la hoja **Configuración**, seleccione el elemento de menú **Grupos**.
3. En la hoja **Grupos**, seleccione el comando **Agregar**, tras lo que aparece la hoja **Agregar grupo**.
4. Seleccione **Imagen personalizada (Linux/Windows)** en la lista desplegable **Tipo de imagen**. El portal muestra el selector **Imagen personalizada**. Elija uno o más VHD en el mismo contenedor y haga clic en el botón **Seleccionar**. 
   En una versión futura, se permitirá seleccionar varios discos duros virtuales de diferentes cuentas de almacenamiento y contenedores.
5. Seleccione el valor correcto de **Publicador/Oferta/SKU** para los VHD personalizados, elija el modo de **Almacenamiento en caché** que desee y rellene el resto de los parámetros para el grupo.
6. Para comprobar si un grupo se basa en una imagen personalizada, vea la propiedad **Operating System** en la sección del resumen de recursos de la hoja **Grupo**. El valor de esta propiedad debe ser **Custom VM image**.
7. Todos los VHD personalizados asociados a un grupo se muestran en la hoja **Propiedades** del grupo.
 
## <a name="next-steps"></a>Pasos siguientes

- Para información general más detallada acerca de Batch, consulte [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md).
- Para obtener información sobre cómo crear una cuenta de Batch, consulte [Creación de una cuenta de Batch con Azure Portal](batch-account-create-portal.md).
