---
title: "Administración del acceso de los invitados con las revisiones de acceso de Azure AD | Microsoft Docs"
description: "Administración de los usuarios invitados como miembros de un grupo o asignados a una aplicación con las revisiones de acceso de Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.contentlocale: es-es
ms.lasthandoff: 09/20/2017

---

# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Administración del acceso de los invitados con las revisiones de acceso de Azure AD


Con Azure Active Directory, puede habilitar fácilmente la colaboración entre distintas organizaciones mediante la [característica B2B de Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md). Los usuarios invitados de otros inquilinos pueden ser [invitados por los administradores](active-directory-b2b-admin-add-users.md) o por [los usuarios finales](active-directory-b2b-how-it-works.md).  Esto también se aplica a las identidades sociales como las cuentas Microsoft.

También puede asegurarse fácilmente de que los usuarios invitados tengan el acceso adecuado.  Para ello, puede pedir a los propios invitados o a quien decida en su lugar que participen en una revisión de acceso y vuelvan a certificar (o atestigüen) el acceso de invitado. Los revisores pueden dar su aprobación para cada necesidad de acceso continuado de los usuarios, en función de las sugerencias de Azure AD. Cuando se completa una revisión de acceso, es posible hacer cambios y retirar el acceso a los invitados que ya no lo necesitan.

> [!NOTE]
> Este documento se centra en revisar el acceso de los usuarios invitados. Si desea revisar el acceso de todos los usuarios, no solo de los invitados, en su lugar lea la guía para [administrar el acceso de los usuarios con revisiones de acceso](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md).  Si desea revisar la pertenencia del usuario a roles administrativos tales como administrador global, consulte [Cómo iniciar una revisión de acceso en Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Requisitos previos 

Las revisiones de acceso están disponibles con la edición Premium P2 de Azure Active Directory, incluida en EMS E5. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).  Cada usuario que interactúa con esta característica, para crear una revisión, revisar el acceso o aplicar una revisión, requiere una licencia.  

Si va a pedir a los usuarios invitados que revisen su propio acceso, lea más acerca de las licencias de usuarios invitados en [Licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md).

## <a name="creating-and-performing-an-access-review-for-guests"></a>Creación y realización de una revisión de acceso para los invitados

En primer lugar, habilite las revisiones de acceso para que aparezcan en los paneles de acceso del revisor.  Como administrador global, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD ofrece varios escenarios para revisar el acceso de los usuarios invitados.  

Seleccione uno de los siguientes:
 - Un grupo en Azure Active Directory que tiene uno o varios invitados como miembros. 
 - Una aplicación conectada a Azure Active Directory que tenga uno o varios usuarios invitados asignados, y decidirá entonces si hacer que cada invitado revise su propio acceso o uno o varios usuarios que revisan el acceso de cada invitado.

 Cada uno de estos escenarios se trata en una de las secciones siguientes.

* [Se pide a los invitados que revisen su propia pertenencia a un grupo](#asking-guests-to-review-their-own-membership-in-a-group)
* [Se pide a los patrocinadores que revisen la pertenencia de un invitado a un grupo](#asking-sponsors-to-review-guests-membership-in-a-group)
* [Se pide a los invitados que revisen su propio acceso a una aplicación](#asking-guests-to-review-their-own-access-to-an-application)
* [Se pide a los patrocinadores que revisen su propio acceso a una aplicación](#asking-sponsors-to-review-guests-access-to-an-application) 
* [Se pide a los invitados que revisen el acceso que requieren en general](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>Se pide a los invitados que revisen su propia pertenencia a un grupo

Las revisiones de acceso pueden utilizarse para asegurarse de que los usuarios que han sido invitados y se han agregado a un grupo continuarán necesitando el acceso.  Una manera fácil es pedir a los propios invitados que revisen su propia pertenencia a ese grupo.

1. Inicie una revisión de acceso para el grupo, seleccione la revisión para incluir solo a los miembros que sean usuarios invitados y que los miembros se revisen a sí mismos. Para más información, consulte [cómo crear una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).
2. Pida a cada invitado que revise su propia pertenencia.  De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso.  Azure AD proporciona instrucciones para los invitados en el artículo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).
3. Cuando los revisores hayan proporcionado sus datos de entrada, detenga la revisión de acceso y aplique los cambios. Para más información, consulte cómo [realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md). 
4. Además de los usuarios que negaron su necesidad de seguir teniendo acceso, puede que desee quitar también los usuarios que no respondieran.  Esto se debe a que los usuarios que no responden es probable que ya no reciban correo electrónico.
5. Si el grupo no se usa para la administración de acceso, puede ser aconsejable quitar también los usuarios que no fueran seleccionados para participar en la revisión porque no aceptaran su invitación.  Esto puede indicar que la dirección de correo electrónico del usuario invitado era errónea.  Sin embargo, si se utiliza un grupo como una lista de distribución, algunos usuarios invitados pueden no haber sido seleccionados para participar dado que son objetos de contacto.

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>Se pide a los patrocinadores que revisen la pertenencia de un invitado a un grupo

Puede solicitar a un patrocinador, por ejemplo a los propietarios de un grupo, que revise la necesidad de un invitado de seguir perteneciendo a un grupo.

1. Inicie una revisión de acceso para el grupo, seleccionando la revisión para incluir solo a los miembros que sean usuarios invitados y especificando uno o varios revisores. Para más información, consulte [cómo crear una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).
2. Pida a los revisores que proporcionen sus datos de entrada. De forma predeterminada, cada uno recibirá un correo electrónico de Azure AD con un vínculo al panel de acceso, en el que podrán [realizar la revisión de acceso](active-directory-azure-ad-controls-perform-access-review.md).
3. Cuando los revisores hayan proporcionado sus datos de entrada, detenga la revisión de acceso y aplique los cambios. Para más información, consulte cómo [realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>Se pide a los invitados que revisen su propio acceso a una aplicación

Las revisiones de acceso pueden utilizarse para asegurarse de que los usuarios que han sido invitados a una aplicación concreta siguen necesitando el acceso.  Una manera fácil es pedir a los propios invitados que revisen su propia necesidad de acceso.

1. Inicie una revisión de acceso para la aplicación, seleccionando la revisión para incluir solo a los invitados y que los usuarios revisen su propio acceso. Para más información, consulte [cómo crear una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).
2. Se pide a cada invitado que revise su propio acceso a la aplicación.  De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso del panel de acceso de la organización.  Azure AD proporciona instrucciones para los invitados en el artículo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).
3. Cuando los revisores hayan proporcionado sus datos de entrada, detenga la revisión de acceso y aplique los cambios. Para más información, consulte cómo [realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).
4. Además de los usuarios que negaron su necesidad de seguir teniendo acceso, puede que desee quitar también los usuarios invitados que no hayan respondido, ya que es posible que estos ya no estén recibiendo el correo electrónico.  Puede que también desee quitar los usuarios invitados que no fueran seleccionados para participar, especialmente si el invitado no ha sido invitado recientemente, ya que esos usuarios no habían aceptado su invitación y no tendrían acceso a la aplicación. 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>Se pide a los patrocinadores que revisen su propio acceso a una aplicación


Puede solicitar a un patrocinador, por ejemplo a los propietarios de una aplicación, que revise la necesidad de un invitado de seguir teniendo acceso a la aplicación.

1. Inicie una revisión de acceso para la aplicación, seleccionando la revisión para incluir solo a los invitados y especificando uno o varios usuarios como revisores. Para más información, consulte [cómo crear una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).
2. Pida a los revisores que proporcionen sus datos de entrada. De forma predeterminada, cada uno recibirá un correo electrónico de Azure AD con un vínculo al panel de acceso, en el que podrán [realizar la revisión de acceso](active-directory-azure-ad-controls-perform-access-review.md).
3. Cuando los revisores hayan proporcionado sus datos de entrada, detenga la revisión de acceso y aplique los cambios. Para más información, consulte cómo [realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>Se pide a los invitados que revisen el acceso que requieren en general

En algunas organizaciones, los invitados pueden no ser conscientes de a qué grupos pertenecen.

> [!NOTE]
> Las versiones anteriores de Azure Portal no permitían el acceso administrativo a los usuarios cuyo valor de UserType fuera Guest y, en algunos casos, un administrador del directorio puede haber cambiado ese valor por Member mediante PowerShell.  Si esto ha sucedido antes en el directorio, la consulta que se ha mencionado puede no incluir todos los usuarios invitados que históricamente han tenido derechos de acceso administrativo, por lo que deberá cambiar el valor de UserType de dichos invitados o incluirlos manualmente entre los miembros del grupo.

1. Cree un grupo de seguridad en Azure AD con los invitados como miembros, si aún no existe un grupo adecuado.  Por ejemplo, puede ser conveniente crear un grupo con la pertenencia mantenida de forma manual para los invitados.  O bien, puede que desee crear un grupo dinámico con un nombre como "Invitados de Contoso" para los usuarios del inquilino Contoso que tengan el valor Guest en el atributo UserType.
2. Inicie una revisión de acceso para ese grupo, seleccionando que los revisores sean los propios miembros. Para más información, consulte [cómo crear una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md).
3. Pida a cada invitado que revise su propia pertenencia.  De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso del panel de acceso de la organización.  Azure AD proporciona instrucciones para los invitados en el artículo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md).
4. Cuando los revisores hayan proporcionado su información, detenga la revisión de acceso. Para más información, consulte cómo [realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md).
5. Quite el acceso de invitado para los invitados cuyo acceso se denegara, no completaran la revisión o no hubieran aceptado su invitación previamente.   Si algunos de los invitados son contactos que no fueron seleccionados para participar en la revisión porque previamente no habían aceptado una invitación, puede ser conveniente deshabilitar su cuenta de inicio de sesión, mediante Azure Portal o PowerShell.  Si el invitado ya no necesita el acceso y no es un contacto, se puede quitar su objeto de usuario desde su directorio, mediante Azure Portal o PowerShell.

## <a name="next-steps"></a>Pasos siguientes

- [Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)








