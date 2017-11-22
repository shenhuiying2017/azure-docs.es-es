---
title: Procedimientos recomendados de seguridad de acceso e identidad de Azure | Microsoft Docs
description: "Este artículo proporciona un conjunto de procedimientos recomendados para la administración de identidades y la seguridad del control de acceso mediante las funcionalidades integradas de Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: d80fdd5a2e4339823c05368d76de333f3314d4ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Procedimientos recomendados para la administración de identidades y la seguridad del control de acceso en Azure
Muchos consideran que la identidad es la nueva capa límite de la seguridad, asumiendo ese papel desde la perspectiva tradicional centrada en la red. Esta evolución del eje principal de la atención y las inversiones en materia de seguridad procede del hecho de que los perímetros de red se han vuelto cada vez más porosos y que la defensa perimetral no puede ser tan eficaz como en su día lo fue, antes de la explosión de los dispositivos [BYOD](http://aka.ms/byodcg) y las aplicaciones de nube.

En este artículo, veremos un conjunto de procedimientos recomendados para la seguridad del control de acceso y la administración de identidades en Azure. Estos procedimientos recomendados proceden de nuestra experiencia con [Azure AD](../active-directory/active-directory-whatis.md) y las experiencias de clientes como usted.

Para cada procedimiento recomendado, explicaremos:

* Qué es el procedimiento recomendado
* Por qué le conviene habilitar este procedimiento recomendado
* Cuál podría ser el resultado si no habilita el procedimiento recomendado
* Alternativas posibles al procedimiento recomendado
* Cómo aprender a habilitar el procedimiento recomendado

Este artículo sobre procedimientos recomendados para la seguridad del control de acceso y la administración de identidades en Azure se basa en una opinión consensuada y en las funcionalidades y conjuntos de características de la plataforma de Azure que existen en el momento de su publicación. Las opiniones y las tecnologías cambian con el tiempo, por lo que se actualizará de forma periódica para reflejar esos cambios.

Los procedimientos recomendados para la seguridad del control de acceso y la administración de identidades en Azure que se describen en este artículo son:

* Centralización de la administración de identidades
* Habilitación del inicio de sesión único (SSO)
* Implementación de la administración de contraseñas
* Aplicación de Multi-Factor Authentication (MFA) para los usuarios
* Uso del control de acceso basado en rol (RBAC)
* Control de las ubicaciones donde se crean los recursos mediante Resource Manager
* Guía para los desarrolladores para aprovechar las funcionalidades de identidad para aplicaciones para SaaS
* Supervisión activa de actividades sospechosas

## <a name="centralize-your-identity-management"></a>Centralización de la administración de identidades
Un paso importante hacia la protección de la identidad es asegurarse de que los responsables de TI pueden administrar las cuentas desde un solo lugar con independencia de dónde se haya creado esta cuenta. Aunque la mayoría de las organizaciones de TI de las empresas tienen su directorio de cuentas principal en el entorno local, las implementaciones de nube híbridas están en auge y es importante que comprenda cómo integrar los directorios local y en la nube y proporcionar una experiencia perfecta para el usuario final.

Para lograr este escenario de [identidad híbrida](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) recomendamos dos opciones:

* Sincronizar el directorio local con el directorio en la nube mediante Azure AD Connect
* Federar la identidad local con su directorio en la nube con [Servicios de federación de Active Directory](https://msdn.microsoft.com/library/bb897402.aspx) (AD FS)

Las organizaciones que no integren la identidad local con la identidad de nube experimentarán una mayor sobrecarga administrativa en la administración de las cuentas, lo que aumentará la probabilidad de errores e infracciones de seguridad.

Para más información sobre la sincronización de Azure AD, lea el artículo [Integración de las identidades locales con Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Habilitación del inicio de sesión único (SSO)
Cuando es necesario administrar varios directorios, esto se convierte en un problema administrativo no solo para TI, sino también para los usuarios finales que tendrán que recordar varias contraseñas. Con el inicio de sesión único ([SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)) proporcionará a los usuarios la posibilidad de usar el mismo conjunto de credenciales para iniciar sesión y acceder a los recursos que necesitan, con independencia de dónde se ubique este recurso, tanto si es en el entorno local como en la nube.

Use SSO para permitir que los usuarios accedan a sus [aplicaciones para SaaS](../active-directory/active-directory-appssoaccess-whatis.md) en función de su cuenta de la organización en Azure AD. Esto no solo es aplicable a las aplicaciones para SaaS de Microsoft, sino también a otras aplicaciones, como [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) y [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). La aplicación se puede configurar para usar Azure AD como un proveedor de [identidades basado en SAML](../active-directory/fundamentals-identity.md). Como control de seguridad, Azure AD no emitirá un token que les permita iniciar sesión en la aplicación, a menos que se les concediera acceso mediante Azure AD. Puede concederles acceso directamente o a través de un grupo del que sean miembros.

> [!NOTE]
> La decisión de utilizar SSO afectará al modo en que se integra el directorio local con el directorio en la nube. Si desea utilizar SSO, deberá usar la federación, ya que la sincronización de directorios solo proporciona la [misma experiencia de inicio de sesión](../active-directory/active-directory-aadconnect.md).
>
>

Las organizaciones que no usan SSO para sus usuarios y aplicaciones están más expuestas a escenarios en los que los usuarios tienen varias contraseñas, lo que aumenta directamente la probabilidad de reutilización o uso de contraseñas poco seguras.

Puede aprender más sobre SSO de Azure AD en el artículo [Servicios de federación de Active Directory y personalización con Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Implementación de la administración de contraseñas
En escenarios donde tenga varios inquilinos o quiera permitir que los usuarios [restablezcan su propia contraseña](../active-directory/active-directory-passwords-update-your-own-password.md), es importante utilizar directivas de seguridad adecuadas para evitar un uso inadecuado. En Azure, puede aprovechar la funcionalidad de autoservicio de restablecimiento de contraseña y personalizar las opciones de seguridad para adaptarlas a sus requisitos empresariales.

Es especialmente importante conocer las experiencias de estos usuarios cuando intentan realizar estos pasos y aprender de ellas. En función de estas experiencias, elabore un plan para mitigar los posibles problemas que pueden producirse durante la implementación en el caso de un grupo más grande. También se recomienda utilizar la [Visión operativa con los informes de la administración de contraseñas](../active-directory/active-directory-passwords-get-insights.md) para supervisar los usuarios que se registran.

Las organizaciones que desean evitar llamadas al soporte técnico para el cambio de contraseña pero que permiten que los usuarios restablezcan sus propias contraseñas, son más susceptibles a un volumen de llamadas más alto al servicio de asistencia técnica debido a problemas con la contraseña. En organizaciones que tienen varios inquilinos, es imperativo que implemente este tipo de funcionalidad y permita que los usuarios realicen el restablecimiento de contraseña dentro de los límites que se establecieron en la directiva de seguridad.

Puede aprender más sobre el restablecimiento de contraseña en el artículo [Implementación de la Administración de contraseñas y formación a los usuarios para que la utilicen](../active-directory/active-directory-passwords-best-practices.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Aplicación de Multi-Factor Authentication (MFA) para los usuarios
En organizaciones que deben cumplir los estándares del sector, como [PCI DSS versión 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), Multi-Factor Authentication es una funcionalidad imprescindible para autenticar a los usuarios. Además de cumplir los estándares del sector, la aplicación de MFA para autenticar a los usuarios ayuda a las organizaciones a mitigar el tipo de ataque de robo de credenciales, como [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Si habilita Azure MFA para los usuarios, agrega una segunda capa de seguridad a sus transacciones e inicios de sesión. En este caso, es posible que durante una transacción se acceda a un documento que se encuentra en un servidor de archivos o en SharePoint Online. Azure MFA también ayuda al equipo de TI a reducir la probabilidad de que se acceda a los datos de la organización usando credenciales en peligro.

Por ejemplo, suponga que aplica Azure MFA para los usuarios y lo configura para usar una llamada de teléfono o un mensaje de texto como confirmación. Si las credenciales del usuario se ven comprometidas, el atacante no podrá acceder a los recursos dado que no tendrá acceso al teléfono del usuario. Las organizaciones que no agregan capas de protección de la identidad adicionales son más susceptibles a ataques de robo de credenciales, lo que puede llevar a poner en peligro la seguridad de los datos.

Una alternativa para las organizaciones que quieren mantener todo el control local de la autenticación es usar [Servidor Microsoft Azure Multi-Factor Authentication ](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), también denominado MFA local. Mediante este método, podrá seguir aplicando Multi-Factor Authentication y mantener el servidor MFA local.

Para más información sobre Azure MFA, lea el artículo [Introducción a Azure Multi-Factor Authentication en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Uso del control de acceso basado en rol (RBAC)
En organizaciones que quieren aplicar directivas de seguridad para el acceso a los datos, es imperativo restringir el acceso en función de los principios de seguridad [necesidad de saber](https://en.wikipedia.org/wiki/Need_to_know) y [mínimo privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege). El control de acceso basado en rol (RBAC) de Azure sirve para asignar permisos a usuarios, grupos y aplicaciones en un ámbito determinado. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un único recurso.

Puede aprovechar los [roles de RBAC integrados](../active-directory/role-based-access-built-in-roles.md) en Azure para asignar privilegios a los usuarios. Considere usar *Colaborador de la cuenta de almacenamiento* para los operadores en la nube que necesiten administrar cuentas de almacenamiento y el rol *Colaborador de la cuenta de almacenamiento clásica* para administrar cuentas de almacenamiento clásicas. En el caso de los operadores en la nube que necesiten administrar máquinas virtuales y cuentas de almacenamiento, podría agregarlos al rol *Colaborador de la máquina virtual*.

Es posible que las organizaciones que no apliquen el control de acceso a los datos mediante funcionalidades como RBAC estén concediendo más privilegios de los necesarios a sus usuarios. Esto puede poner en peligro los datos al permitir que los usuarios accedan a determinados tipos de datos (por ejemplo, aquellos que son críticos para la empresa) a los que no deberían tener acceso en primer lugar.

Puede aprender más sobre Azure RBAC en el artículo [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Control de las ubicaciones donde se crean los recursos mediante Resource Manager
Es muy importante permitir que los operadores de nube realicen tareas pero al mismo tiempo impedir que transgredan las convenciones que son necesarias para administrar los recursos de la organización. Las organizaciones que quieran controlar las ubicaciones donde se crean los recursos deben codificarlas de forma segura.

Para ello, pueden crear directivas de seguridad que contengan definiciones que describan las acciones o los recursos que se deniegan específicamente. Esas definiciones de directivas se asignan en el ámbito deseado, como la suscripción, el grupo de recursos o un recurso individual.

> [!NOTE]
> No es lo mismo que RBAC; en realidad, aprovechan RBAC para autenticar a los usuarios que tienen privilegios para crear esos recursos.
>
>

Utilizan [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para crear directivas personalizadas también para escenarios donde las organizaciones quieren permitir operaciones solo cuando esté asociado el centro de costo adecuado; en caso contrario, denegarán la solicitud.

Las organizaciones que no controlan cómo se crean los recursos son más susceptibles a que los usuarios puedan hacer un mal uso del servicio y crear más recursos de los necesarios. Endurecer el proceso de creación de recursos es un paso importante para proteger escenarios en los que intervienen varios inquilinos.

Para aprender más sobre la creación de directivas con Azure Resource Manager, lea el artículo [¿Qué es Azure Policy?](../azure-policy/azure-policy-introduction.md).

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Guía para los desarrolladores para aprovechar las funcionalidades de identidad para aplicaciones para SaaS
La identidad de los usuarios se aprovechará en muchos escenarios cuando los usuarios accedan a [aplicaciones para SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) que se puedan integrar con directorios locales o en la nube. Lo primero y más importante, recomendamos que los desarrolladores usen una metodología segura para desarrollar estas aplicaciones, como [Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft](https://www.microsoft.com/sdl/default.aspx). Azure AD simplifica la autenticación para los desarrolladores, ya que ofrece identificación como servicio, con compatibilidad con protocolos genéricos estándar del sector como [OAuth 2.0](http://oauth.net/2/) y [OpenID Connect](http://openid.net/connect/), además de bibliotecas de código abierto para distintas plataformas.

Asegúrese de registrar las aplicaciones que externalicen la autenticación a Azure AD. Este procedimiento es obligatorio. La razón de ello es que Azure AD necesita coordinar la comunicación con la aplicación al gestionar inicios de sesión únicos (SSO) o intercambiar tokens. La sesión del usuario expira cuando expira la duración del token emitido por Azure AD. Evalúe siempre si la aplicación debe usar este tiempo o si puede reducirlo. Reducir la duración puede actuar como medida de seguridad que obligará a los usuarios a cerrar sesión en función de un período de inactividad.

Las organizaciones que no aplican control de identidad para el acceso a las aplicaciones y que no guían a sus desarrolladores en el modo de integrar las aplicaciones de forma segura con su sistema de administración de identidades, pueden ser más susceptibles al tipo de ataque de robo de identidad, por ejemplo [autenticación débil y administración de sesiones que se describen en Open Web Application Security Project (OWASP) Top 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Puede aprender más sobre los escenarios de autenticación para aplicaciones SaaS en [Escenarios de autenticación para Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Supervisión activa de actividades sospechosas
Según el [informe de infracción de datos de Verizon 2016](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), la seguridad de las credenciales se está viendo comprometida cada vez más y se está convirtiendo en el negocio más lucrativo de los ciberdelincuentes. Por este motivo, es importante contar con un sistema activo de supervisión de la identidad que pueda detectar rápidamente actividades con comportamiento sospechoso y desencadenar una alerta para investigarlas más a fondo. Azure AD presenta dos funcionalidades principales que pueden ayudar a las organizaciones a supervisar sus identidades: los [informes de anomalías](../active-directory/active-directory-view-access-usage-reports.md) de Azure AD Premium y funcionalidad de [protección de identidades](../active-directory/active-directory-identityprotection.md) de Azure AD.

Asegúrese de utilizar los informes de anomalías para identificar los intentos de inicio de sesión [sin seguimiento](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), ataques [por fuerza bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) contra una cuenta determinada, intentos de inicio de sesión desde varias ubicaciones, inicios de sesión desde [dispositivos infectados](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md) y direcciones IP sospechosas. Tenga en cuenta que se trata de informes. Es decir, debe contar con procesos y procedimientos para que los administradores de TI ejecuten dichos informes diariamente o a petición (normalmente en un escenario de respuesta a incidentes).

En cambio, Azure AD Identity Protection es un sistema de supervisión activo y marcará los riesgos reales en su propio panel. Además de eso, también recibirá notificaciones de resumen diarias por correo electrónico. Le recomendamos que ajuste el nivel de riesgo según sus requisitos empresariales. El nivel de riesgo de un evento de riesgo es una indicación (Alto, Medio o Bajo) de su gravedad. El nivel de riesgo ayuda a los usuarios de Identity Protection a dar prioridad a las acciones que deben tomar para reducir el riesgo para su organización.

Las organizaciones que no supervisen activamente sus sistemas de identidad corren el riesgo de comprometer las credenciales de los usuarios. Si las organizaciones no saben que están teniendo lugar actividades sospechosas con estas credenciales, no podrán mitigar este tipo de amenaza.
Puede aprender más sobre Azure Identity Protection en [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
