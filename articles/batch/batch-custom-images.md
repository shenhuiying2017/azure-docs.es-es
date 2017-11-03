---
title: "Aprovisionamiento de grupos de Azure Batch desde imágenes personalizadas |Microsoft Docs"
description: "Puede crear un grupo de Batch a partir de una imagen personalizada para aprovisionar los nodos de proceso que contienen el software y los datos que se necesitan para la aplicación. Las imágenes personalizadas son una manera eficaz de configurar los nodos de proceso para ejecutar las cargas de Batch."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 10/11/2017
ms.author: v-dotren
ms.openlocfilehash: d62abd673f89fd51edba721119d1680762a60c76
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Uso de una imagen personalizada administrada para crear un grupo de máquinas virtuales 

Al crear un grupo en Azure Batch con Configuración de máquina virtual, se especifica una imagen de máquina virtual (VM) que proporciona el sistema operativo para cada nodo de proceso en el grupo. Puede crear un grupo de máquinas virtuales con una imagen de Azure Marketplace, o con una imagen personalizada (una imagen de máquina virtual que haya creado y configurado). La imagen personalizada debe ser un recurso de *imagen administrada* en la misma región y suscripción de Azure que la cuenta de Batch.

## <a name="why-use-a-custom-image"></a>¿Por qué usar una imagen personalizada?
Al proporcionar una imagen personalizada, tiene el control sobre la configuración y el tipo del sistema operativo y los discos de datos que se usarán. La imagen personalizada puede incluir los datos de referencia y las aplicaciones que están disponibles en los nodo de grupo de Batch en cuanto se aprovisionan.

El uso de una imagen personalizada le ahorra tiempo a la hora de preparar los nodos de proceso de su grupo para ejecutar la carga de trabajo de Batch. Aunque siempre puede utilizar una imagen de Azure Marketplace e instalar el software en cada nodo de proceso una vez que se haya aprovisionado, el uso de una imagen personalizada podría ser más eficiente.

El uso de una imagen personalizada configurada para su escenario puede proporcionar varias ventajas:

- **Configurar el sistema operativo (OS)**. Puede realizar cualquier configuración especial del sistema operativo en el disco del sistema operativo de la imagen personalizada. 
- **Preinstalar las aplicaciones.** Puede crear una imagen personalizada con aplicaciones instaladas previamente en el disco del sistema operativo, lo que es más eficaz y menos propenso a errores que instalar las aplicaciones después de aprovisionar los nodos de proceso mediante StartTask.
- **Ahorrar tiempo de reinicio en las máquinas virtuales.** La instalación de aplicaciones normalmente requiere reiniciar la máquina virtual, lo que lleva mucho tiempo. Puede ahorrar tiempo de reinicio si instala previamente las aplicaciones. 
- **Copiar grandes cantidades de datos una vez.** Puede convertir en estáticos parte de los datos de la imagen personalizada administrada copiándolos en los discos de datos de una imagen administrada. Solo debe hacerse una vez y los datos estarán disponibles para cada nodo del grupo.
- **Elegir los tipos de disco.** Puede crear una imagen personalizada administrada desde un disco duro virtual, desde un disco administrado de una máquina virtual de Azure, una instantánea de estos discos o su propia instalación de Linux o Windows que haya configurado. Tiene la opción de usar almacenamiento premium para el disco del sistema operativo y el disco de datos.
- **Aumentar los grupos a cualquier tamaño.** Cuando usa una imagen personalizada administrada para crear un grupo, el grupo puede crecer a cualquier tamaño que solicite. No es necesario realizar copias de los discos duros virtuales del blob de imágenes para alojar la cantidad de máquinas virtuales. 


## <a name="prerequisites"></a>Requisitos previos

- **Un recurso de imagen administrada**. Para crear un grupo de máquinas virtuales con una imagen personalizada, tiene que crear un recurso de imagen administrada en la misma suscripción y región de Azure que la cuenta de Batch. Para conocer las opciones para preparar una imagen administrada, vea la sección siguiente.
- **Autenticación de Azure Active Directory (AAD)**. La API de cliente de Batch debe utilizar la autenticación de AAD. La compatibilidad de Azure Batch con AAD se documenta en [Autenticación de soluciones de servicio de Batch con Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Preparación de una imagen personalizada
Puede preparar una imagen administrada desde un disco duro virtual, desde una máquina virtual de Azure con discos administrados o desde una instantánea de máquina virtual. 

Al preparar la imagen, tenga en cuenta las cuestiones siguientes:

* Asegúrese de que la imagen del sistema operativo base que se use para aprovisionar los grupos de Batch no tenga extensiones de Azure preinstaladas, como la extensión de script personalizado. Si la imagen contiene una extensión preinstalada, Azure podría experimentar problemas al implementar la máquina virtual.
* Asegúrese de que la imagen del sistema operativo base que proporcione usa la unidad temporal predeterminada. El agente de nodo de Batch actualmente espera la unidad temporal predeterminada.
* El recurso de imagen administrada a la que un grupo de Batch haga referencia no se puede eliminar mientras se mantenga el grupo. Si se elimina el recurso de imagen administrada, el grupo no puede continuar creciendo. 

### <a name="to-create-a-managed-image"></a>Para crear una imagen administrada
Puede usar cualquier disco del sistema operativo Windows o Linux preparado existente para crear una imagen administrada. Por ejemplo, si desea usar una imagen local, cargue el disco local en una cuenta de Azure Storage que esté en la misma suscripción y región que la cuenta de Batch con AzCopy u otra herramienta de carga. Para obtener los pasos detallados que permiten cargar un disco duro virtual y crear una imagen administrada, consulte las instrucciones para las máquinas virtuales [Windows](../virtual-machines/windows/upload-generalized-managed.md) o [Linux](../virtual-machines/linux/upload-vhd.md).

También puede preparar una imagen personalizada a partir de una máquina virtual de Azure nueva o existente, o de una instantánea de máquina virtual. 

* Si va a crear una máquina virtual nueva, puede usar una imagen de Azure Marketplace como la imagen base de la imagen administrada y, luego, personalizarla. 

* Si planea capturar la imagen mediante el portal, asegúrese de que la máquina virtual se crea con un disco administrado. Se trata de la configuración de almacenamiento predeterminada cuando se crea una máquina virtual.

* Una vez que la máquina virtual está en ejecución, conéctese a ella a través de RDP (para Windows) o SSH (para Linux). Instale el software necesario o copie los datos deseados y, luego, generalice la máquina virtual.  

Para obtener los pasos que permiten generalizar una máquina virtual Azure y crear una imagen administrada, consulte las instrucciones para las máquinas virtuales [Windows](../virtual-machines/windows/capture-image-resource.md) o [Linux](../virtual-machines/linux/capture-image.md).

En función de cómo planee crear un grupo de Batch con la imagen, necesita el siguiente identificador para la imagen:

* Si tiene previsto crear un grupo con la imagen mediante las API de Batch, el **identificador de recurso** de la imagen, que tiene el formato `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Si tiene previsto usar el portal, el **nombre** de la imagen. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Creación de un grupo a partir de una imagen personalizada en el portal

Una vez que guarde la imagen personalizada y conozca su identificador de recurso o su nombre, puede crear un grupo de Batch a partir de esa imagen. En los siguientes pasos se muestra cómo crear un grupo desde Azure Portal.

> [!NOTE]
> Si va a crear el grupo mediante una de las API de Batch, asegúrese de que la identidad que usa para la autenticación de AAD tiene permisos para el recurso de imagen. Vea [Autenticación de soluciones de servicio de Batch con Active Directory](batch-aad-auth.md).
>

1. Vaya a la cuenta de Batch en Azure Portal. Esta cuenta debe estar en la misma suscripción y región que el grupo de recursos que contiene la imagen personalizada. 
2. En la ventana **Configuración** que aparece a la izquierda, seleccione el elemento de menú **Grupos**.
3. En la ventana **Grupos**, seleccione el comando **Agregar**.
4. En la ventana **Agregar grupo**, seleccione **Imagen personalizada (Linux/Windows)** en el menú desplegable **Tipo de imagen**. Desde el menú desplegable **Imagen de VM personalizada**, seleccione el nombre de la imagen (forma abreviada del identificador del recurso).
5. Seleccione el valor correcto de **publicador/oferta/SKU** para la imagen personalizada.
6. Especifique los valores requeridos restantes, incluido el **tamaño de nodo**, los **nodos dedicados de destino** y los **nodos de baja prioridad**, además de cualquier otro valor opcional que desee.

    Por ejemplo, para una imagen personalizada de Microsoft Windows Server Datacenter 2016, la ventana **Agregar grupo** aparece como se muestra a continuación:

    ![Agregar grupo desde imagen personalizada de Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para comprobar si un grupo existente se basa en una imagen personalizada, consulte la propiedad **Operating System** en la sección del resumen de recursos de la ventana **Grupo**. Si el grupo se creó a partir de una imagen personalizada, está establecida en **Imagen de máquina virtual personalizada**.

Todas las imágenes personalizadas asociadas a un grupo se muestran en la ventana **Propiedades** del grupo.
 
## <a name="next-steps"></a>Pasos siguientes

- Para información general más detallada acerca de Batch, consulte [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md).
