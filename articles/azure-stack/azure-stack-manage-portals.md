---
title: "Uso del portal de administración de Azure Stack | Microsoft Docs"
description: "Como operador de Azure Stack, debe aprender a usar el portal de administración."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 34d9d207225327758d535a11c870019f3db31cb0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Uso del portal de administración de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Hay dos portales en Azure Stack: el portal de administración y el portal de usuarios (también llamado en ocasiones portal de *inquilinos*). Como operador de Azure Stack, puede usar el portal de administración para la administración y las operaciones diarias de Azure Stack. 

## <a name="access-the-administrator-portal"></a>Acceso al portal de administración

En un entorno de kit de desarrollo, primero deberá asegurarse de que puede [conectarse al host del kit de desarrollo](azure-stack-connect-azure-stack.md) mediante Conexión a Escritorio remoto o una red privada virtual (VPN).

Para acceder al portal de administración, vaya a la dirección URL del portal e inicie sesión con las credenciales de un operador de Azure Stack. En un sistema integrado, la dirección URL del portal varía según el nombre de la región y el nombre de dominio completo (FQDN) externo de la implementación de Azure Stack.

| Environment | Dirección URL del portal de administración |   
| -- | -- | 
| Kit de desarrollo| https://adminportal.local.azurestack.external  |
| Sistemas integrados | https://adminportal.&lt;*región*&gt;.&lt;*FQDN*&gt; | 
| | |

 ![El portal de administración](media/azure-stack-manage-portals/image1.png)

En el portal de administración, puede hacer cosas como las siguientes:

* Administrar la infraestructura (como el estado del sistema, las actualizaciones, la capacidad, etc.)
* Rellenar el Marketplace.
* Crear planes y ofertas.
* Crear suscripciones para los usuarios.

En el icono del **tutorial de inicio rápido**, se incluyen vínculos a documentación en línea para las tareas más comunes.
 
Aunque existe la posibilidad de que un operador cree recursos, como máquinas virtuales, redes virtuales y cuentas de almacenamiento en el portal de administración, debe [iniciar sesión en el portal de usuarios](user/azure-stack-use-portal.md) para crear y probar los recursos. (El vínculo **Crear una máquina virtual** del icono del tutorial de inicio rápido le permite crear una máquina virtual en el portal de administración; sin embargo, este procedimiento es solo para validar Azure Stack después de la implementación inicial).

## <a name="subscription-behavior"></a>Comportamiento de la suscripción
 
Hay solo una suscripción que está disponible en el portal de administración. Esta suscripción es la *suscripción de proveedor predeterminada*. No se puede agregar ninguna otra suscripción para usarla en el portal de administración.

Como operador de Azure Stack, puede agregar suscripciones para los usuarios (incluido usted mismo) desde el portal de administración. Los usuarios (incluido usted) pueden tener acceso a estas suscripciones y usarlas desde el portal de usuarios. El portal de usuarios no proporciona acceso a cualquiera de las funcionalidades administrativas u operativas del portal de administración.

Los portales de administración y de usuarios se apoyan en instancias independientes de Azure Resource Manager. Debido a la separación de Resource Manager, las suscripciones no sirven en todos los portales. Por ejemplo, si, como operador de Azure Stack, inicia sesión en el portal de usuarios, no puede tener acceso a la suscripción de proveedor predeterminada. Por lo tanto, no tiene acceso a las funciones administrativas. Puede crear suscripciones para sí mismo a partir de ofertas públicas, pero se le considerará un usuario inquilino.

  >[!NOTE]
  En el entorno del kit de desarrollo, si un usuario pertenece al mismo directorio de inquilino que el operador de Azure Stack, no se le impide iniciar sesión en el portal de administración. Sin embargo, no tiene acceso a ninguna de las funciones administrativas. Además, desde el portal de administrador, no puede agregar suscripciones ni acceder a las ofertas disponibles en el portal de usuarios.

## <a name="administrator-portal-tips"></a>Sugerencias del portal de administración

### <a name="customize-the-dashboard"></a>Personalización del panel

El panel contiene un conjunto de iconos predeterminados. Puede hacer clic en **Editar panel** para modificar el panel predeterminado o en **Nuevo panel** para agregar paneles personalizados. Puede agregar fácilmente iconos al panel. Por ejemplo, puede hacer clic en **Nuevo**, hacer clic con el botón derecho en **Ofertas y planes** y luego hacer clic en **Anclar al panel**.

### <a name="quick-access-to-online-documentation"></a>Acceso rápido a la documentación en línea

Para acceder a la documentación del operador de Azure Stack, haga clic en el icono de Ayuda y soporte técnico (signo de interrogación) de la esquina superior derecha del portal de administración y luego haga clic en **Ayuda y soporte técnico**.

### <a name="quick-access-to-help-and-support"></a>Acceso rápido a ayuda y soporte técnico

Si hace clic el icono de ayuda y soporte técnico (signo de interrogación) en la esquina superior derecha del portal de administración y, a continuación, hace clic en **Nueva solicitud de soporte técnico**, sucede lo siguiente:

- Si usa un sistema integrado, esta acción abre un sitio donde puede abrir directamente un vale de soporte técnico para los servicios de soporte al cliente de Microsoft (CSS). Consulte la sección sobre dónde obtener soporte técnico de [Conceptos básicos de administración de Azure Stack](azure-stack-manage-basics.md) para comprender cuándo debe acudir al soporte técnico de Microsoft o al soporte técnico del proveedor de hardware OEM (fabricante de equipo original).
- Si usa el kit de desarrollo, esta acción abre directamente el sitio de foros de Azure Stack. Estos foros se supervisan periódicamente. Dado que el kit de desarrollo es un entorno de evaluación, el servicio de asistencia al cliente (CSS) de Microsoft no ofrece soporte técnico oficial.

## <a name="next-steps"></a>Pasos siguientes

- [Administración de regiones en Azure Stack](azure-stack-region-management.md)
