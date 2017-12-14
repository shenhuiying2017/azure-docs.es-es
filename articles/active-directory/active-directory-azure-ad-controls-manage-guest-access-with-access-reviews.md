---
title: "Administración del acceso de los invitados con las revisiones de acceso de Azure AD | Microsoft Docs"
description: "Administración de los usuarios invitados como miembros de un grupo o asignados a una aplicación con las revisiones de acceso de Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 8d5cc8035d085ac9c8fc46077376836726afbb1a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Administración del acceso de los invitados con las revisiones de acceso de Azure AD


Con Azure Active Directory (Azure AD), puede habilitar fácilmente la colaboración entre distintas organizaciones mediante la [característica B2B de Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md). Los usuarios invitados de otros inquilinos pueden ser [invitados por los administradores](active-directory-b2b-admin-add-users.md) o por [otros usuarios](active-directory-b2b-how-it-works.md). Esta capacidad también se aplica a las identidades sociales como las cuentas Microsoft.

También puede asegurarse fácilmente de que los usuarios invitados tengan el acceso adecuado. Puede pedir a los invitados o a quien decida en su lugar que participen en una revisión de acceso y vuelvan a certificar (o atestiguar) el acceso de los invitados. Los revisores pueden dar su aprobación para cada necesidad de acceso continuado de los usuarios, en función de las sugerencias de Azure AD. Cuando una revisión de acceso haya terminado, es posible hacer cambios y retirar la concesión de acceso a los invitados que ya no lo necesitan.

> [!NOTE]
> Este documento se centra en revisar el acceso de los usuarios invitados. Si desea revisar el acceso de todos los usuarios, no solo los invitados, vea [Administración del acceso de usuario con revisiones de acceso](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Si desea revisar la pertenencia de los usuarios a roles administrativos tales como administrador global, consulte [Inicio de una revisión de acceso en Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Requisitos previos 

Las revisiones de acceso están disponibles con la edición Premium P2 de Azure AD, que se incluye en Microsoft Enterprise Mobility + Security, E5. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md). Cada usuario que interactúa con esta característica para crear una revisión, acceder a ella o aplicarla requiere una licencia.

Si va a solicitar a los usuarios invitados que revisen su propio acceso, lea acerca de las licencias de usuario invitado. Para obtener más información, vea [Licencia de colaboración de Azure AD B2B](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Creación y realización de una revisión de acceso para invitados

En primer lugar, habilite las revisiones de acceso para que aparezcan en los paneles de acceso del revisor. Como administrador global, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD ofrece varios escenarios para revisar el acceso de los usuarios invitados.

Seleccione uno de los siguientes:

 - Un grupo de Azure AD con uno o más invitados como miembros.
 - Una aplicación conectada a Azure AD con uno o más usuarios invitados asignados a ella. 

A continuación, puede decidir si solicitar a cada invitado que revise su propio acceso o preguntar a uno o más usuarios que revise el acceso de cada invitado.

 Estos escenarios se tratan en las siguientes secciones.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Se pide a los invitados que revisen su propia pertenencia a un grupo

Puede usar las revisiones de acceso para garantizar que los usuarios invitados y agregados a un grupo siguen necesitando acceso. Puede solicitar fácilmente a los invitados que revisen su propia pertenencia a ese grupo.

1. Para iniciar una revisión de acceso para el grupo, seleccione la revisión para incluir solo a los miembros que sean usuarios invitados y que los miembros se revisen a sí mismos. Para más información, consulte el artículo sobre la [creación de una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).

2. Pida a cada invitado que revise su propia pertenencia. De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso. Azure AD proporciona instrucciones para los invitados en el artículo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).

3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte el artículo sobre la [realización de una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).

4. Además de los usuarios que negaron su necesidad de seguir teniendo acceso, puede también quitar a los usuarios que no respondieron. Es posible que los usuarios que no respondieron no reciban ya correos electrónicos.

5. Si el grupo no se usó para la administración de acceso, también puede quitar los usuarios que no estuvieran seleccionados para participar en la revisión porque no aceptaran su invitación. La no aceptación podría indicar que la dirección de correo electrónico del usuario invitado tiene un error de escritura. Si se utiliza un grupo como una lista de distribución, quizás algunos usuarios invitados no se seleccionaron para la participación porque son objetos de contacto.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Se pide a los patrocinadores que revisen la pertenencia de un invitado a un grupo

Puede solicitar a un patrocinador, por ejemplo al propietario de un grupo, que revise la necesidad de un invitado de seguir perteneciendo a un grupo.

1. Para iniciar una revisión de acceso para el grupo, seleccione la revisión para incluir solo a los miembros que sean usuarios invitados. Luego especifique uno o más revisores. Para más información, consulte el artículo sobre la [creación de una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).

2. Pida a los revisores que proporcionen sus datos de entrada. De forma predeterminada, cada uno recibe un correo electrónico de Azure AD con un vínculo al panel de acceso, en el que podrán [realizar la revisión de acceso](active-directory-azure-ad-controls-perform-access-review.md).

3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte el artículo sobre la [realización de una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Se pide a los invitados que revisen su propio acceso a una aplicación

Puede usar revisiones de acceso para asegurarse de que los usuarios que han sido invitados a una aplicación concreta siguen necesitando el acceso. Puede solicitarles de manera fácil que revisen su propia necesidad de acceso.

1. Para inicie una revisión de acceso para la aplicación, seleccione la revisión para incluir solo a los invitados y que los usuarios revisen su propio acceso. Para más información, consulte el artículo sobre la [creación de una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).

2. Se pide a cada invitado que revise su propio acceso a la aplicación. De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso del panel de acceso de la organización. Azure AD proporciona instrucciones para los invitados en el artículo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).

3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte el artículo sobre la [realización de una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).

4. Además de los usuarios que negaron su necesidad de seguir teniendo acceso, puede también quitar a los usuarios invitados que no respondieron. Es posible que los usuarios que no respondieron no reciban ya correos electrónicos. También puede quitar los usuarios invitados que no estaban seleccionados para participar, especialmente si no recibieron recientemente ninguna invitación. Esos usuarios no aceptaron su invitación y, por lo tanto, no disponían de acceso a la aplicación. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Se pide a los patrocinadores que revisen su propio acceso a una aplicación

Puede solicitar a un patrocinador, por ejemplo al propietario de una aplicación, que revise la necesidad de un invitado de seguir teniendo acceso a la aplicación.

1. Para iniciar una revisión de acceso para la aplicación, seleccione la revisión para incluir solo a los invitados. Luego especifique uno o más usuarios como revisores. Para más información, consulte el artículo sobre la [creación de una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).

2. Pida a los revisores que proporcionen sus datos de entrada. De forma predeterminada, cada uno recibe un correo electrónico de Azure AD con un vínculo al panel de acceso, en el que podrán [realizar la revisión de acceso](active-directory-azure-ad-controls-perform-access-review.md).

3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte el artículo sobre la [realización de una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Se pide a los invitados que revisen el acceso que requieren en general

En algunas organizaciones, los invitados pueden no ser conscientes de a qué grupos pertenecen.

> [!NOTE]
> Las versiones anteriores de Azure Portal no permitieron el acceso administrativo a los usuarios con el UserType de Guest. En algunos casos, un administrador del directorio podría haber cambiado el valor UserType a Member mediante PowerShell. Si anteriormente se produjo este cambio en el directorio, la consulta anterior podría no incluir todos los usuarios invitados que históricamente tenían derechos de acceso administrativo. En este caso, debe cambiar el UserType del invitado o incluir manualmente el invitado en la pertenencia al grupo.

1. Cree un grupo de seguridad en Azure AD con los invitados como miembros, si aún no existe un grupo adecuado. Por ejemplo, puede crear un grupo con la pertenencia mantenida de forma manual para los invitados. O bien, puede crear un grupo dinámico con un nombre como "Invitados de Contoso" para los usuarios del inquilino Contoso que tengan el valor Guest en el atributo UserType.

2. Para comenzar con la revisión de acceso para ese grupo, seleccione los revisores para que sean los propios miembros. Para más información, consulte el artículo sobre la [creación de una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).

3. Pida a cada invitado que revise su propia pertenencia. De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso del panel de acceso de la organización. Azure AD proporciona instrucciones para los invitados en el artículo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).

4. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso. Para más información, consulte el artículo sobre la [realización de una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).

5. Quite el acceso de invitado para los invitados cuyo acceso se denegara, no completaran la revisión o no hubieran aceptado su invitación previamente. Si algunos de los invitados son contactos que no fueron seleccionados para participar en la revisión porque previamente no habían aceptado una invitación, puede deshabilitar sus cuentas mediante Azure Portal o PowerShell. Si el invitado ya no necesita el acceso y no es un contacto, puede quitar su objeto de usuario desde su directorio mediante Azure Portal o PowerShell.

## <a name="next-steps"></a>Pasos siguientes

[Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)







