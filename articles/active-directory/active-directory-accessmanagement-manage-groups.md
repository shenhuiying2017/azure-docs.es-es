---
title: "Administración de grupos en Azure Active Directory | Microsoft Docs"
description: "Cómo crear y administrar grupos para administrar usuarios de Azure mediante Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d1f5451c-3807-423c-8bac-2822d27b893f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 2cc2b63312b331a19c61cd7b59a4cac78edf32e6
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="managing-groups-in-azure-active-directory"></a>Administración de grupos en Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-groups-create-azure-portal.md)
> * [Portal de Azure clásico](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Una de las características de la administración de usuarios de Azure Active Directory (Azure AD) es la capacidad para crear grupos de usuarios. Los grupos se usan para realizar tareas de administración, como asignar licencias o permisos a varios usuarios a la vez. También puede usarlos para asignar permiso de acceso a:

* Recursos, como objetos en el directorio;
* Recursos de fuera del directorio, como aplicaciones de SaaS, servicios de Azure, sitios de SharePoint o recursos locales.

Además, el propietario de un recurso también puede asignar acceso a un recurso a un grupo de Azure AD cuyo propietario sea otro usuario. La asignación otorga acceso al recurso a los miembros de dicho grupo. A continuación, el propietario del grupo administra la pertenencia al grupo. El propietario del recurso delega efectivamente en el propietario del grupo el permiso para asignar usuarios a sus recursos.

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD con el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal en lugar de usar el portal de Azure clásico al que se hace referencia en este artículo. Para administrar grupos en el Centro de administración de Azure AD, consulte [Creación de un grupo y adición de miembros en Azure Active Directory](active-directory-groups-create-azure-portal.md).

## <a name="how-do-i-create-a-group"></a>¿Cómo se crea un grupo?
En función de los servicios a los que se haya suscrito su organización, podrá crear grupos mediante:

* el Portal de Azure clásico
* el portal de cuentas de Office 365
* el portal de cuentas de Windows Intune

Aquí se describirán las tareas tal como se realizan en el Portal de Azure clásico. Para más información acerca del uso de portales que no sean el de Azure para administrar un directorio de Azure AD, consulte [Administración del directorio de Azure AD](active-directory-administer.md).

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory**y, luego, el nombre del directorio de la organización.
2. Seleccione la pestaña **Grupos** .
3. Seleccione **Agregar grupo**.
4. En la ventana **Agregar grupo** , especifique el nombre y la descripción de un grupo.

## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Asignación o eliminación de usuarios individuales de un grupo de seguridad
**Para agregar un usuario individual a un grupo**

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory**y, luego, el nombre del directorio de la organización.
2. Seleccione la pestaña **Grupos** .
3. Abra el grupo al que desea agregar miembros. Abra la pestaña **Miembros** del grupo seleccionado, en caso de que no se muestre.
4. Seleccione **Agregar miembros**.
5. En la página **Agregar miembros** , seleccione el nombre del usuario o el grupo que desee agregar como miembro de este grupo. Asegúrese de que este nombre se agrega al panel **Seleccionado** .

**Para quitar un usuario individual de un grupo**

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory**y, luego, el nombre del directorio de la organización.
2. Seleccione la pestaña **Grupos** .
3. Abra el grupo del que desea quitar miembros.
4. Seleccione la pestaña **Miembros**, seleccione el nombre del miembro que desee quitar de este grupo y haga clic en **Quitar**.
5. En el símbolo del sistema, confirme que desea quitar este miembro del grupo.

## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>¿Cómo puedo administrar la pertenencia de un grupo dinámicamente?
En Azure AD, puede configurar fácilmente una regla sencilla que determine qué usuarios van a ser miembros del grupo. Una regla sencilla es aquélla que hace solo una comparación única. Por ejemplo, si se asigna un grupo a una aplicación SaaS, puede configurar una regla que agregue los usuarios que tengan el puesto "Representante de ventas". Esta regla otorga acceso a esta aplicación SaaS a todos los usuarios del directorio que tengan dicho puesto.

Cuando los atributos de un usuario cambian, el sistema evalúa todas las reglas de grupos dinámicos de un directorio para ver si la modificación de los atributos del usuario en cuestión desencadenaría adiciones o retiradas en el grupo. Si un usuario cumple una regla de un grupo, se agrega a este como miembro. Si, por el contrario, deja de cumplir la regla del grupo al que pertenece, se le quita como miembro de este.

> [!NOTE]
> Puede configurar una regla de pertenencia dinámica a grupos de seguridad o en grupos de Office 365. Actualmente, las pertenencias a grupos anidados no son compatibles con la asignación basada en grupos a aplicaciones.
>
> La pertenencia dinámica a grupos requiere que haya una licencia de Azure AD Premium asignada
>
> * Al administrador que administra la regla en un grupo
> * Todos los miembros del grupo
>
>

**Para habilitar la pertenencia dinámica para un grupo**

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory**y, luego, el nombre del directorio de la organización.
2. Seleccione la pestaña **Grupos** y abra el grupo que desee editar.
3. Seleccione la pestaña **Configurar** y en **Habilitar pertenencia dinámica**, seleccione **Sí**.
4. Configure una sola regla sencilla para que el grupo controle el funcionamiento de la pertenencia dinámica para este grupo. Asegúrese de que la opción **Agregar usuarios donde** está seleccionada y, a continuación, seleccione una propiedad de usuario en la lista (por ejemplo, department, jobTitle, etc.).
5. A continuación, seleccione una condición (No es igual a, Es igual a, No empieza por, Empieza por, No contiene, Contiene, No coincide, Coincide).
6. Especifique un valor de comparación para la propiedad de usuario seleccionada.

Para obtener información acerca de cómo crear reglas *avanzadas* (reglas que pueden contener comparaciones múltiples) para la pertenencia a grupos dinámicos, consulte [Uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Información adicional
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
