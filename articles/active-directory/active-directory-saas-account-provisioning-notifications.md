---
title: Notificaciones de aprovisionamiento de cuentas | Microsoft Docs
description: "Obtenga información sobre cómo asegurarse de que se le notifica si surge cualquier problema relacionado con el aprovisionamiento de usuarios que requiere su atención habilitando las notificaciones de aprovisionamiento de cuentas."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 16ec2b320f733d8828b0046c20f7f7b0e341daf9
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="account-provisioning-notifications"></a>Notificaciones de aprovisionamiento de cuentas
Gracias al aprovisionamiento de usuarios, puede automatizar el proceso de administración de usuarios en aplicaciones SaaS de terceros. <br>
Aunque este proceso es automático, necesitará interactuar con él de forma esporádica. <br>
Por ejemplo, deberá utilizarlo si resulta que la contraseña de la cuenta que ha configurado para intercambiar datos con una aplicación SaaS de terceros ha expirado. 

Al habilitar las notificaciones de aprovisionamiento de cuentas, podrá asegurarse de que se le notifica si surge cualquier problema relacionado con el aprovisionamiento de usuarios que requiere su atención.

Puede activar o desactivar las notificaciones de aprovisionamiento de cuentas como  parte de la configuración de aprovisionamiento de usuarios de aplicaciones SaaS de terceros.

![Aprovisionamiento de usuarios][1] 

Para activar las notificaciones de aprovisionamiento de cuentas, active la casilla correspondiente en la página de diálogo **Confirmación** y escriba el alias del correo electrónico del destinatario.

![Notificaciones de aprovisionamiento de cuentas][2]

Puede introducir una lista de distribución a modo de destinatario, pero tenga en cuenta que el correo electrónico de notificación contiene vínculos a informes a los que solo pueden acceder los administradores de Azure AD.

Si tiene las notificaciones de aprovisionamiento de cuentas habilitadas, recibirá correos electrónicos de todos los problemas críticos que estén relacionados con el aprovisionamiento de usuarios. De todos modos, y para evitar una sobrecarga de correos electrónicos, solo recibirá un correo electrónico de notificación al día para cada aplicación SaaS para la que esté habilitado dicho correo.

## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Personalización de asignaciones de atributos para el aprovisionamiento de usuarios](active-directory-saas-customizing-attribute-mappings.md)
* [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de ámbito para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
* [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png
