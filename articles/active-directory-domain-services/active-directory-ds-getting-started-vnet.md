---
title: "Azure Active Directory Domain Services: Creación o selección de una red virtual | Microsoft Docs"
description: "Habilitación de Azure Active Directory Domain Services mediante el Portal de Azure clásico"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 457519b00b65b0157effe2d4aba033a1c99852e8
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
<a id="create-or-select-a-virtual-network-for-azure-active-directory-domain-services" class="xliff"></a>

# Creación o selección de una red virtual para Azure Active Directory Domain Services
<a id="before-you-begin" class="xliff"></a>

## Antes de empezar
Consulte [Consideraciones de red de Azure Active Directory Domain Services](active-directory-ds-networking.md).

<a id="task-2-create-an-azure-virtual-network" class="xliff"></a>

## Tarea 2: Creación de una red virtual de Azure
La siguiente tarea de configuración es crear una red virtual de Azure y una subred dentro de ella. Puede habilitar Azure Active Directory Domain Services en esta subred dentro de la red virtual. Si ya tiene una red virtual que quiere usar, puede omitir este paso.

> [!NOTE]
> Asegúrese de que la red virtual de Azure que cree o elija usar con Azure Active Directory Domain Services pertenezca a una región de Azure que sea compatible con estos servicios. Consulte la página [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure Active Directory Domain Services.
>
>Anote el nombre de la red virtual para asegurarse de que selecciona la red virtual adecuada al habilitar Azure Active Directory Domain Services en un paso posterior de la configuración.


Para crear una red virtual de Azure en la que desee habilitar Azure Active Directory Domain Services, siga estas instrucciones de configuración:

1. Vaya al [Portal de Azure clásico](https://manage.windowsazure.com).
2. En el panel izquierdo, seleccione **Redes**.

    ![Nodo Redes](./media/active-directory-domain-services-getting-started/networks-node.png)  
    Se abre la ventana **Redes virtuales**.
3. En la parte inferior de la ventana, haga clic en **Nuevo** en el panel de tareas.

    ![Ventana Redes virtuales](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Haga clic en **Network Services** y seleccione **Virtual Network**.

    ![Red virtual: creación rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. Haga clic en **Creación rápida** para crear una red virtual.

6. Especifique un **nombre** para la red virtual y tenga en cuenta las acciones siguientes:
    * También puede elegir configurar el **Espacio de direcciones** o el **Número máximo de VM** para esta red.
    * Por ahora, puede dejar la configuración del **servidor DNS** establecida en **Ninguna**. Puede actualizar la configuración después de habilitar Azure Active Directory Domain Services.
7. Seleccione una región de Azure compatible en la lista desplegable **Ubicación**.  
    Consulte la página [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure Active Directory Domain Services.
8. Para crear la red virtual, haga clic en **Crear una red virtual**.

    ![Creación de una red virtual para Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. Después de crear una red virtual, selecciónela y haga clic en la pestaña **Configurar**.

    ![Creación de una subred](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. En **Espacios de direcciones de la red virtual**, haga clic en **Agregar subred**y, a continuación, especifique una subred con el nombre **AaddsSubnet**.

    ![Creación de una subred para Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. Haga clic en **Guardar** para crear la subred.


<a id="next-step" class="xliff"></a>

## Paso siguiente
[Tarea 3: Habilitación de Azure Active Directory Domain Services](active-directory-ds-getting-started-enableaadds.md)

