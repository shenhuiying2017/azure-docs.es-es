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
ms.openlocfilehash: 0816c464b6b52747148cc42a55445048901e7595
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Uso de una imagen personalizada para crear un grupo de máquinas virtuales

Cuando se crea un grupo de máquinas virtuales en Azure Batch, se especifica una imagen de máquina virtual (VM) que proporciona el sistema operativo para cada nodo de ejecución del grupo. Es posible crear un grupo de máquinas virtuales ya sea con una imagen de Azure Marketplace o si se proporciona una imagen de VHD personalizada que se haya preparado. Al proporcionar una imagen personalizada, controla cómo se configura el sistema operativo en el momento en el que cada nodo de proceso se aprovisiona. La imagen personalizada puede incluir también los datos de referencia y las aplicaciones que están disponibles en el nodo de proceso en cuanto se aprovisiona.

El uso de una imagen personalizada puede ahorrarle tiempo a la hora de conseguir que los nodos de proceso de su grupo estén listos para ejecutar la carga de trabajo de Batch. Aunque siempre puede utilizar una imagen de Azure Marketplace e instalar el software en cada nodo de proceso una vez que se haya aprovisionado, este enfoque puede ser menos eficiente que usar una imagen personalizada. 

Algunas de las razones para usar una imagen personalizada que está configurada para el escenario en cuestión incluyen la necesidad de:

- **Configurar el sistema operativo (SO)** Cualquier configuración especial del sistema operativo se puede hacer en la imagen personalizada. 
- **Instalar aplicaciones de gran tamaño.** Instalar aplicaciones en una imagen personalizada es más eficaz que instalarlas en cada nodo de ejecución una vez que se aprovisiona.
- **Copiar cantidades importantes de datos.** Si los datos se copian a la imagen personalizada, solo es necesario copiarlos una vez en lugar de hacerlo a cada nodo de ejecución, lo que permite ahorrar tiempo y ancho de banda.
- **Reiniciar la máquina virtual durante el proceso de configuración.** Reiniciar la máquina virtual puede ser un proceso lento, sobre todo si se tienen varios nodos de ejecución.

## <a name="prerequisites"></a>Requisitos previos

- **Una cuenta de Batch creada con el modo de asignación de grupos Suscripción de usuario.** Con este modo, los grupos de Batch se asignan en la suscripción en la que reside la cuenta. Consulte la sección [Cuenta](batch-api-basics.md#account) en [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md) para más información.

- **Una cuenta de Azure Storage.** Para crear un grupo de máquinas virtuales con una imagen personalizada, necesita una cuenta de Azure Storage estándar de uso general en la misma suscripción y región. Si crea una imagen personalizada a partir de una máquina virtual de Azure, luego copiará la imagen a la cuenta de almacenamiento donde reside el disco del SO de la máquina virtual y no será necesario crear una cuenta de almacenamiento independiente. 
    
## <a name="prepare-a-custom-image"></a>Preparación de una imagen personalizada

Para preparar una imagen personalizada para usarse con Batch, debe generalizar una instalación existente de Linux o Windows. Al generalizar una instalación de sistema operativo, quita la información específica de la máquina. El resultado es una imagen que se puede instalar en otros equipos o máquinas virtuales.  

> [!IMPORTANT]
> Actualmente, Batch no admite usar imágenes administradas de Azure para aprovisionar un grupo. La imagen personalizada que se usa para aprovisionar un grupo se debe almacenar en Azure Storage. 
>
> Cuando prepare la imagen personalizada, tenga en cuenta los puntos siguientes:
> - Asegúrese de que la imagen del sistema operativo base que se use para aprovisionar los grupos de Batch no tenga extensiones de Azure preinstaladas, como la extensión de script personalizado. Si la imagen contiene una extensión preinstalada, Azure podría experimentar problemas al implementar la máquina virtual.
> - Asegúrese de que la imagen del sistema operativo base proporcionada usa la unidad temporal predeterminada, porque el agente de nodo de Batch espera la unidad temporal predeterminada.
>
>

Puede usar cualquier imagen preparada de Windows o Linux existente como imagen personalizada. Por ejemplo, si desea usar una imagen local, cargue la imagen a una cuenta de Azure Storage que esté en la misma suscripción y región que la cuenta de Batch con [AzCopy](../storage/storage-use-azcopy.md) u otra herramienta de carga.

También puede preparar una imagen personalizada a partir de una máquina virtual de Azure nueva o existente. Si crea una máquina virtual nueva, puede usar una imagen de Azure Marketplace como la imagen base de la imagen personalizada y, luego, personalizarla. Para personalizar la imagen base, cree una máquina virtual de Azure y agréguele sus aplicaciones o datos. A continuación, generalice la máquina virtual para que actúe como la imagen personalizada y guárdela en Azure Storage. 

Para preparar una imagen personalizada de una máquina virtual de Azure, siga estos pasos:

1. Cree una máquina virtual de Azure **no administrada** a partir de una imagen de Azure Marketplace. Azure Marketplace incluye imágenes para [Windows](../virtual-machines/windows/quick-create-portal.md) y [Linux](../virtual-machines/linux/quick-create-portal.md).
    
    En el paso 3 del proceso de creación de la máquina virtual, asegúrese de seleccionar **No** en la opción **Almacenamiento: Usar discos administrados**. Anote también el nombre de la cuenta de almacenamiento para el disco del SO de la máquina virtual, porque esta cuenta de almacenamiento también será donde Azure guardará la imagen personalizada:

    ![Creación de una máquina virtual no administrada y nota del nombre de la cuenta de almacenamiento](media/batch-custom-images/vm-create-storage.png)
 
2. Complete el proceso de crear la máquina virtual y espere que Azure la asigne. A continuación aparece una imagen que muestra una máquina virtual en Azure Portal en estado en ejecución:

    ![Creación de una máquina virtual a partir de una imagen de Marketplace](media/batch-custom-images/vm-status-running.png)

3. Una vez que la máquina virtual está en ejecución, conéctese a ella a través de RDP (para Windows) o SSH (para Linux). Instale el software necesario o copie los datos deseados y, luego, generalice la máquina virtual. Siga los pasos que se describen en [Generalización de la máquina virtual](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm). 
   
4. Siga los pasos para realizar el [inicio de sesión en Azure PowerShell](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell). Para instalar Azure PowerShell, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0). 

5. Luego, siga los pasos para [desasignar la máquina virtual y establecer el estado en generalizado](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized). 

    En Azure Portal, observe que la máquina virtual está desasignada:

    ![Asegúrese de que la máquina virtual esté desasignada](media/batch-custom-images/vm-status-deallocated.png)

6.  Cree y guarde la imagen de máquina virtual en Azure Storage con el cmdlet [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) de PowerShell. Siga las instrucciones que se describen en [Creación de la imagen](../virtual-machines/windows/sa-copy-generalized.md#create-the-image).
    
    La imagen de máquina virtual se guarda en la cuenta de Azure Storage que se creó cuando se creó la máquina virtual, como se muestra en el paso 1 del procedimiento. El cmdlet Save-AzureRmVMImage guarda la imagen en el contenedor **system** de esa cuenta de almacenamiento. El parámetro `-DestinationContainername` designa un directorio virtual dentro del contenedor **system**. El parámetro `-VHDNamePrefix` especifica un prefijo para el nombre del blob. El prefijo se antepone al nombre del blob con un guión. 

    Por ejemplo, imagine que llama a Save-AzureRmVMImage con los parámetros siguientes:  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    La imagen resultante se guarda el nombre de ubicación y blob que se muestra a continuación:

    ![Ubicación del VHD guardado en el contenedor de sistema](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Una máquina virtual no administrada de Azure crea varias cuentas de almacenamiento para distintos usos. Si no anotó el nombre del contenedor de almacenamiento del disco del SO cuando se creó la máquina virtual, busque la cuenta de almacenamiento asociada que contiene el contenedor **system**. Navegue por el contenedor **system** para buscar la imagen personalizada con los valores que especificó para el comando **Save-AzureRmVMImage**.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Creación de un grupo a partir de una imagen personalizada en el portal

Una vez que guarde la imagen personalizada y conozca la ubicación, puede crear un grupo de Batch a partir de esa imagen. Siga estos pasos para crear un grupo en Azure Portal:

1. Vaya a la cuenta de Batch en Azure Portal. Esta cuenta debe estar en la misma suscripción y región que la cuenta de almacenamiento que contiene la imagen personalizada. 
2. En la ventana **Configuración** que aparece a la izquierda, seleccione el elemento de menú **Grupos**.
3. En la ventana **Grupos**, seleccione el comando **Agregar**.
4. En la ventana **Agregar grupo**, seleccione **Imagen personalizada (Linux/Windows)** en el menú desplegable **Tipo de imagen**. El portal muestra el selector **Imagen personalizada**. Vaya a la cuenta de almacenamiento donde está la imagen personalizada y seleccione el VHD deseado en el contenedor. 
5. Seleccione el valor correcto de **publicador/oferta/SKU** para el VHD personalizado.
6. Especifique los valores requeridos restantes, incluido el **tamaño de nodo**, los **nodos dedicados de destino** y los **nodos de baja prioridad**, además de cualquier otro valor opcional que desee.

    Por ejemplo, para una imagen personalizada de Microsoft Windows Server Datacenter 2016, la ventana **Agregar grupo** aparece como se muestra a continuación:

    ![Agregar grupo desde imagen personalizada de Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para comprobar si un grupo existente se basa en una imagen personalizada, consulte la propiedad **Operating System** en la sección del resumen de recursos de la ventana **Grupo**. Si el grupo se creó a partir de una imagen personalizada, está establecida en **Imagen de máquina virtual personalizada**.

Todos los VHD personalizados asociados a un grupo se muestran en la ventana **Propiedades** del grupo.
 
## <a name="next-steps"></a>Pasos siguientes

- Para información general más detallada acerca de Batch, consulte [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md).
- Para obtener información sobre cómo crear una cuenta de Batch, consulte [Creación de una cuenta de Batch con Azure Portal](batch-account-create-portal.md).