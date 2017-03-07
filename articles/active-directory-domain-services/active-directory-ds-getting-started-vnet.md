---
title: "Servicios de dominio de Azure AD: creación o selección de una red virtual | Microsoft Docs"
description: "Introducción a los Servicios de dominio de Azure Active Directory"
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
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9e933774e3b618b1584b4f24a0491eda49e42077
ms.lasthandoff: 12/08/2016


---
# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Creación o selección de una red virtual para Azure Active Directory Domain Services
## <a name="guidelines-to-select-an-azure-virtual-network"></a>Directrices para seleccionar una red virtual de Azure
> [!NOTE]
> **Antes de comenzar**: consulte [Networking considerations for Azure AD Domain Services](active-directory-ds-networking.md)(Consideraciones de redes para Azure Active Directory Domain Services).
>
>

## <a name="task-2-create-an-azure-virtual-network"></a>Tarea 2: Creación de una red virtual de Azure
La siguiente tarea de configuración es crear una red virtual de Azure y una subred dentro de ella. Puede habilitar Azure AD Domain Services en esta subred dentro de la red virtual. Si ya tiene una red virtual que quiere usar, puede omitir este paso.

> [!NOTE]
> Asegúrese de que la red virtual de Azure que cree o elija usar con los Servicios de dominio de Azure AD pertenezca a una región de Azure que sea compatible con dichos servicios. Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure Active Directory Domain Services.
>
>

Anote el nombre de la red virtual para seleccionar la red virtual adecuada al habilitar Azure Active Directory Domain Services en un paso posterior de la configuración.

Realice los siguientes pasos de la configuración para crear una red virtual de Azure en la que desee habilitar Azure Active Directory Domain Services.

1. Navegue hasta el **Portal de Azure clásico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Seleccione el nodo **Redes** en el panel izquierdo.

    ![Nodo Redes](./media/active-directory-domain-services-getting-started/networks-node.png)
3. En la parte inferior de la página, haga clic en **NUEVO** en el panel de tareas.

    ![Nodo Redes virtuales](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. En el nodo **Network Services**, seleccione **Virtual Network**.
5. Haga clic en **Creación rápida** para crear una red virtual.

    ![Red virtual: creación rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
6. Escriba un **Nombre** para la red virtual. También puede elegir configurar el **Espacio de direcciones** o el **Número máximo de VM** para esta red. Por ahora, puede dejar la configuración del **servidor DNS** establecida en 'Ninguna'. Puede actualizar el servidor DNS después de habilitar Azure AD Domain Services.
7. Asegúrese de seleccionar una región de Azure compatible en la lista desplegable **Ubicación** . Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure Active Directory Domain Services.
8. Para crear la red virtual, haga clic en el botón **Crear una red virtual** .

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. Después de crear la red virtual, selecciónela y haga clic en la pestaña **CONFIGURAR**.

    ![Creación de una subred](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Navegue hasta la sección **Espacios de direcciones de la red virtual**. Haga clic en **agregar subred** y especifique una subred con el nombre de **AaddsSubnet**. Haga clic en **Guardar** para crear la subred.

    ![Cree una subred para Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Tarea 3: Habilitación de los Servicios de dominio de Azure AD
La siguiente tarea de configuración consiste en [habilitar Azure Active Directory Domain Services](active-directory-ds-getting-started-enableaadds.md).

