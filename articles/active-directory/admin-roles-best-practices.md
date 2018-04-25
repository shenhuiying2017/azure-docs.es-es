---
title: Procedimientos recomendados para proteger el acceso administrativo en Azure AD | Microsoft Docs
description: Asegúrese de que el acceso administrativo y las cuentas de administrador de la organización son seguras. Para arquitectos de sistemas y profesionales de TI que configuran Azure AD, Azure y Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
ms.author: curtand
ms.date: 03/09/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 09ee56627f6c254362d9fbc3c665494418efb1dc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Protección del acceso con privilegios para las implementaciones híbridas y en la nube en Azure AD

La seguridad de la mayoría de los recursos empresariales de la organización moderna, o de todos ellos, depende de la integridad de las cuentas con privilegios que administran los sistemas de TI. Actores malintencionados, entre los que se incluyen ciberatacantes, a menudo tienen como objetivo las cuentas de administrador y otros elementos con acceso privilegiado para intentar obtener acceso rápidamente a datos confidenciales y a sistemas mediante ataques de robo de credenciales. En el caso de los servicios en la nube, la prevención y la respuesta son responsabilidades conjuntas del proveedor de los servicios en la nube y del cliente. Para más información acerca de las amenazas más recientes a los puntos de conexión y a la nube, consulte el [informe de inteligencia de seguridad de Microsoft](https://www.microsoft.com/security/sir/default.aspx). Este artículo puede ayudarlo a desarrollar un mapa de ruta que cubra el espacio existente entre sus planes actuales y las directrices que se describen aquí.

> [!NOTE] 
> Microsoft se compromete a ofrecer los mayores niveles de confianza, transparencia, así como de cumplimiento de estándares y reglamentaciones. Aprenda no solo la forma en que el equipo de respuesta a incidentes globales de Microsoft mitiga los efectos de los ataques contra los servicios en la nube, sino también la forma en que se incorpora la seguridad en los productos empresariales y servicios en la nube de Microsoft en [Microsoft Trust Center - Seguridad](https://www.microsoft.com/en-us/trustcenter/security)y destinos de cumplimiento de Microsoft en [Microsoft Trust Center - Cumplimiento normativo](https://www.microsoft.com/en-us/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
En la mayoría de las organizaciones, la seguridad de los recursos empresariales depende de la integridad de las cuentas con privilegios que administran los sistemas de TI. Los ciberatacantes se centran en el acceso con privilegios a los sistemas de la infraestructura (como Active Directory y Azure Active Directory) para obtener acceso a información confidencial de una organización. 

Los enfoques tradicionales que se centran en proteger los puntos de entrada y salida de una red como perímetro de seguridad principal son menos eficaces debido al aumento del uso de aplicaciones SaaS y dispositivos personales en Internet. La sustitución natural del perímetro de seguridad de red en las complejas empresas modernas son los controles de autenticación y autorización en la capa de identidad de una organización. 

Las cuentas administrativas con privilegios son las que realmente controlan este nuevo "perímetro de seguridad". Es fundamental proteger el acceso con privilegios, independientemente de que el entorno sea local, en la nube o servicios locales híbridos y hospedados en la nube. La protección del acceso contra determinados adversarios requiere que se adopte un enfoque global y bien analizado para proteger los sistemas de su organización de todo riesgo. 

La seguridad del acceso con privilegios requiere que se realicen cambios en:
* Los procesos, las prácticas administrativas y la administración del conocimiento.
* Los componentes técnicos, como las defensas de los hosts, las protecciones de las cuentas y la administración de identidades.

Este documento se centra principalmente en la creación de un mapa de ruta para proteger las identidades y el acceso que se administran o notifican en Azure AD, Microsoft Azure, Office 365 y otros servicios en la nube. Para ver la información relativa a las organizaciones que tienen cuentas administrativas locales, consulte la guía para el acceso con privilegios a entornos locales e híbridos administrado desde Active Directory en [Protección del acceso con privilegios](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Las directrices de este artículo hacen referencia principalmente a las características de Azure Active Directory que se incluyen en los planes P1 y P2 de Azure Active Directory Premium. Azure Active Directory Premium P2 se incluye en los conjuntos EMS E5 y Microsoft 365 E5. En esta guía se da por supuesto que la organización ya ha adquirido licencias de Azure AD Premium P2 para los usuarios. Si no es así, es posible que algunas de las instrucciones no sean válidas para la organización. Por otra parte, en este artículo, el término administrador global será sinónimo de "administrador de la empresa" o "administrador de inquilinos".

## <a name="develop-a-roadmap"></a>Desarrollo de una hoja de ruta 

Microsoft recomienda que se desarrolle una hoja de ruta, y se siga, para proteger el acceso con privilegios frente a ciberatacantes. Dicha hoja de ruta se puede ajustar en cualquier momento para adecuarla a las funcionalidades existentes y a los requisitos concretos de cualquier organización. Cada fase de la hoja de ruta debería no solo aumentar el costo que supone a los adversarios atacar el acceso con privilegios en los recursos locales, híbridos y en la nube, sino también dificultar dicho ataque. Microsoft recomienda las cuatro siguientes fases de la hoja de ruta. En esta hoja de ruta que se recomienda, están programadas en primer lugar las implementaciones más rápidas y eficaces, según la experiencia de Microsoft en ciberataques e implementación de respuestas a ellos. Los plazos indicados en esta hoja son aproximados.

![Fases de la hoja de ruta con plazos:](./media/admin-roles-best-practices/roadmap-timeline.png)

* Fase 1 (24 a 48 horas): elementos críticos que es aconsejable realizar de inmediato.

* Fase 2 (2 a 4 semanas): mitigar las técnicas de ataque que se usan con más frecuencia.

* Fase 3 (1 a 3 meses): crear visibilidad y crear control total de la actividad de administración.

* Fase 4 (6 meses, y más): seguir creando defensas que protejan aún más la plataforma de seguridad.

El marco de esta hoja de ruta está diseñado para maximizar el uso de tecnologías de Microsoft que es posible que ya haya implementado. También se puede sacar provecho de las principales tecnologías de seguridad actuales y futuras, e integrar herramientas de seguridad de otros proveedores que ya se hayan implementado o que se puedan implementar. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fase 1: elementos críticos que es aconsejable realizar de inmediato

![Fase 1](./media/admin-roles-best-practices/stage-one.png)

La fase 1 de la hoja de ruta se centra en tareas críticas que se implementan de forma rápida y sencilla. Se recomienda realizar estos pocos elementos de inmediato, en las primeras 24 a 48 horas, para garantizar un nivel básico de acceso con privilegios seguro. Esta fase de la hoja de ruta del acceso con privilegios protegido incluye las siguientes acciones:

### <a name="general-preparation"></a>Preparación general

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Activación de Azure AD Privileged Identity Management

Si aún no ha activado Azure AD Privileged Identity Management (PIM), hágalo en el inquilino de producción. Tras activar Privileged Identity Management, recibirá mensajes de correo electrónico de notificación si se producen cambios en el rol de acceso con privilegios. Estas notificaciones advierten rápidamente cuando se agregan más usuarios a roles con privilegios elevados en el directorio.

Azure AD Privileged Identity Management se incluye en Azure AD Premium P2 o EMS E5. Estas soluciones le ayudan a proteger el acceso a aplicaciones y recursos tanto en el entorno local como en la nube. Si aún no tiene Azure AD Premium P2 o EMS E5, y desea evaluar varias de las capacidades a las que se hace referencia en esta guía, suscríbase a la [evaluación gratuita de 90 días de Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Use estas licencias de prueba para probar AD Privileged Identity Management de Azure y Azure AD Identity Protection para supervisar la actividad mediante las alertas, las auditorías y los informes de seguridad avanzados de Azure AD.

Una vez que haya activado Azure AD Privileged Identity Management:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con una cuenta que tenga el rol de administrador global en el inquilino de producción.

2. Para seleccionar el inquilino en el que desea usar Privileged Identity Management, seleccione el nombre de usuario en la esquina superior derecha de Azure Portal.

3. Seleccione **Todos los servicios** y filtre la lista por **Azure AD Privileged Identity Management**.

4. Abra Privileged Identity Management en la lista **Todos los servicios** y ánclelo al panel.

A la primera persona que use Azure AD Privileged Identity Management en el inquilino se le asignan automáticamente los roles de **Administrador de seguridad** y **Administrador de rol con privilegios** del inquilino. Los administradores de rol con privilegios son los únicos que pueden administrar las asignaciones de roles de directorio de Azure AD de los usuarios. Además, después de agregar Azure AD Privileged Identity Management, se muestra al Asistente para la seguridad, que le guía a través de la detección y asignación iniciales. Ahora puede salir del asistente sin realizar ningún cambio adicional. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificación y clasificación de las cuentas que están en roles con privilegios elevados 

Después de activar Azure AD Privileged Identity Management, vea los usuarios que están en los roles de directorio Administrador global, Administrador de rol con privilegios, Administrador de Exchange Online y Administrador de SharePoint Online. Si no tiene Azure AD Privileged Identity Management en su inquilino, puede usar la [API de PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comience con el rol de administrador global, ya que este rol es genérico: un usuario al que se haya asignado este rol de administrador tiene los mismos permisos en todos los servicios en la nube a los que se haya suscrito la organización, independientemente de que el rol se haya asignado en el Portal de Office 365, en Azure Portal o por medio del módulo de Azure AD para Microsoft PowerShell. 

Quite todas las cuentas que no sean necesarias en los roles y clasifique las restantes que estén asignadas a roles de administrador:

* Asignadas individualmente a usuarios administrativos y que también puedan usarse para fines no administrativos (por ejemplo, correo electrónico personal)
* Asignadas individualmente a usuarios administrativos y designadas solo para fines administrativos
* Compartidas entre varios usuarios
* Para los escenarios de acceso de máxima emergencia
* Para scripts automatizados
* Para usuarios externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definición de un mínimo de dos de cuentas de acceso de emergencia 

Asegúrese de que no llegará a una situación en la que se les podría bloquear accidentalmente de la administración de su inquilino de Azure AD debido a su incapacidad para iniciar sesión o activar la cuenta de un usuario individual existente como administrador. Por ejemplo, si la organización está federada en un proveedor de identidades local, es posible que este no esté disponible, por lo que los usuarios no pueden iniciar sesión en el entorno local. El impacto de una falta involuntaria de acceso administrativo se puede mitigar mediante el almacenamiento de dos o más cuentas de acceso de emergencia en el inquilino.

Las cuentas de acceso de emergencia ayudan a las organizaciones a restringir el acceso con privilegios en un entorno de Azure Active Directory existente. Estas cuentas tienen privilegios elevados y no se asignan a usuarios específicos. Las cuentas de acceso de emergencia se limitan a situaciones "excepcionales" o de emergencia en las que no se pueden usar las cuentas administrativas normales. Las organizaciones deben asegurarse el objetivo de controlar y reducir el uso de las cuentas de emergencia solo para aquellos casos en los que sea necesario. 

Evalúe las cuentas que están asignadas al rol de administrador global o que son aptas para él. Si no ve que ninguna cuenta que se use solo en la nube emplee el dominio *.onmicrosoft.com (destinado al acceso de emergencia "excepcional"), para crearlos. Para más información, consulte [Administración de cuentas administrativas de acceso de emergencia en Azure AD](active-directory-admin-manage-emergency-access-accounts.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Activación de la autenticación multifactor y registro de las restantes cuentas de administrador no federadas de usuario único con privilegios elevados 

Requiera Azure Multi-Factor Authentication (MFA) en el inicio de sesión a todos los usuarios individuales a los que se haya asignado permanentemente uno o varios de los roles de administrador de Azure AD: Administrador global, Administrador de rol con privilegios, Administrador de Exchange Online y Administrador de SharePoint Online. Use la guía para habilitar [Multi-Factor Authentication (MFA) en sus cuentas de administrador](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) y asegúrese de que todos esos usuarios se hayan registrado en [https://aka.ms/mfasetuphttps://aka.ms/mfasetup](https://aka.ms/mfasetup). Puede encontrar más información en los pasos 2 y 3 de [Protección del acceso a datos y servicios de Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fase 2: mitigar las técnicas de ataque que se usan con más frecuencia

![Fase 2](./media/admin-roles-best-practices/stage-two.png)

La fase 2 de la hoja de ruta se centra en mitigar las técnicas de ataque que se usan con más frecuencia para el robo y uso de credenciales y está diseñada para que se implementen en aproximadamente 2 a 4 semanas. Esta fase de la hoja de ruta del acceso con privilegios protegido incluye las siguientes acciones.

### <a name="general-preparation"></a>Preparación general

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Realice un inventario de los servicios, propietarios y administradores

Con el aumento de las directivas de bring-your-own-device (BYOD) y de trabajo desde casa y el crecimiento de la conectividad inalámbrica en las empresas, es fundamental supervisar quiénes se conectan a la red. Una auditoría de seguridad eficaz suele revelar aquellos dispositivos, aplicaciones y programas que se ejecutan en la red y que no tienen el soporte técnico de TI y, por lo tanto, son potencialmente no seguros. Para más información, consulte [Información general sobre la administración y la supervisión de la seguridad en Azure](../security/security-management-and-monitoring-overview.md). Asegúrese de que incluye las tareas siguientes en el proceso de inventario. 

* Identifique a los usuarios que tengan roles administrativos y los servicios donde pueden administrarlos.
* Use Azure AD Privileged Identity Management para averiguar qué usuarios de la organización tienen acceso de administrador a Azure AD, incluidos roles adicionales que no se enumeran en la fase 1.
* Más allá de los roles definidos en Azure AD, Office 365 incluye un conjunto de roles de administrador que se pueden asignar a los usuarios de una organización. Cada rol de administrador se asigna a funciones empresariales comunes y proporciona a las personas de su organización permisos para realizar tareas específicas en el centro de administración de Office 365. Use el Centro de administración de Office para averiguar qué usuarios de su organización tienen acceso de administrador a Office 365, lo que incluye roles no administrados en Azure AD. Para más información, consulte [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) y [Procedimientos recomendados de seguridad para Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Realice el inventario de otros servicios que use su organización, como Azure, Intune o Dynamics 365.
* Asegúrese de que sus cuentas de administrador (cuentas que se usan con fines de administración, no las cuentas diarias de los usuarios) tienen direcciones de correo electrónico activas asociadas y que se han registrado en Azure MFA o usan MFA localmente.
* Pida a los usuarios su justificación comercial para el acceso administrativo.
* Quite el acceso de administrador a los usuarios y servicios que no lo necesiten.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifique las cuentas de Microsoft que tengan roles administrativos y que deban cambiarse a cuentas profesionales o educativas 

A veces, los administradores globales iniciales de una organización vuelven a usar las credenciales de su cuenta de Microsoft existentes cuando empezaron a usar Azure AD. Dichas cuentas de Microsoft deben reemplazarse por cuentas individuales basadas en la nube o sincronizadas. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Asegúrese de que haya cuentas de usuario independientes y reenvío de correo electrónico para las cuentas de administrador globales 

Las cuentas de administrador globales no deben tener direcciones de correo electrónico personales, ya que los ciberatacantes suelen suplantar las identidades de las cuentas de correo electrónico personales. Para ayudar a separar los riesgos de internet (ataques de suplantación de identidad, exploración web involuntaria) de los privilegios administrativos, cree cuentas dedicadas para cada usuario con privilegios administrativos. 

Si aún no lo ha hecho, cree cuentas independientes para que los usuarios realicen tareas de administrador globales, para asegurarse de que no abren involuntariamente mensajes de correo electrónico ni ejecutan programas asociados con sus cuentas de administrador. Asegúrese de que el correo electrónico de dichas cuentas se reenvía a un buzón activo.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Asegúrese de que las contraseñas de las cuentas administrativas han cambiado recientemente

Asegúrese de que todos los usuarios han iniciado sesión en sus cuentas administrativas y han cambiado sus contraseñas al menos una vez en los últimos 90 días. Además, asegúrese de que todas las cuentas compartidas en las que varios usuarios conozcan la contraseña se han sincronizado las contraseñas recientemente.

#### <a name="turn-on-password-hash-synchronization"></a>Activación de la sincronización de hashes de contraseñas

La sincronización de hashes de contraseñas es una característica que se utiliza para sincronizar los hashes de los hashes de las contraseñas de los usuarios desde una instancia de Active Directory local con una instancia de Azure AD en la nube. Aunque decida usar la federación con Active Directory Federation Services (AD FS) u otros proveedores de identidades, opcionalmente puede configurar la sincronización de hashes de contraseñas de respaldo, por si su infraestructura local, como los servidores de AD o ADFS, sufre un error o deja de estar disponible temporalmente. Esto permite que los usuarios inicien sesión en el servicio con la misma contraseña que usan para iniciar sesión en su instancia local de Active Directory. Además, permite que Identity Protection detecte las credenciales que están en peligro mediante la comparación de los algoritmos hash de dichas contraseñas con contraseñas que se sepa que están en peligro, si un usuario ha usado su misma dirección de correo electrónico y contraseña en otros servicios que no estén conectados a Azure AD.  Para más información, consulte [Implement password hash synchronization with Azure AD Connect sync](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) (Implementación de la sincronización de hash de contraseñas mediante la sincronización de Azure AD Connect).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Requiera la autenticación multifactor (MFA) a los usuarios de todos los roles con privilegios, así como a los usuarios expuestos

Azure AD recomienda que requiera la autenticación multifactor (MFA) a todos los usuarios, incluidos los administradores y todos los restantes usuarios que se verían muy afectados si su cuenta corriera peligro (por ejemplo, los responsables financieros). De esta forma, se reduce el riesgo de ataques debidos a que una contraseña se ha puesto en peligro.

Active:

* [MFA en cuentas de alto riesgo](../multi-factor-authentication/multi-factor-authentication-security-best-practices.md) como las cuentas de los directores corporativos de una organización 
* [MFA para todas las cuentas de administrador asociadas a un usuario individual](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) para otras aplicaciones de SaaS conectadas 
* MFA para todos los administradores de aplicaciones SaaS de Microsoft, incluidos los administradores en roles que se administran en Exchange Online y el Portal de Office

Si usa Windows Hello for Business, el requisito de MFA se puede cumplir con la experiencia de inicio de sesión de Windows Hello. Para más información, consulte [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configure Identity Protection 

Azure AD Identity Protection es una herramienta de supervisión y generación de informes basada en algoritmos que se puede usar para detectar posibles vulnerabilidades que afectan a las identidades de una organización. Puede configurar respuestas automáticas a las actividades sospechosas detectadas y realizar la acción apropiada para resolverlas. Para obtener más información, consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obtenga su Puntuación segura de Office 365 (si usa Office 365)

Puntuación segura detecta los servicios de Office 365 que usa (como OneDrive, SharePoint y Exchange), examina la configuración y las actividades y las compara con una base de referencia establecida por Microsoft. Obtendrá una puntuación que se basa en su grado de realización de los procedimientos de seguridad recomendados. Cualquiera que tenga permisos de administrador (administrador global o un rol de administrador personalizado) en una suscripción de Office 365 Business Premium o Enterprise puede acceder a la Puntuación segura en [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Consulte la guía de seguridad y cumplimiento de Office 365 (si usa Office 365).

El [plan de seguridad y cumplimiento](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) describe tanto el método para que un cliente de Office 365 lo configure como la forma de sacar provecho de otras funcionalidades de EMS. Luego, lea los pasos 3-6 de [Protección del acceso a datos y servicios de Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) y la guía para [supervisar la seguridad y el cumplimiento en Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configure la supervisión de la actividad de Office 365 (si usa Office 365)

Puede supervisar la forma en que el personal de su organización usa los servicios de Office 365, lo que le permite identificar a los usuarios que tienen una cuenta administrativa y que puede que no necesiten acceso a Office 365 poerqu7e no inician sesión en esos portales. Para más información, consulte [Informes de actividades en el Centro de administración de Office 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Establezca los propietarios de plan de respuesta ante incidentes o emergencias

Responder eficazmente a los incidentes es una tarea compleja. Por consiguiente, el establecimiento de una funcionalidad adecuada de respuesta ante incidentes requiere una planeación y unos recursos sustanciales. Es fundamental supervisar continuamente si hay ciberataques y establecer procedimientos para asignar prioridades al control de incidentes. El uso de métodos eficaces de recopilar, analizar y generar informes de los datos es vital para crear relaciones y para establecer comunicación con otros grupos y propietarios de planes internos. Para más información, consulte [Centro de respuesta de seguridad de Microsoft](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteja las cuentas administrativas con privilegios locales, si no lo ha hecho

Si su inquilino de Azure Active Directory está sincronizado con la instancia local de Active Directory, a continuación, siga las instrucciones de [Hoja de ruta de la seguridad del acceso con privilegios guía básica de acceso con privilegios](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fase 1. Aquí se incluye la creación de cuentas de administrador independientes para usuarios que necesiten realizar tareas administrativas locales, la implementación de Estaciones de trabajo con privilegios de acceso para administradores de Active Directory y la creación de contraseñas de administrador local únicas para estaciones de trabajo y servidores.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Pasos adicionales para las organizaciones que administran el acceso a Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Complete un inventario de suscripciones

Utilice Enterprise Portal y Azure Portal para identificar las suscripciones de la organización que hospedan aplicaciones de producción. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Eliminación de cuentas de Microsoft de roles de administrador

Las cuentas Microsoft de otros programas, como Xbox, Live y Outlook no deben usarse como cuentas de administrador para las suscripciones de la organización. Quite el estado de administrador de todas las cuentas de Microsoft y sustitúyalas por cuentas profesionales o educativas de Active Directory (por ejemplo, chris@contoso.com).

#### <a name="monitor-azure-activity"></a>Supervise la actividad de Azure

El registro de actividad de Azure proporciona un historial de los eventos de nivel de suscripción en Azure. Ofrece información acerca de quién creó, actualizó o eliminó los recursos y cuándo se produjeron estos eventos. Para más información, consulte, [Auditoría y recepción de notificaciones sobre las acciones importantes en su suscripción de Azure](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Pasos adicionales para las organizaciones que administran el acceso a otras aplicaciones en la nube a través de Azure AD 

#### <a name="configure-conditional-access-policies"></a>Configure directivas de acceso condicional

Prepare las directivas de acceso condicional para aplicaciones locales y hospedadas en la nube. Si los usuarios han unido dispositivos al área de trabajo, obtenga más información de [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fase 3: crear visibilidad y crear control total de la actividad de administración

![Fase 3](./media/admin-roles-best-practices/stage-three.png)

La fase 3 se basa en las mitigaciones de riesgos de la fase 2 y está diseñada para implementarse en aproximadamente 1 a 3 meses. Esta fase de la hoja de ruta del acceso con privilegios protegido incluye los siguientes componentes.

### <a name="general-preparation"></a>Preparación general

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Complete una revisión de acceso de los usuarios en roles de administrador

Un mayor número de usuarios corporativos obtienen acceso con privilegios mediante servicios en la nube, lo que puede llevar a una plataforma cada vez menos administrada. Aquí se incluyen los usuarios que se convierten en administradores globales de Office 365, los administradores de suscripciones de Azure y los usuarios que tienen acceso de administrador a las máquinas virtuales o a través de aplicaciones SaaS. En su lugar, en las organizaciones todos los empleados, especialmente los administradores, deberían realizar sus transacciones empresariales diarias como usuarios sin privilegios y solo asumir derechos de administrador cuando sea necesario. Dado que es posible que el número de usuarios con roles de administrador haya crecido desde la adopción inicial, realice revisiones de los accesos para identificar y confirmar todos los usuarios que pueden activar los privilegios de administrador. 

Haga lo siguiente:

* Determine qué usuarios son administradores de Azure AD, habilite el acceso de administrador Just-in-Time a petición y los controles de seguridad basados en rol.
* Convierta los usuarios que no tengan una justificación clara para el acceso de administrador con privilegios a otro rol (si ninguna de las funciones es apta, quítelas).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continúe con el lanzamiento de una autenticación más segura para todos los usuarios 

Solicite a los ejecutivos del máximo rango, directores de alto nivel, personal de seguridad e TI crítico y otros usuarios de alta exposición que tengan una autenticación moderna, segura, como Azure MFA o Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Use estaciones de trabajo dedicados para la administración de Azure AD

Los atacantes pueden intentar dirigirse a cuentas con privilegios para obtener acceso a los datos y sistemas de una organización, con el fin de deteriorar la integridad y la autenticidad de los datos mediante código malintencionado que modifica la lógica del programa o fisgonea al administrador cuando este escriba una credencial. Las estaciones de trabajo con privilegios de acceso (PAW) proporcionan un sistema operativo dedicado para tareas delicadas protegido contra ataques de Internet y vectores de amenazas. La separación de estas tareas y cuentas delicadas de las estaciones de trabajo y dispositivos de uso diario proporciona una protección muy eficaz contra ataques de suplantación de identidad (phishing), las vulnerabilidades de las aplicaciones o del sistema operativo, diversos ataques de suplantación y ataques de robo de credenciales, como registro de pulsaciones de teclas, y ataques pass-the-hash y pass-the-ticket. Mediante la implementación de estaciones de trabajo de acceso con privilegios, se puede reducir el riesgo de que los administradores escriban las credenciales de administrador, salvo en un entorno de escritorio que se ha protegido. Para más información, consulte [Estaciones de trabajo con privilegios de acceso](https://docs.microsoft.com/en-us/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Consulte las recomendaciones de control de incidentes que realiza el Instituto Nacional de Normas y Tecnología 

El Instituto Nacional de Normas y Tecnología (NIST) proporciona directrices para el control de incidentes, especialmente para analizar los datos relacionados con los incidentes y determinar la respuesta más apropiada ante cada incidente. Para más información, consulte [la guía para el control de incidentes de seguridad en los equipos del NIST (SP 800-61, revisión 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implemente Privileged Identity Management (PIM) para JIT en más roles administrativos

En el caso de Azure Active Directory, use la funcionalidad [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). La activación por tiempo limitado de roles con privilegios funciona mediante la habilitación para:

* Activar los privilegios de administrador para llevar a cabo una tarea específica
* Exigir MFA durante el proceso de activación
* Usar alertas para informar a los administradores acerca de los cambios fuera de banda
* Permitir a los usuarios conservar ciertos privilegios durante un período previamente configurado
* Permitir a los administradores de seguridad detectar todas las identidades con privilegios, ver informes de auditoría y crear revisiones de acceso para identificar todos los usuarios aptos para activar los privilegios de administrador

Si ya usa Azure AD Privileged Identity Management, ajuste los períodos de tiempo de los privilegios limitados en tiempo según sea necesario (por ejemplo, las ventanas de mantenimiento).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determine la exposición a protocolos de inicio de sesión mediante contraseña (si usa Exchange Online)

Antes, los protocolos asumían que las combinaciones de nombre de usuario y contraseña se insertaban en los dispositivos, las cuentas de correo electrónico, los teléfonos, etc. Pero ahora, con el riesgo de ciberataques en la nube, se recomienda identificar a todos los usuarios potenciales que podrían causar una catástrofe en la organización si sus credenciales corrieran peligro y no permitirles iniciar sesión en su correo electrónico con una combinación de nombre de usuario y contraseña mediante la implementación de requisitos de autenticación segura y acceso condicional. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Realice una valoración del examen de los roles de Office 365 (si usa Office 365)

Evalúe si todos los usuarios administradores están en los roles correctos (elimínelos y vuelva a asignarlos en función de esta evaluación).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Examine el enfoque de administración de incidentes de seguridad usado en Office 365 y compárelo con su propia organización

Este informe se puede descargar de [Security Incident Management in Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302) (Administración de incidentes de seguridad en Microsoft Office 365).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continúe con la protección de las cuentas administrativas con privilegios locales

Si Azure Active Directory está conectado con la instancia local de Active Directory, siga las instrucciones de [Hoja de ruta de la seguridad del acceso con privilegios guía básica de acceso con privilegios](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fase 2. Esto incluye implementar estaciones de trabajo de acceso con privilegios para todos los administradores, requerir MFA, usar Just Enough Admin para el mantenimiento de controladores de dominio, reducir la superficie expuesta al ataque de los dominios e implementar ATA para la detección de ataques.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Pasos adicionales para las organizaciones que administran el acceso a Azure

#### <a name="establish-integrated-monitoring"></a>Establezca la supervisión integrada

[Azure Security Center](../security-center/security-center-intro.md) proporciona funcionalidades de administración de directivas y supervisión de la seguridad integradas en sus suscripciones de Azure, ayuda a detectar las amenazas que pueden pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Realice un inventario de sus cuentas con privilegios en las máquinas virtuales hospedadas

En la mayoría de los casos, no es necesario conceder a los usuarios permisos sin restricciones a todas las suscripciones o recursos de Azure. Puede usar los roles de administrador de Azure AD para segregar las tareas en su organización y conceder únicamente la cantidad de acceso precisa a los usuarios que necesitan realizar tareas específicas. Por ejemplo, utilice los roles de administrador de Azure AD para permitir que un administrador administre solo las máquinas virtuales de una suscripción, mientras que otro pueda administrar las bases de datos SQL en la misma suscripción. Para más información, consulte [Introducción al control de acceso basado en roles en Azure Portal](../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implemente PIM para los roles de administrador de Azure AD

Use Privileged Identity Management con los roles de administrador de Azure AD para administrar, controlar y supervisar el acceso a los recursos de Azure. El uso de PIM protege las cuentas con privilegios frente a ciberataques mediante la reducción del tiempo de exposición de los privilegios y el aumento de la visibilidad de su uso mediante alertas e informes. Para más información, consulte [Administración del acceso a los recursos de Azure con Privileged Identity Management](../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Use las integraciones de registros de Azure para enviar los registros de Azure relevantes a los sistemas de SIEM 

Azure Log Integration permite integrar los registros sin procesar de los recursos de Azure en los sistemas de administración de eventos e información de seguridad (SIEM) existentes de la organización. [Azure Log Integration](../security/security-azure-log-integration-overview.md) recopila eventos de Windows de los registros del Visor de eventos de Windows y registros de Azure de los registros de actividad de Azure, las alertas de Azure Security Center y los registros de Azure Diagnostics. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Pasos adicionales para las organizaciones que administran el acceso a otras aplicaciones en la nube a través de Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implemente el aprovisionamiento de usuarios para las aplicaciones conectadas

Azure AD permite automatizar la creación, el mantenimiento y la eliminación de identidades de usuario en aplicaciones en la nube (SaaS) como Dropbox, Salesforce, ServiceNow, etc. Para más información, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Integre la protección de la información

MCAS permite investigar archivos y establecer directivas en función de las etiquetas de clasificación de Azure Information Protection, lo que permite tener mayor visibilidad y control de los datos en la nube. Analice y clasifique los archivos de la nube y aplique las etiquetas de Azure Information Protection. Para más información, consulte [Integración de Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configure el acceso condicional

Configure el acceso condicional en función del grupo, la ubicación y la confidencialidad de la aplicación para las aplicaciones [SaaS](https://azure.microsoft.com/overview/what-is-saas/) y las aplicaciones conectadas a Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Supervise la actividad en las aplicaciones en la nube conectadas

Para asegurarse de que el acceso de los usuarios también se protege en las aplicaciones conectadas, es aconsejable usar [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Esta solución protege el acceso de la empresa a las aplicaciones en la nube, además de proteger sus cuentas de administrador, ya que le permite:

* Ampliar la visibilidad y el control a las aplicaciones en la nube
* Crear directivas de acceso, actividades y uso compartido de datos
* Identificar automáticamente las actividades de riesgo, comportamientos anómalos y amenazas
* Evitar la pérdida de datos
* Minimizar el riesgo, realizar una prevención automática de las amenazas y exigir la aplicación de directivas

El agente de SIEM de Cloud App Security integra Cloud App Security en el servidor de SIEM para habilitar la supervisión centralizada de las actividades y alertas de Office 365. Se ejecuta en el servidor y extrae las alertas y actividades de Cloud App Security y las transmite por secuencias al servidor de SIEM. Para más información, consulte [Integración de SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fase 4: seguir creando defensas para tener una postura proactiva con respecto a la seguridad


![Fase 4](./media/admin-roles-best-practices/stage-four.png)

La fase 4 de la hoja de ruta se basa en la visibilidad de la fase 3 y está diseñada para implementarse en seis meses, o más. La finalización de la hoja de ruta le ayuda a desarrollar una mayor protección del acceso con privilegios frente a posibles ataques conocidos y disponibles actualmente. Lamentablemente, las amenazas de seguridad están en constante evolución, por lo que le recomendamos que vea la seguridad como un proceso continuo dirigido a elevar el costo y reducir la tasa de éxito de aquellos que dirigen sus ataques a su entorno.

La protección del acceso con privilegios es un primer paso fundamental para establecer garantías de seguridad para los recursos empresariales en una organización moderna, pero no es la única parte de un programa de seguridad completo que incluiría elementos, como directiva, operaciones, seguridad de la información, servidores, aplicaciones, equipos, dispositivos, tejido de nube y otros componentes que proporcionan garantías de seguridad continua. 

Además de administrar las cuentas de acceso con privilegios, es aconsejable que revise los siguientes elementos de forma regular:

* Asegúrese de que los administradores realizan sus tareas cotidianas como usuarios sin privilegios.
* Conceda acceso con privilegios solo cuando estrictamente necesario y revóquelo inmediatamente después (Just-In-Time).
* Conserve y revise toda la actividad de auditoría relacionada con las cuentas con privilegios.

Para más información acerca de cómo crear toda una hoja de ruta de seguridad, consulte [Recursos de arquitectura de TI de la nube de Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Para más información acerca de cómo interactuar con los servicios de Microsoft para que le ayuden con cualquiera de estos temas, póngase en contacto con su representante de Microsoft o consulte [Build critical cyber defenses to protect your enterprise](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx) (Creación de ciberdefensas críticas que protejan su empresa).

Esta fase final de la hoja de ruta del acceso con privilegios protegido incluye los siguientes componentes.

### <a name="general-preparation"></a>Preparación general

#### <a name="review-admin-roles-in-azure-active-directory"></a>Revise los roles de administración en Azure Active Directory 

Determine si los actuales roles de administrador de Azure AD integrados siguen estando actualizados y asegúrese de los usuarios están en los roles y las delegaciones que necesitan para los permisos correspondientes. Con Azure AD puede designar administradores independientes que desempeñen distintas funciones. Para más información, consulte [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Examine los usuarios que tengan la administración de dispositivos unidos a Azure AD

Para más información, consulte [Configuración de dispositivos híbridos unidos a Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Revise los miembros de los [roles de administrador de Office 365 integrados](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Si utiliza Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Valide el plan de respuesta ante incidentes

Para mejorar su plan, Microsoft recomienda validar periódicamente que funciona según lo esperado:

* Recorra la hoja de ruta existente para ver lo que falta
* En función del análisis post mortem, revise los procedimientos recomendados existentes o defina otros nuevos.
* Asegúrese de que tanto el plan de respuesta ante incidentes actualizado como los procedimientos recomendados se distribuyen por toda la organización


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Pasos adicionales para las organizaciones que administran el acceso a Azure 

Determine si necesita [transferir la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Romper el cristal": qué hacer en caso de emergencia

![Emergencia](./media/admin-roles-best-practices/emergency.jpeg)

1. Proporcione a los principales directivos y responsables de seguridad la información pertinente acerca del incidente.

2. Revise el cuaderno de estrategias para repeler ataques. 

3. Acceda a la combinación de nombre de usuario y contraseña de su cuenta "de emergencia" para iniciar sesión en Azure AD. 

4. Obtenga ayuda de Microsoft, para lo que debe [abrir una solicitud de soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md).

5. Examine los [informes de inicio de sesión de Azure AD](active-directory-reporting-azure-portal.md). Puede haber un retraso entre el momento en que se produce un evento y cuándo está incluido en el informe.

6. En el caso de los entornos híbridos, si está federado y el servidor de AD FS no está disponible, es posible que tenga que cambiar temporalmente de una autenticación federada a usar la sincronización del hash de contraseña. De esta forma se revierte la federación del dominio a la autenticación administrada hasta que el servidor de AD FS vuelva a estar disponible.

7. Supervisar el correo electrónico de las cuentas con privilegios.

8. Asegúrese de guardar copias de seguridad de los registros relevantes, por si es preciso realizar una investigación legal o forense.

Para más información acerca de la forma en que Microsoft Office 365 controla los incidentes de seguridad, consulte [Security Incident Management in Microsoft Office 365](http://aka.ms/Office365SIM) (Administración de incidentes de seguridad en Microsoft Office 365).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Preguntas más frecuentes: preguntas que recibimos habitualmente con respecto a la protección de acceso con privilegios  


**P:**  ¿Qué tengo hago si aún no he implementado ningún componente de acceso seguro?

**Respuesta:** Defina un mínimo de dos cuentas para casos de emergencia, asigne MFA a sus cuentas de administrador con privilegios y separe las cuentas de usuario de las cuentas de administrador global.


**P:**  Después de una infracción, ¿qué problema es el primero que se debe solucionar?

**Respuesta:** Asegúrese de que requiere la máxima autenticación a los usuarios con mayor exposición.


**P:** ¿Qué ocurre si se han desactivado nuestros administradores con privilegios?

**Respuesta:** Cree una cuenta de administrador global y manténgala siempre actualizada.


**P:** ¿Qué pasa si hay un solo administrador global y no se le puede localizar? 

**Respuesta:** Utilice una de las cuentas de emergencia para obtener acceso con privilegios de inmediato.


**P:** ¿Cómo puedo proteger a los administradores en mi organización?

**Respuesta:** Indique a los administradores que siempre deben realizar sus tareas cotidianas como usuarios estándar de "sin privilegios".
 

**P:** ¿Cuáles son los procedimientos recomendadas para la creación de cuentas de administrador en Azure AD?

**Respuesta:** Reserva el acceso con privilegios para tareas de administrador específicas.


**P:** ¿Qué herramientas existen para reducir el acceso de administrador constante?

**Respuesta:** Privileged Identity Management (PIM) y los roles de administrador de Azure AD.


**P:** ¿Cuál es la posición de Microsoft acerca de la sincronización de cuentas de administrador con Azure AD?

**Respuesta:** Las cuentas de administrador de nivel 0 (lo que incluye las cuentas, los grupos y otros recursos que tienen control administrativo directo o indirecto del bosque de AD, los dominios o los controladores de dominio y todos los recursos) se utilizan únicamente para las cuentas de AD locales y normalmente no se sincronizan para Azure AD para la nube. 


**P:** ¿Cómo se evita que los administradores asignen acceso de administrador aleatoriamente en el portal?

**Respuesta:** Use cuentas sin privilegios para todos los usuarios y la mayoría de los administradores. Para empezar, desarrolle una imagen de la organización para determinar qué cuentas de administrador deberían tener privilegios. Y supervise los usuarios administrativos recién creados.


## <a name="next-steps"></a>Pasos siguientes

* [Centro de confianza de Microsoft para la seguridad de los productos](https://www.microsoft.com/en-us/trustcenter/security): características de seguridad de los productos y servicios en la nube de Microsoft

* [Centro de confianza de Microsoft - Cumplimiento normativo](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings): conjunto completo de ofertas de cumplimiento de normas para los servicios en la nube de Microsoft

* [Guía para realizar una evaluación del riesgo](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment): administre la seguridad y los requisitos de cumplimiento de los servicios en la nube de Microsoft

### <a name="other-ms-online-services"></a>Otros servicios en línea de Microsoft 

* [Seguridad de Microsoft Intune](https://www.microsoft.com/en-us/trustcenter/security/intune-security): Intune proporciona funcionalidades de administración de dispositivos móviles, aplicaciones móviles y PC desde la nube.

* [Seguridad de Microsoft Dynamics 365](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security): Dynamics 365 es la solución en la nube de Microsoft que unifica la administración de relaciones con clientes (CRM) y las funcionalidades de planificación de recursos empresariales (ERP).

 
