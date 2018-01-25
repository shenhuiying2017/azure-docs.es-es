---
title: "Personalización de asignaciones de atributos de Azure AD | Microsoft Docs"
description: "Conozca cuáles son las asignaciones de atributos para aplicaciones SaaS en Azure Active Directory y cómo puede modificarlas para satisfacer sus necesidades empresariales."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ed3558fd4ca0022389091edad9caa7686a5116a0
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalización de asignaciones de atributos de aprovisionamiento de usuarios para aplicaciones SaaS en Azure Active Directory de usuarios
Microsoft Azure AD proporciona soporte para el aprovisionamiento de usuarios para aplicaciones SaaS de terceros como Salesforce, Google Apps y otras. Si dispone de aprovisionamiento de usuarios para una aplicación SaaS de terceros habilitada, el Portal de administración de Azure controla sus valores de atributo en forma de una configuración denominada "asignación de atributos".

Hay un conjunto preconfigurado de asignaciones de atributos entre los objetos de usuario de Azure AD y los objetos de usuario de cada aplicación SaaS. Algunas aplicaciones administran otros tipos de objetos, como grupos o contactos. <br> 
 Puede personalizar las asignaciones de atributos predeterminadas según sus necesidades empresariales. Esto significa que puede cambiar o eliminar asignaciones de atributos existentes o crear nuevas asignaciones de atributos.

En el portal de Azure AD, se puede acceder a esta característica haciendo clic en una configuración de **Asignaciones** en **Aprovisionamiento** en la sección **Administrar** de una **aplicación empresarial**.


![Salesforce][5] 

Al hacer clic en una configuración de **Asignaciones**, se abre la hoja **Asignación de atributos**.  
Hay asignaciones de atributos que una aplicación SaaS necesita para funcionar correctamente. Para los atributos necesarios, la característica **Eliminar** no está disponible.


![Salesforce][6]  

En el ejemplo anterior, puede ver que el atributo **Username** de un objeto administrado en Salesforce se rellena con el valor de **userPrincipalName** del objeto vinculado de Azure Active Directory.

Puede personalizar las **asignaciones de atributos** existentes haciendo clic en una asignación. Se abre la hoja **Editar atributo**.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>Información sobre los tipos de asignación de atributos
Con asignaciones de atributos, puede controlar cómo se rellenan los atributos en una aplicación SaaS de terceros. Se admiten cuatro tipos de asignaciones diferentes:

* **Directa** : el atributo de destino se rellena con el valor de un atributo del objeto vinculado en Azure AD.
* **Constante** : el atributo de destino se rellena con una cadena específica que se ha especificado.
* **Expresión** : el atributo de destino se rellena según el resultado de una expresión similar a un script. 
  Para más información, consulte [Escritura de expresiones para la asignación de atributos en Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Ninguno** : el atributo de destino se deja sin modificar. Sin embargo, si el atributo de destino está vacío, se rellena con el valor predeterminado que especifique.

Además de estos cuatro tipos básicos de asignaciones de atributos, las asignaciones de atributos personalizadas admiten el concepto de una asignación de valor **predeterminada** opcional. La asignación de valor predeterminada garantiza que un atributo de destino se rellene con un valor si no hay ningún valor en Azure AD ni en el objeto de destino. La configuración más habitual consiste en dejarlo en blanco.


## <a name="understanding-attribute-mapping-properties"></a>Información sobre las propiedades de asignación de atributos

En la sección anterior, ya ha introducido la propiedad de tipo de asignación de atributos.
Además de esta propiedad, las asignaciones de atributos también admiten los siguientes atributos:

- **Atributo de origen**: especifica el atributo de usuario del sistema de origen (p. ej.: Azure Active Directory).
- **Atributo de destino**: especifica el atributo de usuario en el sistema de destino (p. ej.: ServiceNow).
- **Hacer coincidir objetos con este atributo**: especifica si se debe usar o no esta asignación para identificar de forma unívoca a los usuarios entre los sistemas de origen y de destino. Normalmente esto se establece en el atributo userPrincipalName o mail en Azure AD, que se suele asignar a un campo de nombre de usuario en una aplicación de destino.
- **Precedencia de coincidencia**: se pueden establecer varios atributos coincidentes. Si hay varios, se evalúan en el orden definido por este campo. En el momento en que se encuentre una coincidencia, no se evaluarán más atributos coincidentes.
- **Aplicar esta asignación**
    - **Siempre**: esta asignación se aplica a las acciones de creación y actualización de usuarios
    - **Solo durante la creación**: esta asignación se aplica solo a las acciones de creación de usuarios


## <a name="what-you-should-know"></a>Qué debería saber

Microsoft Azure AD proporciona una implementación eficaz de un proceso de sincronización. En un entorno inicializado, sólo los objetos que requieren actualizaciones se procesan durante un ciclo de sincronización. Actualizar las asignaciones de atributos repercute en el rendimiento de un ciclo de sincronización. Una actualización de la configuración de la asignación de atributos requiere que se vuelvan a evaluar todos los objetos administrados. Es un procedimiento recomendado mantener el número mínimo de cambios consecutivos de las asignaciones de atributos.

## <a name="next-steps"></a>pasos siguientes

* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de ámbito para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
* [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md)
* [Notificaciones de aprovisionamiento de cuentas](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

