---
title: "Aprovisionamiento de aplicaciones con filtros de ámbito | Microsoft Docs"
description: "Obtenga información sobre cómo usar los filtros de ámbito para evitar el aprovisionamiento real de los objetos de las aplicaciones que admiten el aprovisionamiento automático de usuarios, en caso de que un objeto no satisfaga los requisitos empresariales."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: c06c089fb08c19b55246122201c378917a560e14
ms.openlocfilehash: 3e4458f70afce9ebd9477b00afc39b6e84e49319
ms.contentlocale: es-es
ms.lasthandoff: 03/01/2017


---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito
El objetivo de esta sección es explicar cómo usar filtros de ámbito para definir reglas basadas en atributos que determinarán qué usuarios se aprovisionarán en la aplicación.

## <a name="clauses-and-scope-groups"></a>Cláusulas y grupos de ámbitos
![Filtro de ámbito][1] 

Los filtros de ámbito se definen mediante uno o varios **grupos de ámbitos**, y cada uno de ellos contiene una o varias **cláusulas**. Para ver las cláusulas de un grupo de ámbitos concreto, haga clic en la flecha situada a la izquierda del nombre del grupo para expandirlo.

Una **cláusula** determina qué usuarios pueden pasar a través del filtro de ámbito mediante la evaluación de los atributos de cada usuario. Por ejemplo, podría tener una cláusula que requiere que el atributo "state" del usuario sea igual a Nueva York, de modo que solo se aprovisionará a los usuarios de Nueva York en la aplicación.

![Nombre del grupo de ámbito][2] 

Cada **grupo de ámbitos** comienza con una **cláusula** obligatoria, como se muestra en la captura de pantalla anterior. Esta cláusula sólo indica que primero es necesario asignar al usuario a la aplicación antes de que los filtros de ámbito lo evalúen. Esta cláusula no puede eliminarse ni modificarse.

Puede agregar cláusulas nuevas o grupos de ámbitos nuevos; para ello, haga clic en el botón correspondiente. Puede asignar un nombre a cada grupo de ámbitos; para ello, edite la propiedad **Nombre del grupo de ámbitos** .

## <a name="how-scoping-filters-are-evaluated"></a>Evaluación de los filtros de ámbito
Durante el aprovisionamiento, probamos a cada usuario asignado con los filtros de ámbito para determinar si es posible dar acceso a un usuario a la aplicación. Puede considerar cada cláusula como una prueba que se debe superar para evitar que el filtro rechace a un usuario. 

Si ha definido varios grupos de ámbitos, cada usuario debe pasar al menos uno de ellos para poder tener acceso a la aplicación. Dentro de cada grupo de ámbitos, sin embargo, el usuario debe pasar cada cláusula para pasar ese grupo de ámbitos específico. 

En otras palabras, puede considerar los grupos de ámbitos como agrupaciones mediante el operador OR, y puede considerar las cláusulas contenidas en ellos como agrupaciones mediante el operador AND. Por ejemplo, considere el siguiente filtro de ámbito:

![Nombre del grupo de ámbito][3]  

Según este filtro de ámbito, los usuarios deben cumplir los siguientes criterios, a fin de que se les pueda aprovisionar:

1. Se les debe haber asignado a la aplicación.
2. Deben trabajar en el departamento de ingeniería.
3. Deben trabajar en San Francisco o Canadá.

## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Aprovisionamiento y desaprovisionamiento automático de usuarios para aplicaciones SaaS](active-directory-saas-app-provisioning.md)
* [Personalización de asignaciones de atributos para el aprovisionamiento de usuarios](active-directory-saas-customizing-attribute-mappings.md)
* [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Notificaciones de aprovisionamiento de cuentas](active-directory-saas-account-provisioning-notifications.md)
* [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./media/active-directory-saas-scoping-filters/ic782813.png

