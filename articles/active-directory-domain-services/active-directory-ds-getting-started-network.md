---
title: "Azure Active Directory Domain Services: introducción | Microsoft Docs"
description: "Habilitación de Azure Active Directory Domain Services mediante Azure Portal"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 680ffc41ab96d69153ef7039698bf9285ed6ce16
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilitación de Azure Active Directory Domain Services mediante Azure Portal


## <a name="before-you-begin"></a>Antes de empezar
Consulte [Consideraciones de red de Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Tarea 2: Configuración de red
La siguiente tarea de configuración es crear una red virtual de Azure y una subred dedicada en ella. Puede habilitar Azure Active Directory Domain Services en esta subred dentro de la red virtual. También puede seleccionar una red virtual existente y crear la subred dedicada en ella.

1. Haga clic en **Red virtual** para seleccionar una red virtual.
    > [!NOTE]
    > **No se admiten redes virtuales clásicas para nuevas implementaciones.** No se admiten redes virtuales clásicas para nuevas implementaciones. Los dominios administrados existentes implementados en redes virtuales clásicas seguirán siendo compatibles. En el futuro se ofrecerá la posibilidad de migrar un dominio administrado existente de una red virtual clásica a una red virtual de Resource Manager.
    >

2. En la página **Elegir red virtual**, se ven todas las redes virtuales existentes. Solo se ven las redes virtuales que pertenecen al grupo de recursos y la ubicación de Azure que ha seleccionado en la página **Conceptos básicos** del asistente.
3. Elija la red virtual en la que Azure AD Domain Services debe habilitarse. Puede seleccionar una red virtual existente o crear una nueva.

  > [!TIP]
  > **El dominio administrado no se puede mover a otra red virtual después de haber habilitado Azure AD Domain Services.** Seleccione la red virtual correcta para habilitar el dominio administrado. Después de crear un dominio administrado, no se puede mover a otra red virtual sin eliminar el dominio administrado. Se recomienda revisar [Consideraciones de red de Azure Active Directory Domain Services](active-directory-ds-networking.md) antes de empezar.  
  >

4. **Crear una red virtual:** haga clic en **Crear nueva** para crear una nueva red virtual. Se recomienda usar una subred dedicada para Azure AD Domain Services. Por ejemplo, cree una subred llamada 'DomainServices', lo que facilitará que otros administradores sepan lo que se implementa en la subred. Cuando haya terminado, haga clic en **Aceptar**.

    ![Seleccionar una red virtual](./media/getting-started/domain-services-blade-network-pick-vnet.png)

5. **Red virtual existente:** si piensa seleccionar una red virtual ya existente, [cree una subred dedicada con la extensión de redes virtuales](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) y después selecciónela. Haga clic en **Virtual Network** para seleccionar la red virtual existente. Haga clic en **Subred** para seleccionar la subred dedicada en la red virtual existente, dentro de la que va a habilitar su nuevo dominio administrado. Cuando haya terminado, haga clic en **Aceptar**.

    ![Elegir una subred en la red virtual](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Directrices para seleccionar una subred**
  > 1. Use una subred dedicada para Azure AD Domain Services. No implemente otras máquinas virtuales en esta subred. Esta configuración permite configurar grupos de seguridad de red (NSG) para las cargas de trabajo o máquinas virtuales sin interrumpir el dominio administrado. Para obtener detalles, vea [Consideraciones de red de Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. No seleccione la subred Gateway para implementar Azure AD Domain Services, porque no es una configuración admitida.
  3. Asegúrese de que la subred que ha seleccionado tiene suficiente espacio de direcciones disponible (al menos entre 3 y 5 direcciones IP disponibles).
  >

6. Cuando haya terminado, haga clic en **Aceptar** para ir a la página **Grupo de administradores** del asistente.


## <a name="next-step"></a>Paso siguiente
[Tarea 3: Configuración del grupo administrativo y habilitación de Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
