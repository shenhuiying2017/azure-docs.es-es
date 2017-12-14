---
title: "Componentes de la guía de prueba de concepto de Azure Active Directory | Microsoft Docs"
description: "Explorar e implementar rápidamente escenarios de administración de identidades y acceso"
services: active-directory
keywords: "azure active directory, guía, prueba de concepto, PoC"
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: ff4a8601b619837d835ec25c26b1f7e69b46ae85
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Componentes de la guía de prueba de concepto de Azure Active Directory 

## <a name="theme"></a>Tema
Azure AD proporciona soluciones de identidad y acceso en múltiples áreas de la empresa. Los escenarios se clasifican en las áreas siguientes: 

* [Gran cantidad de aplicaciones, una identidad](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [Mejorar la seguridad](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Escalable con autoservicio](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

Definir un tema para enmarcar la prueba de concepto ayuda a alinear los esfuerzos con los objetivos empresariales que, a menudo, son los primeros desencadenadores de la participación en una prueba de concepto. 

## <a name="environment"></a>Environment

Es importante determinar los detalles del entorno donde se entregará la prueba de concepto. Siempre es posible construir sobre ella una vez ha sido completada. El entorno de destino es fundamental y debe buscar el equilibrio adecuado entre ser tan real como sea posible y la sobrecarga de las restricciones o consideraciones adicionales. Los entornos típicos de las pruebas de concepto son:
* **Producción:** los escenarios se implementarán en un entorno activo y ya implementado de Microsoft Cloud Services (AD en producción, Office 365, inquilino Azure AD/solución SSO). 
* **Pruebas de aceptación de usuario (UAT) o entorno de desarrollo:** Se dispone de una infraestructura de prueba (AD en paralelo y potencialmente un inquilino Azure AD, o una solución SSO) con datos de prueba similares a los de producción. Normalmente, el entorno de prueba se comparte entre varios proyectos de la empresa.

La mayoría de los escenarios en esta guía son aditivos por naturaleza. Por ello, se pueden implementar en el inquilino de producción sin que afecte a los usuarios ajenos a la prueba de concepto. En este documento, se advertirá sobre los escenarios que pudieran tener efecto en todo el inquilino. En esos casos, podría valorarse un entorno que no sea de producción. 


## <a name="target-users"></a>Usuarios de destino

Es importante determinar el conjunto de destino de los usuarios que actúan sobre los escenarios, especialmente cuando el entorno es de producción o de prueba. Las categorías de usuarios de destino para la prueba de concepto son:
* **Usuarios piloto:** usuarios reales del entorno que van a utilizar la solución con la misma cuenta que utilizan diariamente para sus funciones de trabajo
* **Usuarios de prueba:** cuentas de prueba creadas en el entorno 

La mayoría de los escenarios de esta guía pueden realizarse con usuarios piloto. En este documento, se advertirá sobre consideraciones de los usuarios de destino si es necesario.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]