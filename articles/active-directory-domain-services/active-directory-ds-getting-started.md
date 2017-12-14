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
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 16b206b97f71eeffbb4fddb9e447649034998672
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilitación de Azure Active Directory Domain Services mediante Azure Portal
En este artículo se muestra cómo habilitar Azure Active Directory Domain Services (Azure AD DS) mediante Azure Portal.

Para iniciar el Asistente para **habilitar Azure AD Domain Services**, complete los pasos siguientes:

1. Vaya a [Azure Portal](https://portal.azure.com).
2. En el panel izquierdo, haga clic en **Nuevo**.
3. En la página **Nuevo**, escriba **Domain Services** en la barra de búsqueda.

    ![Buscar servicios de dominio](./media/getting-started/search-domain-services.png)

4. Haga clic para seleccionar **Azure AD Domain Services** en la lista de sugerencias de la búsqueda. En la página **Azure AD Domain Services**, haga clic en el botón **Crear**.

    ![Vista de Domain Services](./media/getting-started/domain-services-blade.png)

5. Se inicia el Asistente para **habilitar Azure AD Domain Services**.


## <a name="task-1-configure-basic-settings"></a>Tarea 1: Configuración básica
En la página **Conceptos básicos** del asistente, puede especificar el nombre de dominio DNS del dominio administrado. También puede elegir el grupo de recursos y la ubicación de Azure en la que se debe implementar el dominio administrado.

![Configurar conceptos básicos](./media/getting-started/domain-services-blade-basics.png)

1. Elija el **nombre de dominio DNS** del dominio administrado.

   > [!NOTE]
   > **Directrices para seleccionar un nombre de dominio DNS**
   > * **Nombre de dominio integrado:** de forma predeterminada, el asistente especifica el nombre de dominio predeterminado/integrado del directorio (con un sufijo **. onmicrosoft.com**). Si decide habilitar el acceso LDAP seguro en el dominio administrado a través de Internet, cuente con que tendrá problemas para crear un registro DNS público o para obtener un certificado LDAP seguro de una entidad de certificación pública para este nombre de dominio. Microsoft tiene la propiedad del dominio *. onmicrosoft.com* y las entidades de certificación no emitirán certificados que den fe de este dominio.
   * **Nombres de dominio personalizados:** también puede escribir un nombre de dominio personalizado. En este ejemplo, el nombre de dominio personalizado es *contoso100.com*.
   * **Sufijos de dominio no enrutables:** en general, se recomienda evitar un sufijo de nombre de dominio no enrutable. Por ejemplo, es mejor evitar la creación de un dominio con el nombre de dominio DNS "contoso.local". El sufijo DNS ".local" no es enrutable y puede causar problemas con la resolución DNS.
   * **Restricciones en el prefijo de dominio:** el prefijo del nombre de dominio especificado (por ejemplo, *contoso100* en el nombre de dominio *contoso100.com*) tiene que contener un máximo de 15 caracteres. No puede crear un dominio administrado con un prefijo de más de 15 caracteres.
   * **Conflictos de nombre en la red:** asegúrese de que el nombre de dominio DNS que ha elegido para el dominio administrado no existe ya en la red virtual. En concreto, compruebe si:
       * Ya tiene un dominio de Active Directory con el mismo nombre de dominio DNS en la red virtual.
       * La red virtual en la que planea habilitar el dominio administrado tiene una conexión VPN con la red local. En este escenario, asegúrese de que no tiene un dominio con el mismo nombre de dominio DNS de la red local.
       * Si ya dispone de un servicio en la nube con ese nombre en la red virtual.
    >

2. Seleccione la **suscripción** de Azure en la que desea crear el dominio administrado.

3. Seleccione el **grupo de recursos** al que debería pertenecer el dominio administrado. Puede elegir las opciones **Crear nuevo** o **Utilizar existente** para seleccionar el grupo de recursos.

4. Elija la **ubicación** de Azure en que se debe crear el dominio administrado. En la página **Red** del asistente, verá solo las redes virtuales que pertenecen a la ubicación que ha seleccionado.

5. Cuando haya terminado, haga clic en **Aceptar** para ir a la página **Red** del asistente.


## <a name="next-step"></a>Paso siguiente
[Tarea 2: Configuración de red](active-directory-ds-getting-started-network.md)
