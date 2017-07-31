---
title: "Introducción a la concesión de licencias en Azure Active Directory | Microsoft Docs"
description: "Cómo funciona la concesión de licencias en Azure Active Directory y cómo empezar a trabajar con los procedimientos recomendados"
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017

---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>Obtención de una licencia para usted y sus usuarios en Azure Active Directory

> [!div class="op_single_selector"]
> * [Instrucciones de Azure Portal](active-directory-licensing-get-started-azure-portal.md)
> * [Información del Portal de Azure clásico](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) es una solución y plataforma de identidad como servicio (IDaaS) de Microsoft. Azure AD se ofrece en ediciones de servicio diferentes:

* Azure Active Directory Free, que está disponible con los servicios de Microsoft, como Office 365, Dynamics, Microsoft Intune o Azure. Azure AD no genera ningún cargo de consumo en este modo.

* Ediciones de pago de Azure AD, como:
  - Enterprise Mobility + Security 
  - Azure AD Premium (P1 y P2)
  - Azure AD Basic
  - Azure Multi-Factor Authentication

Al igual que muchos de los servicios en línea de Microsoft, la mayoría de las versiones de pago de Azure AD se proporcionan a través de derechos por usuario, como ocurre en Office 365, Microsoft Intune y Azure AD. En estos casos, la compra de servicios se representa con una o varias suscripciones, y cada suscripción incluye algunas licencias compradas previamente en el inquilino. Los derechos por usuario se obtienen mediante:

* La asignación de una licencia. 
* La creación de un vínculo entre el usuario y el producto.
* La habilitación de los componentes del servicio para el usuario.
* El consumo de una de las licencias de prepago.

[Probar Azure AD Premium ahora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Para más información sobre las funcionalidades de servicio de Azure AD, consulte [¿Qué es Azure AD?](active-directory-whatis.md). Para más información, consulte la [página de Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/).

> [!NOTE]
> Las suscripciones de pago por uso de Azure permiten la creación de recursos de Azure y su posterior asignación a un método de pago. En este caso, no hay recuentos de licencias asociados a la suscripción. Cuando se concede un permiso de usuario para usar los recursos de Azure asignados a la suscripción, estos se asocian a la suscripción y puede administrarlos.

## <a name="how-does-azure-active-directory-licensing-work"></a>¿Cómo funciona la concesión de licencias en Azure Active Directory?

Los servicios de Azure AD basados en licencia funcionan mediante la activación de una suscripción en el inquilino del servicio de Azure AD. Una vez activa la suscripción, los administradores de Azure AD pueden administrar las funcionalidades del servicio y los usuarios con licencia pueden usarlas.

### <a name="manage-subscription-information"></a>Información sobre la administración de suscripciones

Cuando compra Enterprise Mobility + Security, Azure AD Premium o Azure AD Basic, el inquilino se actualiza con la suscripción, lo que incluye su período de validez y las licencias de prepago. La información de suscripción, incluido el número de licencias asignadas o disponibles, se encuentra en Azure Portal: en **Azure Active Directory**, abra el icono de **Licencias** para el directorio específico. La hoja **Licencias** es también el mejor lugar para administrar las asignaciones de licencias.

Cada suscripción está formada por uno o varios planes de servicio Azure AD, Multi-Factor Authentication, Intune, Exchange Online o SharePoint Online.  La administración de licencias de Azure AD *no* requiere administración a nivel del plan de servicio. Esto es diferente en Office 365, que se basa en este modo de configuración avanzada para administrar el acceso a los servicios incluidos. Azure AD se basa en la configuración del servicio para habilitar las características y administrar los permisos individuales.

> [!IMPORTANT]
> Las suscripciones a Azure AD Premium, Azure AD Basic y Enterprise Mobility + Security se limitan a su inquilino o directorio aprovisionado. Las suscripciones no se pueden dividir entre directorios ni usarse para autorizar a los usuarios en otros directorios. Es posible mover una suscripción entre directorios, pero requiere el envío de una incidencia de soporte técnico o la cancelación y compra de nuevo en caso de compras directas.
>
> Cuando se compra Azure AD o Enterprise Mobility + Security mediante una suscripción de licencias por volumen y cuando el acuerdo incluye otros servicios de Microsoft Online (por ejemplo, Office 365), la activación tiene lugar automáticamente. 

### <a name="assign-licenses"></a>Asignación de licencias

Aunque para configurar las funcionalidades de pago todo lo que necesita es una suscripción, debe distribuir licencias a los usuarios para las características de pago de Azure AD. Se debe asignar una licencia a los usuarios que deban disponer de acceso o que puedan administrarse a través de una característica de pago de Azure AD. Una asignación de licencia es una asignación entre un usuario y un servicio comprado, como Azure AD Premium, Basic o Enterprise Mobility + Security.


La administración de qué usuarios de su directorio deben tener una licencia se puede realizar de la forma siguiente: 

* Mediante la asignación de licencias a grupos en [Azure Portal](active-directory-licensing-whatis-azure-portal.md).
* Mediante la asignación directa de licencias por medio de portal, PowerShell o las API. 

Al asignar licencias a un grupo, todos los miembros del grupo reciben una licencia. Si se agregan usuarios al grupo o se quitan de él, la licencia correspondiente se asigna o se quita. La asignación de grupo puede usar cualquier administración de grupo disponible para usted y que sea coherente con la asignación a las aplicaciones basada en grupo.

Puede usar [asignación de licencias basada en grupo](active-directory-licensing-whatis-azure-portal.md) para configurar reglas como las siguientes:
* Todos los usuarios del directorio reciben automáticamente una licencia
* Todos los usuarios con el puesto adecuado reciben una licencia
* Puede delegar la decisión en otros administradores de la organización (mediante [grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)).

Para ver una explicación detallada sobre la asignación de licencias a grupos, incluidos escenarios avanzados y escenarios de concesión de licencias de Office 365, consulte [Asignación de licencias a usuarios según su pertenencia a un grupo en Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="get-started-with-azure-ad-licensing"></a>Introducción a la concesión de licencias en Azure AD

Empezar a trabajar con Azure AD es fácil. Siempre puede crear su directorio como parte del registro para una [evaluación gratuita de Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Los siguientes procedimientos recomendados pueden ayudar a garantizar que el inquilino está en consonancia con otros servicios de Microsoft que podría estar consumiendo y con los objetivos del servicio:

- Si ya usa cualquiera de los servicios organizativos de Microsoft, ya dispone de un inquilino de Azure AD. Ya que resulta de utilidad usar el mismo inquilino para otros servicios, se puede usar la administración básica de identidades, lo que incluye el aprovisionamiento y el inicio de sesión único (SSO) híbrido, en todos los servicios. Con el inicio de sesión único, los usuarios se benefician de las completas funcionalidades presentes en todos los servicios. Por lo tanto, si decide comprar un servicio de pago de Azure AD para sus recursos, se recomienda usar de nuevo el mismo inquilino.

- Es recomendable que use un nuevo inquilino en Azure Portal, si tiene pensado:
  - Usar Azure AD para un conjunto diferente de usuarios (por ejemplo, asociados o clientes).
  - Evaluar los servicios de Azure AD de forma aislada de su servicio de producción.
  - Configurar un entorno aislado para sus servicios.

  El nuevo directorio se crea con su cuenta como un usuario externo con permisos de administrador global. Cuando inicie sesión en Azure Portal con esta cuenta, podrá ver este inquilino y acceder a todas las tareas de administración.

> [!NOTE]
> Azure AD admite "usuarios invitados", que son cuentas de usuario de un inquilino de Azure AD que se crearon con una cuenta Microsoft o una identidad de Azure AD de otro inquilino. El Portal de administración de Office 365 no admite actualmente estos usuarios. Los usuarios invitados con cuentas Microsoft no podrán acceder al Portal de administración de Office 365, mientras que los usuarios invitados de otros inquilinos de Azure AD se ignorarán. En el segundo caso, la cuenta local del usuario, del inquilino de Azure AD u Office 365 donde se creó originalmente el usuario, será la única accesible.

### <a name="select-one-or-more-license-trials"></a>Seleccione una o más versiones de prueba de licencia

Puede activar una suscripción de prueba de Azure AD Premium o Enterprise Mobility + Security en **Azure Active Directory** &gt; **Inicio rápido**.

![Selección de licencia de prueba](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

Las licencias de prueba están disponibles en la hoja **Licencias**.

### <a name="assign-licenses-to-users-and-groups"></a>Asignación de licencias a usuarios y grupos

Una vez que la suscripción está activa, debe asignarse a sí mismo una licencia. A continuación, actualice el explorador para asegurarse de que está viendo todas las características. El siguiente paso es asignar licencias a los usuarios que necesitarán acceso a las características de pago de Azure AD. Como se describe en [Asignación de licencias](#assign-licenses), una manera fácil de asignar licencias es identificar el grupo que representa el público deseado y asignarles la licencia. De esta manera, a los usuarios que se agregan o quitan del grupo durante su ciclo de vida, se les asigna una licencia o se les cancela la asignación de la licencia, respectivamente.

> [!NOTE]
> Algunos servicios de Microsoft no están disponibles en todas las ubicaciones. Para poder asignar una licencia a un usuario, el administrador tiene que especificar la propiedad **Ubicación de uso** para el usuario. Esta propiedad se puede establecer en **Usuario** &gt; **Perfil** &gt; **Configuración** en Azure Portal. Cuando se usa la asignación de licencias de grupo, los usuarios cuya ubicación de uso no se especifique heredan la ubicación del directorio.

Para asignar una licencia, en **Azure Active Directory** &gt; **Licencias** &gt; **Todos los productos**, seleccione uno o varios productos y luego seleccione **Asignar** en la barra de comandos.

![Selección de una licencia para asignar](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

Puede usar la hoja **Usuarios y grupos** para elegir varios usuarios o grupos o para deshabilitar planes de servicio en el producto. Use el cuadro de búsqueda de la parte superior para buscar nombres de usuarios y grupos.

![Selección de un usuario o grupo para la asignación de licencias](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Al asignar una licencia a un grupo, puede pasar algún tiempo antes de que todos los usuarios hereden la licencia, en función del número de usuarios en el grupo. El estado de procesamiento se puede comprobar en la hoja **Grupo**, en el icono de **Licencias**.

![Estado de asignación de licencias](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Se pueden producir errores de asignación durante la asignación de licencias de Azure AD, pero son relativamente poco frecuentes al administrar productos de Azure AD y Enterprise Mobility + Security. Los posibles errores de asignación se limitan a los siguientes:
- Conflicto de asignación: cuando se asignó previamente a un usuario una licencia incompatible con la actual. En este caso, para asignar la nueva licencia será necesario quitar la anterior.
- Número excesivo de licencias disponibles: cuando el número de usuarios en los grupos asignados supera las licencias disponibles, el estado de la asignación de los usuarios reflejará un error de asignación debido a que faltan licencias.

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Concesión de licencias de colaboración B2B de Azure AD

La colaboración B2B permite invitar a usuarios invitados a su inquilino de Azure AD para proporcionarles acceso a los servicios de Azure AD y a los recursos de Azure que estén disponibles.  

No se aplica ningún cargo por invitar a los usuarios de B2B ni por asignarlos a una aplicación en Azure AD. Hasta 10 aplicaciones por usuario invitado y 3 informes básicos también son gratuitos para los usuarios de colaboración B2B. Si un usuario invitado tiene las licencias pertinentes asignadas en el inquilino de Azure AD del asociado, también las tendrá en el suyo.

Aunque no es necesario, si quiere proporcionar acceso a características de pago de Azure AD, a esos usuarios invitados de B2B se les deben proporcionar las licencias de Azure AD adecuadas. Un inquilino que invita con una licencia de pago de Azure AD puede asignar derechos de usuario de colaboración de B2B a cinco usuarios invitados adicionales al inquilino. Para ver escenarios e información, consulte [Guía de concesión de licencias de colaboración B2B](active-directory-b2b-licensing.md).

### <a name="view-assigned-licenses"></a>Visualización de licencias asignadas

En **Azure Active Directory** &gt; **Licencias** &gt; **Todos los productos** se muestra una vista de resumen de las licencias asignadas y disponibles.

![Visualización del resumen de licencias](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Al seleccionar un producto específico aparece una lista detallada de los usuarios y grupos asignados. En la lista **Usuarios con licencia** se muestran todos los usuarios actualmente con licencia, tanto si la licencia se asignó directamente al usuario como si se heredó de un grupo.

![Visualización de los detalles de licencia](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

De forma similar, en la lista **Grupos con licencias** se muestran todos los grupos con licencia asignada. Seleccione un usuario o grupo para abrir la hoja **Licencias**, que muestra todas las licencias asignadas a ese objeto.

### <a name="remove-a-license"></a>Eliminación de una licencia

Para quitar una licencia, vaya al usuario o grupo y abra el icono de **Licencias**. Seleccione la licencia y haga clic en **Quitar**.

![Eliminación de una licencia](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

Las licencias heredadas por el usuario de un grupo no se pueden quitar directamente. En su lugar, quite el usuario del grupo desde el que haya heredado la licencia.

### <a name="extend-trials"></a>Ampliación de pruebas

Las extensiones de prueba para los clientes están disponibles como registro de autoservicio mediante el Portal de Office 365. Un administrador de clientes puede ir al Portal de Office (el acceso depende de los permisos para el Portal de Office) y seleccionar la prueba de Azure AD Premium. Al hacer clic en el vínculo **Extend trial** (Ampliar prueba) se inicia el proceso de extensión. Se necesita una tarjeta de crédito, pero no se realizarán cargos.

![Ampliación de una prueba en Azure Portal](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre escenarios avanzados de administración de licencias mediante grupos, consulte el artículo [Asignación de licencias a un grupo](active-directory-licensing-group-assignment-azure-portal.md).

Aquí encontrará información sobre cómo configurar y usar otras características de pago de Azure AD:

* [Restablecimiento de la contraseña de autoservicio](active-directory-manage-passwords.md)
* [Administración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Compra directa de licencias de Azure AD Premium](http://aka.ms/buyaadp)

