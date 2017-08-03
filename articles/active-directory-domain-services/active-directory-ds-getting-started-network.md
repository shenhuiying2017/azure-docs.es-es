---
title: "Azure Active Directory Domain Services: introducción | Microsoft Docs"
description: "Habilitación de Azure Active Directory Domain Services mediante Azure Portal (versión preliminar)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 5e08c37ea63ca45efb4098ca032e1e9e9ffd3c78
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Habilitación de Azure Active Directory Domain Services mediante Azure Portal (versión preliminar)

> [!div class="op_single_selector"]
> * [VERSIÓN PRELIMINAR: habilitación de Azure AD Domain Services mediante Azure Portal](active-directory-ds-getting-started-network.md)
> * [Habilitación de Azure AD Domain Services mediante el Portal de Azure clásico](active-directory-ds-getting-started-create-group.md)


## <a name="before-you-begin"></a>Antes de empezar
Consulte [Consideraciones de red de Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Tarea 2: Configuración de red
La siguiente tarea de configuración es crear una red virtual de Azure y una subred dedicada en ella. Puede habilitar Azure Active Directory Domain Services en esta subred dentro de la red virtual. También puede seleccionar una red virtual existente y crear la subred dedicada en ella.

1. Haga clic en **Red virtual** para seleccionar una red virtual.
2. En la hoja **Elegir red virtual**, se ven todas las redes virtuales existentes. Solo se ven las redes virtuales que pertenecen al grupo de recursos y la ubicación de Azure que ha seleccionado en la página **Conceptos básicos** del asistente.

3. Elija la red virtual existente en la que Azure AD Domain Services debe habilitarse. Haga clic en **Crear nuevo** si prefiere crear una nueva red virtual.

    ![Seleccionar una red virtual](./media/getting-started/domain-services-blade-network-pick-vnet.png)

4. Haga clic en **Subred** para seleccionar la subred dedicada en esta red virtual, en la que va a habilitar su nuevo dominio administrado. En la hoja **Crear subred**, especifique el nombre de la subred y haga clic en **Aceptar** cuando haya terminado. Por ejemplo, cree una subred llamada 'DomainServices', lo que facilitará que otros administradores sepan lo que se implementa en la subred.

    ![Elegir una subred en la red virtual](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Directrices para seleccionar una subred**
  > 1. No seleccione la subred Gateway para implementar Azure AD Domain Services, porque no es una configuración admitida.
  2. Asegúrese de que la subred que ha seleccionado tiene suficiente espacio de direcciones disponible (al menos entre 3 y 5 direcciones IP disponibles).
  >

5. Cuando haya terminado, haga clic en **Aceptar** para ir a la página **Grupo de administradores** del asistente.


## <a name="next-step"></a>Paso siguiente
[Tarea 3: Configuración del grupo administrativo y habilitación de Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)

