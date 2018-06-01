---
title: 'Azure AD Connect Health: diagnóstico de errores de sincronización de atributos duplicados | Microsoft Docs'
description: En este documento se describe el proceso de diagnóstico de los errores de sincronización de atributos duplicados y la posible solución de los objetos huérfanos directamente desde Azure Portal.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305662"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Diagnóstico y solución de errores de sincronización de atributos duplicados 

## <a name="overview"></a>Información general
Azure Active Directory Connect Health, además de destacar los errores de sincronización, presenta una experiencia de corrección de autoservicio para solucionar problemas de errores de sincronización de atributos duplicados y corregir los objetos huérfanos desde Azure AD. Principal ventaja de la característica de diagnóstico:
- Proporciona el procedimiento de diagnóstico para reducir los escenarios de error de sincronización de atributos duplicados e indica soluciones específicas.
- Aplica la corrección de escenarios dedicados desde Azure AD para resolver el error con un solo clic.
- No es necesaria ninguna actualización o configuración para habilitar la característica.
Lea más sobre la [resistencia duplicada](https://aka.ms/dupattributeresdocs) de Azure AD.

## <a name="problems"></a>Problemas
### <a name="common-scenario"></a>Escenario común
Cuando se producen los errores de sincronización **QuarantinedAttributeValueMustBeUnique** y **AttributeValueMustBeUnique**, es habitual ver conflictos con el nombre principal de usuario o las direcciones de proxy en Azure AD. Para resolver los errores de sincronización, puede actualizar el objeto de origen en conflicto desde el lado local. El error de sincronización se resolverá después de la sincronización siguiente. Por ejemplo, en la imagen que tiene a continuación se indica que dos usuarios tienen un conflicto en UserPrincipalName como *Joe.J@contoso.com*. Los objetos en conflicto se ponen en cuarentena en Azure AD. 

![Escenario común de diagnóstico de errores de sincronización](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Escenario del objeto huérfano
En ocasiones, puede encontrarse con que un usuario existente pierde el delimitador de origen. La eliminación del objeto de origen se produjo en una instancia de AD local, pero el cambio de señal de eliminación nunca se sincronizó con Azure AD. Esto puede suceder debido a motivos como un problema del motor de sincronización o la migración del dominio. Cuando se restaura o recrea el mismo objeto, el usuario existente lógicamente debe ser el usuario que se sincronice desde el delimitador de origen. Para un usuario que existe solo como un objeto en la nube, también se puede comprobar que el usuario en conflicto se sincronice con Azure AD y no coincida con la sincronización con el objeto existente. No hay ninguna manera directa de reasignar el delimitador de origen. Conozca más sobre esto en el artículo de [KB existente](https://support.microsoft.com/help/2647098). Por ejemplo, el objeto existente en Azure AD conserva la licencia de Joe. El objeto recién sincronizado con el delimitador de origen diferente se produjo en el estado de atributo duplicado en Azure AD. Los cambios de Joe en la instancia local de AD no podrán aplicarse al usuario original de Joe (objeto existente) en Azure AD.  

![Escenario de diagnóstico de errores de sincronización del objeto huérfano](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Pasos de diagnóstico y de solución de problemas en Connect Health 
La característica de diagnóstico admite objetos de usuario con los siguientes atributos duplicados:

| Nombre del atributo | Tipos de error de sincronización|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique o AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique o AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Requiere permiso de **administrador global** o **colaborador** desde la configuración de RBAC para obtener acceso a esta característica. 
>

Si sigue los pasos de Azure Portal, podrá restringir los detalles del error de sincronización y proporcionar soluciones más específicas:

![Pasos para el diagnóstico de errores de sincronización](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Desde Azure Portal, podrá seguir unos pocos pasos para detectar los escenarios específicos que pueden corregirse:  
1.  En la columna de estado de diagnóstico, el estado mostrará si hay un flujo de solución de problemas posible para acotar el caso de error y, de ser posible, corregirlo directamente desde Azure Active Directory.
| Status | ¿Qué significa? |
| ------------------ | -----------------|
| No iniciado | No ha visitado este proceso de diagnóstico. Según el resultado del diagnóstico, hay una manera posible de corregir el error de sincronización desde el portal directamente. |
| Corrección manual necesaria | El error no se ajusta a los criterios de solución disponibles en el portal. El caso puede ser: (1) los tipos de objeto en conflicto no son usuarios; (2) ya pasó por los pasos de diagnóstico y no hay una solución disponible en el portal. En este caso, la corrección desde el nivel local seguirá siendo una de las soluciones. [Más información sobre la corrección local](https://support.microsoft.com/help/2647098). | 
| Sincronización pendiente | Se aplicó la corrección. Se espera al siguiente ciclo de sincronización para borrar el error. |
>[!IMPORTANT]
> La columna de estado de diagnóstico se restablecerá después de cada ciclo de sincronización. 
>

2.  Al hacer clic en el botón **Diagnosticar** en la hoja de detalles de error, tendrá que responder unas preguntas e identificar los detalles del error de sincronización. Responder las preguntas le ayudará a identificar el caso del escenario de objeto huérfano. 

3.  Si no hay un botón **Cerrar** al final del diagnóstico, significa que no hay ninguna corrección rápida disponible desde el portal en función de las respuestas aportadas. Consulte la solución que se muestra en el último paso. La corrección desde el nivel local seguirá siendo una de las soluciones. Después de hacer clic en el botón Cerrar, verá que el estado del error de sincronización actual cambiará a **Corrección manual necesaria**. El estado se conservará durante el ciclo de sincronización actual.

4.  Una vez que se identifique el caso del objeto huérfano, podrá solucionar los errores de sincronización de atributos duplicados directamente desde el portal. Haga clic en el botón **Aplicar corrección** para desencadenar el proceso. El estado del error de sincronización actual se actualizará a **Sincronización pendiente**.

5.  Tras el siguiente ciclo de sincronización, el error debe haberse quitado de la lista.

## <a name="how-to-answer-the-diagnosis-questions"></a>Cómo responder a las preguntas de diagnóstico 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>¿Existe el usuario en Active Directory local?

La pregunta intenta identificar el objeto de origen de un usuario existente de la instancia de Active Directory local.  
1.  Compruebe si Active Directory tiene un objeto con el valor UserPrincipalName especificado. Si no lo tiene, responda No.
2.  Si efectivamente lo tiene, compruebe si el objeto sigue en el ámbito de sincronización.  
  - Busque en el espacio conector de Azure AD mediante el DN.
  - Si el objeto se encuentra con el estado **Pendiente de agregación**, responda No. Azure AD Connect no es capaz de conectar el objeto al objeto de AD correcto.
  - Si no se encuentra el objeto, responda Sí.

> Si tomamos el siguiente diagrama como ejemplo, la pregunta intenta identificar si *Joe Jackson* sigue existiendo en la instancia local de Active Directory.
En el **escenario común**, ambos usuarios, *Joe Johnson* y *Joe Jackson*, estarán presentes en la instancia local de Active Directory. Los objetos en cuarentena son dos usuarios distintos.

![Escenario común de diagnóstico de errores de sincronización](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> Para el **escenario de objeto huérfano**, solo un usuario, *Joe Johnson*, estará presente en la instancia local de Active Directory:

![Escenario de diagnóstico de errores de sincronización del objeto huérfano](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>¿Pertenecen al mismo usuario estas dos cuentas?
La pregunta comprueba el usuario entrante en conflicto y el objeto de usuario existente en Azure AD para ver si pertenecen al mismo usuario.  
1.  El objeto en conflicto se sincroniza nuevamente con Azure Active Directory. Compare el objeto con:  
  - Display Name (Nombre para mostrar)
  - Nombre principal de usuario
  - Id. de objeto
2.  Si no pudo compararlos, compruebe si Active Directory contiene objetos con los valores UserPrincipalNames proporcionados. Si encuentra ambos valores, responda NO.  

> En el caso siguiente, los dos objetos pertenecen al mismo usuario, *Joe Johnson*.

![Escenario de diagnóstico de errores de sincronización del objeto huérfano](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>Qué ha ocurrido después de aplicar la corrección para el escenario de objeto huérfano
En función de las respuestas a las preguntas mencionadas, podrá ver el botón **Aplicar corrección** cuando haya una corrección disponible en Azure AD. En este caso, el objeto local se sincroniza con un objeto inesperado de Azure AD. Los dos objetos se asignan mediante el "delimitador de origen". El cambio a aplicar llevará a cabo pasos como:
- Actualizar el delimitador de origen para el objeto correcto en Azure AD.
- Eliminar el objeto en conflicto de Azure AD si está presente.

![Diagnóstico de errores de sincronización después de la corrección](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> El cambio Aplicar corrección solo se aplicará a los casos de objetos huérfanos.
>

Después de los pasos anteriores, el usuario podrá obtener acceso al recurso original, que es el vínculo a un objeto existente. El valor **Diagnose status** (Estado de diagnóstico) en la vista de lista se actualizará a **Sincronización pendiente**. El error de sincronización se resolverá después de la sincronización siguiente. Connect Health ya no mostrará el error de sincronización resuelto en la vista de lista. 


## <a name="faq"></a>Preguntas más frecuentes
 -  ¿Qué ocurre si se produce un error en la ejecución de la aplicación?  
Si se produce un error en la ejecución, es posible que Azure AD Connect ejecute un error de exportación en este momento. Actualice la página del portal y vuelva a intentar la operación después de la siguiente sincronización. El ciclo de sincronización predeterminado es de 30 minutos. 

 -  ¿Qué ocurre si el **objeto existente** debe ser el objeto que se debe eliminar?  
Si el objeto existente debe eliminarse en este caso, el proceso no supone un cambio en el delimitador de origen. Podrá corregirlo en la instancia local de AD.  

 -  ¿Cuál es el permiso de usuario para poder aplicar la revisión?  
El administrador global y el colaborador de configuración de RBAC tendrán el permiso para obtener acceso al proceso de diagnóstico y solución de problemas.

 -  ¿Hay que configurar AAD Connect o actualizar el agente de Azure AD Connect Health para esta característica?  
No, el proceso de diagnóstico es una característica completa basada en la nube.

 -  Si el objeto existente se elimina mediante software, ¿el proceso de diagnóstico restaurará el objeto para que vuelva a estar activo?  
No, la corrección no actualizará el atributo de objeto, a excepción del delimitador de origen. 
