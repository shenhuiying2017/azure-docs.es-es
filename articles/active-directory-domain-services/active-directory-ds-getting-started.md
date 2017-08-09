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
ms.date: 07/15/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 128e70e4abc68ed8c9589dd08a9aec3a52fa49be
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Habilitación de Azure Active Directory Domain Services mediante Azure Portal (versión preliminar)
En este artículo se muestra cómo habilitar Azure Active Directory Domain Services (Azure AD DS) mediante Azure Portal.


Para iniciar el Asistente para **habilitar Azure AD Domain Services**, complete los pasos siguientes:

1. Vaya a [Azure Portal](https://portal.azure.com).
2. En el panel izquierdo, haga clic en **Nuevo**.
3. En la hoja **Nuevo**, escriba **Domain Services** en la barra de búsqueda.

    ![Buscar servicios de dominio](./media/getting-started/search-domain-services.png)

4. Haga clic para seleccionar **Azure AD Domain Services** en la lista de sugerencias de la búsqueda. En la hoja **Azure AD Domain Services**, haga clic en el botón **Crear**.

    ![Hoja Servicios de dominio](./media/getting-started/domain-services-blade.png)

5. Se inicia el Asistente para **habilitar Azure AD Domain Services**.


## <a name="task-1-configure-basic-settings"></a>Tarea 1: Configuración básica
En la página **Conceptos básicos** del asistente, puede especificar el nombre de dominio DNS del dominio administrado. También puede elegir el grupo de recursos y la ubicación de Azure en la que se debe implementar el dominio administrado.

![Configurar conceptos básicos](./media/getting-started/domain-services-blade-basics.png)

1. Elija el **nombre de dominio DNS** del dominio administrado.

   * De forma predeterminada, se especifica el nombre de dominio predeterminado del directorio (con el sufijo **.onmicrosoft.com**).

   * También puede escribir un nombre de dominio personalizado. En este ejemplo, el nombre de dominio personalizado es *contoso100.com*.

     > [!WARNING]
     > El prefijo del nombre de dominio especificado (por ejemplo, *contoso100* en el nombre de dominio *contoso100.com*) debe contener 15 caracteres o menos. No puede crear un dominio administrado con un prefijo de más de 15 caracteres.
     >
     >

2. Asegúrese de que el nombre de dominio DNS que ha elegido para el dominio administrado no existe ya en la red virtual. En concreto, compruebe si:

   * Ya tiene un dominio con el mismo nombre de dominio DNS en la red virtual.

   * La red virtual en la que planea habilitar el dominio administrado tiene una conexión VPN con la red local. En este escenario, asegúrese de que no tiene un dominio con el mismo nombre de dominio DNS de la red local.

   * Si ya dispone de un servicio en la nube con ese nombre en la red virtual.

3. Seleccione la **suscripción** de Azure en la que desea crear el dominio administrado.

4. Seleccione el **grupo de recursos** al que debería pertenecer el dominio administrado. Puede elegir las opciones **Crear nuevo** o **Utilizar existente** para seleccionar el grupo de recursos.

5. Elija la **ubicación** de Azure en que se debe crear el dominio administrado. En la página **Red** del asistente, verá solo las redes virtuales que pertenecen a la ubicación que ha seleccionado.

6. Cuando haya terminado, haga clic en **Aceptar** para ir a la página **Red** del asistente.


## <a name="next-step"></a>Paso siguiente
[Tarea 2: Configuración de red](active-directory-ds-getting-started-network.md)

