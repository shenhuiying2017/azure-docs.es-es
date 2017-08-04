---
title: Establecimiento de una directiva de acceso condicional basado en dispositivos para aplicaciones conectadas a Azure Active Directory | Microsoft Docs
description: Establezca directivas de acceso condicional basado en dispositivos para aplicaciones conectadas a Azure AD.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133
ms.contentlocale: es-es
ms.lasthandoff: 12/28/2016

---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Establecimiento de una directiva de acceso condicional basado en dispositivos para aplicaciones conectadas a Azure Active Directory
El acceso condicional basado en dispositivos de Azure Active Directory (Azure AD) puede ayudarle a proteger los recursos de la organización ante:

* Intentos de acceso desde dispositivos desconocidos o no administrados.
* Dispositivos que no cumplan las directivas de seguridad de la organización.

Para ver una introducción al acceso condicional, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md).

Puede establecer directivas de acceso condicional basado en dispositivos para proteger estas aplicaciones:

* Office 365 SharePoint Online, para proteger documentos y sitios de la organización
* Office 365 Exchange Online, para proteger el correo electrónico de la organización
* Aplicaciones de software como servicio (SaaS) que estén conectados a Azure AD para la autenticación
* Aplicaciones locales que se publiquen mediante servicios del proxy de aplicación de Azure AD

Para establecer una directiva de acceso condicional basado en dispositivos, en Azure Portal, vaya a la aplicación específica en el directorio.

  ![Lista de aplicaciones en el directorio de Azure Portal](./media/active-directory-conditional-access-policy-connected-applications/01.png "Aplicaciones")

Seleccione la aplicación y haga clic en la pestaña **Configurar** para establecer la directiva de acceso condicional.  

  ![Configuración de la aplicación](./media/active-directory-conditional-access-policy-connected-applications/02.png "Reglas de acceso basado en dispositivos")

Para establecer una directiva de acceso condicional basado en dispositivos, en la sección **Reglas de acceso basadas en dispositivos**, en **Habilitar reglas de acceso**, seleccione **Activado**.

Una directiva de acceso condicional basado en dispositivos consta de tres componentes:

* **Aplicar a**. Ámbito de los usuarios a los que se aplica la directiva.
* **Reglas del dispositivo**. Las condiciones que debe cumplir un dispositivo para poder acceder a la aplicación.
* **Cumplimiento de la aplicación**. Las aplicaciones cliente (nativas en comparación con las de explorador) a las que se aplica la directiva.
  
  ![Los tres componentes de una directiva de acceso basado en dispositivos](./media/active-directory-conditional-access-policy-connected-applications/03.png "Reglas de acceso basado en dispositivos")

## <a name="select-the-users-the-policy-applies-to"></a>Selección de los usuarios a los que se aplica la directiva
En la sección **Aplicar a** , puede seleccionar el ámbito de los usuarios a los que se aplica esta directiva.

Tiene dos opciones cuando crea un ámbito de directiva de acceso para los usuarios:

* **Todos los usuarios**. Aplique la directiva a todos los usuarios que acceden a la aplicación.
* **Grupos**. Limite la directiva a los usuarios que sean miembros de un grupo específico.

![Aplicación de la directiva a todos los usuarios o a un grupo](./media/active-directory-conditional-access-policy-connected-applications/11.png "Aplicar a")

 Para excluir un usuario de una directiva, active la casilla **Excepto**. Esto resulta útil cuando necesita conceder permisos a un usuario específico para acceder de forma temporal a la aplicación. Por ejemplo, seleccione esta opción si algunos de los usuarios tienen dispositivos que no están preparados para el acceso condicional. Es posible que los dispositivos aún no estén registrados o que estén a punto de entrar en incumplimiento.

## <a name="select-the-conditions-that-devices-must-meet"></a>Selección de las condiciones que deben cumplir los dispositivos
Use **Reglas del dispositivo** para establecer las condiciones que un dispositivo debe cumplir para acceder a la aplicación.

Puede establecer el acceso condicional basado en dispositivos para estos tipos de dispositivo:

* Actualización de aniversario de Windows 10, Windows 8.1 y Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 R2
* Dispositivos iOS (iPad, iPhone)
* Dispositivos Android

La compatibilidad para Mac se agregará próximamente.

  ![Aplicación de la directiva a los dispositivos](./media/active-directory-conditional-access-policy-connected-applications/04.png "Aplicaciones")

> [!NOTE]
> Para más información sobre las diferencias entre dispositivos unidos a un dominio y a Azure AD, consulte [Uso de dispositivos de Windows 10 en el área de trabajo](active-directory-azureadjoin-windows10-devices.md).
> 
> 

Tiene dos opciones para las reglas de dispositivos:

* **Todos los dispositivos deben ser compatibles**. Todas las plataformas de dispositivos que accedan a la aplicación deben ser compatibles. Se deniega el acceso a los dispositivos que se ejecuten en plataformas que no admitan el acceso condicional basado en dispositivos.
* **Solo los dispositivos seleccionados deben ser compatibles**. Solo plataformas de dispositivos específicas deben ser compatibles. Las demás plataformas, o aquellas que pueden acceder a la aplicación, tienen acceso.
  
  ![Establecimiento del ámbito de las reglas de dispositivo](./media/active-directory-conditional-access-policy-connected-applications/05.png "Aplicaciones")

Los dispositivos unidos a Azure AD son compatibles si están marcados como **compatibles** en el directorio en Intune o un sistema de administración de dispositivos móviles de terceros que se integre con Azure AD.

Un dispositivo unido a un dominio es compatible si:

* Está registrado con Azure AD. Muchas organizaciones tratan los dispositivos unidos a un dominio como dispositivos de confianza.
* Está marcado como **compatible** en Azure AD mediante System Center Configuration Manager.
  
  ![Dispositivos unidos a un dominio que son compatibles](./media/active-directory-conditional-access-policy-connected-applications/06.png "Reglas de dispositivo")

Los dispositivos personales Windows son compatibles si están marcados como **compatibles** en el directorio en Intune o un sistema de administración de dispositivos móviles de terceros que se integre con Azure AD.

Los dispositivos que no son de Windows son compatibles si se marcan como **compatibles** en el directorio en Intune.

> [!NOTE]
> Para más información sobre cómo configurar Azure AD para el cumplimiento de dispositivos en diferentes sistemas de administración, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md).
> 
> 

Puede seleccionar una o varias plataformas de dispositivos para una directiva de acceso basado en dispositivos. Entre ellas, se incluyen Android, iOS, Windows Mobile (teléfonos y tabletas con Windows 8.1) y Windows (todos los demás dispositivos Windows, incluidos todos aquellos con Windows 10).
La evaluación de directivas solo se produce en las plataformas seleccionadas. Si un dispositivo que intenta obtener acceso no se está ejecutando en una de las plataformas seleccionadas, puede acceder a la aplicación si el usuario tiene acceso. No se evalúa ninguna directiva de dispositivos.

![Selección de plataformas para las reglas de dispositivo](./media/active-directory-conditional-access-policy-connected-applications/07.png "Reglas de dispositivo")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>Establecimiento de la evaluación de directivas para un tipo de aplicación
En la sección **Cumplimiento de la aplicación**, establezca el tipo de aplicaciones en las que la directiva evaluará el acceso de dispositivos o usuarios.

Tiene dos opciones para el tipo de aplicación que se incluya:

* Aplicaciones nativas y de explorador
* Solo aplicaciones nativas

![Elección de aplicaciones nativas o de explorador](./media/active-directory-conditional-access-policy-connected-applications/08.png "Aplicaciones")

Para aplicar la directiva de acceso para las aplicaciones, seleccione **Para aplicaciones nativas y de explorador**. Después, puede incluir:

* Exploradores (por ejemplo, Microsoft Edge en Windows 10 o Safari en iOS).
* Aplicaciones que usen la biblioteca de autenticación de Active Directory (ADAL) en cualquier plataforma o que usen la API WebAccountManager (WAM) en Windows 10.

> [!NOTE]
> Para más información sobre la compatibilidad con exploradores y otras consideraciones para el usuario que accede a una aplicación protegida por un entidad de certificación y basada en dispositivos, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md).
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Contribución a la protección del acceso de correo electrónico desde aplicaciones basadas en Exchange ActiveSync
En las aplicaciones de Office 365 Exchange Online, puede usar Exchange ActiveSync para bloquear el acceso de correo electrónico a las aplicaciones de correo basadas en Exchange ActiveSync.

![Opciones de cumplimiento de Exchange ActiveSync](./media/active-directory-conditional-access-policy-connected-applications/09.png "Aplicaciones")

![Requerir un dispositivo compatible para acceder al correo electrónico](./media/active-directory-conditional-access-policy-connected-applications/10.png "Aplicaciones")

## <a name="next-steps"></a>Pasos siguientes
* [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)


