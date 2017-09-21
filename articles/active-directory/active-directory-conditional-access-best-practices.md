---
title: Procedimientos recomendados para el acceso condicional en Azure Active Directory | Microsoft Docs
description: "Obtenga información acerca de las cosas que debe saber y lo que se debe evitar hacer al configurar directivas de acceso condicional."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: fedc72f8fe1ada9a991d417cc77b8ca659589f55
ms.contentlocale: es-es
ms.lasthandoff: 09/08/2017

---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Procedimientos recomendados para el acceso condicional en Azure Active Directory

En este tema se proporciona información acerca de las cosas que debe saber y lo que se debe evitar hacer al configurar directivas de acceso condicional. Antes de leer este tema, debe familiarizarse con los conceptos y la terminología que se describe en [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)

## <a name="what-you-should-know"></a>Qué debería saber

### <a name="whats-required-to-make-a-policy-work"></a>¿Cuáles son los requisitos para realizar un trabajo de directiva?

Cuando crea una nueva directiva, no hay usuarios, grupos, aplicaciones o controles de acceso seleccionados.

![Aplicaciones en la nube](./media/active-directory-conditional-access-best-practices/02.png)


Para realizar un trabajo de directiva, debe configurar lo siguiente:


|Qué           | Cómo                                  | Porqué|
|:--            | :--                                  | :-- |
|**Aplicaciones en la nube** |Tiene que seleccionar una o más aplicaciones.  | El objetivo de la directiva de acceso condicional es permitirle que realice un mejor ajuste sobre cómo los usuarios autorizados pueden acceder a sus aplicaciones.|
| **Usuarios y grupos** | Tiene que seleccionar al menos un usuario o grupo que esté autorizado para acceder a las aplicaciones en la nube que haya seleccionado. | Una directiva de acceso condicional que no tiene usuarios ni grupos asignados nunca se desencadena. |
| **Controles de acceso** | Tiene que seleccionar al menos un control de acceso. | El procesador de directivas tiene que saber qué hacer si se satisfacen las condiciones.|


Además de estos requisitos básicos, en muchos casos, también debe configurar una condición. Cuando una directiva también funcione sin una condición configurada, las condiciones son el factor de conducción para el acceso de mejor ajuste a sus aplicaciones.


![Aplicaciones en la nube](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>¿Cómo se evalúan las asignaciones?

A todas las asignaciones se les asigna **la operación lógica AND**. Si tiene más de una asignación configurada, se deben satisfacer todas las asignaciones para desencadenar una directiva.  

Si debe configurar una condición de ubicación que se aplica a todas las conexiones realizadas desde fuera de la red de la organización, puede:

- Incluir **todas las ubicaciones**.
- Excluir **todas las direcciones IP de confianza**.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>¿Qué ocurre si tiene directivas configuradas en el Portal de Azure clásico y en Azure Portal?  
Las dos directivas se aplican mediante Azure Active Directory y el usuario obtiene acceso únicamente cuando se cumplen todos los requisitos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>¿Qué sucede si tiene directivas en el portal de Intune Silverlight y en Azure Portal?
Las dos directivas se aplican mediante Azure Active Directory y el usuario obtiene acceso únicamente cuando se cumplen todos los requisitos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>¿Qué ocurre si tiene varias directivas configuradas para el mismo usuario?  
En cada inicio de sesión, Azure Active Directory evalúa todas las directivas y garantiza que se cumplan todos los requisitos antes de conceder acceso al usuario.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>¿Funciona el acceso condicional con Exchange ActiveSync?

Sí, se puede usar Exchange ActiveSync en una directiva de acceso condicional.


## <a name="what-you-should-avoid-doing"></a>¿Qué no debería hacer?

El marco de trabajo de acceso condicional le proporciona una flexibilidad de configuración excelente. Sin embargo, una gran flexibilidad también implica revisar cuidadosamente cada directiva de configuración antes de liberarla para evitar resultados no deseados. En este contexto, debe prestar especial atención a las asignaciones que afectan a conjuntos completos, como **todos los usuarios / grupos / aplicaciones en la nube**.

En su entorno, debería evitar las siguientes configuraciones:


**Para todos los usuarios, todas las aplicaciones en la nube:**

- **Bloquear acceso**: esta configuración bloquea toda la organización, lo cual no es en absoluto una buena idea.

- **Requerir dispositivo compatible**: para usuarios que aún no han inscrito sus dispositivos, esta directiva bloquea todo el acceso, incluido el acceso al portal Intune. Si es un administrador y no tiene un dispositivo inscrito, esta directiva le impide volver a acceder Azure Portal para cambiar la directiva.

- **Requerir unión a un dominio** : este acceso al bloqueo de directivas también ofrece la posibilidad de bloquear el acceso a todos los usuarios de su organización si aún no tiene un dispositivo unido al dominio.


**Para todos los usuarios, todas las aplicaciones en la nube, todas las plataformas de dispositivos:**

- **Bloquear acceso**: esta configuración bloquea toda la organización, lo cual no es en absoluto una buena idea.



## <a name="policy-migration"></a>Migración de directivas

Si tiene directivas configuradas en el Portal de Azure clásico, debe migrarlas a Azure Portal ya que:


- Un usuario que se encuentra en una directiva del Portal de Azure clásico y en una directiva de Azure Portal debe cumplir los requisitos de las dos directivas 

- Si no se migran las directivas existentes, no podrá implementar las directivas que conceden acceso


### <a name="migration-from-the-azure-classic-portal"></a>Migración desde el Portal de Azure clásico

En este escenario: 

- En su [Portal de Azure clásico](https://manage.windowsazure.com), ha configurado:

    - SharePoint Online

    ![Acceso condicional](./media/active-directory-conditional-access-best-practices/14.png)

    - Una directiva de acceso condicional basada en dispositivos

    ![Acceso condicional](./media/active-directory-conditional-access-best-practices/15.png)

- Quiere configurar una directiva de acceso condicional de administración de aplicaciones móviles en Azure Portal 
 

#### <a name="configuration"></a>Configuración 

- Revise sus directivas de acceso condicional basadas en dispositivos

- Mígrelas a Azure Portal 

- Agregue las directivas de acceso condicional con administración de aplicaciones móviles


### <a name="migrating-from-intune"></a>Migración desde Intune 

En este escenario:

- En [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), tiene una directiva de acceso condicional con administración de aplicaciones móviles para cualquier Exchange Online o SharePoint Online configurados

    ![Acceso condicional](./media/active-directory-conditional-access-best-practices/15.png)

- Quiere realizar la migración para poder usar una directiva de acceso condicional con administración de aplicaciones móviles en Azure Portal


#### <a name="configuration"></a>Configuración 
 
- Revise sus directivas de acceso condicional basadas en dispositivos

- Mígrelas a Azure Portal 

- Revise sus directivas de acceso condicional con administración de aplicaciones móviles configuradas para cualquier Exchange Online o SharePoint Online en Intune

- Agregue el control para **requerir aplicaciones aprobadas** además del control basado en dispositivos 
 

### <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Migración desde el Portal de Azure clásico e Intune

En este escenario:

- Tiene la siguiente configuración:

    - **Portal de Azure clásico:** condicional basado en dispositivos 

    - **Intune:** directivas de acceso condicional con administración de aplicaciones móviles 
    
- Quiere realizar la migración de ambas directivas para poder usar directivas de acceso condicional con administración de aplicaciones móviles en Azure Portal


#### <a name="configuration"></a>Configuración

- Revise sus directivas de acceso condicional basadas en dispositivos

- Mígrelas a Azure Portal 

- Revise la directiva de acceso condicional con administración de aplicaciones móviles configurada para cualquier Exchange Online o SharePoint Online en Intune

- Agregue el control para **requerir aplicaciones aprobadas** además del basado en dispositivos 












## <a name="common-scenarios"></a>Escenarios comunes

### <a name="requiring-multi-factor-authentication-for-apps"></a>Exigir autenticación multifactor para las aplicaciones

Muchos entornos tienen aplicaciones que requieren un mayor nivel de protección que otras.
Este es, por ejemplo, el caso de aplicaciones que tienen acceso a datos confidenciales.
Si quiere agregar otra capa de protección a estas aplicaciones, puede configurar una directiva de acceso condicional que exija autenticación multifactor cuando los usuarios accedan a estas aplicaciones.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Exigir autenticación multifactor para el acceso desde redes que no son de confianza

Este escenario es similar al escenario anterior porque agrega un requisito para la autenticación multifactor.
Sin embargo, la diferencia principal es la condición de este requisito.  
Mientras que el escenario anterior se centra en aplicaciones con acceso a datos confidenciales, este escenario se centra en ubicaciones de confianza.  
En otras palabras, podría tener un requisito de autenticación multifactor si un usuario accede a una aplicación desde una red que no es de confianza.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Solo los dispositivos de confianza pueden acceder a servicios de Office 365

Si usa Intune en su entorno, puede comenzar a usar inmediatamente la directiva de acceso condicional en la consola de Azure.

Muchos clientes de Intune usan el acceso condicional para asegurarse de que solo los dispositivos de confianza puedan acceder a servicios de Office 365. Esto significa que los dispositivos móviles se inscriben con Intune y satisfacen los requisitos de la directiva de cumplimiento y que los equipos con Windows están unidos a un dominio local. Una importante mejora es que no es necesario establecer la misma directiva para cada uno de los servicios de Office 365.  Cuando cree una nueva directiva, configure las aplicaciones de nube para que incluyan cada una de las aplicaciones de Office 365 que quiere proteger con acceso condicional.

## <a name="next-steps"></a>Pasos siguientes

Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

