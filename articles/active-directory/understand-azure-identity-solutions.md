---
title: "Información acerca de la identidad en Azure | Microsoft Docs"
description: "Obtenga un conocimiento básico de los términos, los conceptos y las recomendaciones de las soluciones de identidad de Microsoft Azure para tomar la mejor decisión de regulación de las identidades para la organización."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 4438917db93c37ddbba3e7ee692b2e3c065d2beb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="understand-azure-identity-solutions"></a>Información acerca de las soluciones de identidad de Azure
Microsoft Azure Active Directory (Azure AD) es una solución en la nube de administración de identidades y acceso que proporciona servicios de directorio, control de identidad y administración del acceso a las aplicaciones. Azure AD [habilita rápidamente el inicio de sesión único (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) en miles de aplicaciones comerciales y personalizadas preintegradas de la [galería de aplicaciones de Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/). Probablemente ya use muchas de estas aplicaciones, como Office 365, Salesforce.com, Box, ServiceNow y Workday.

Un único directorio de Azure AD se asocia automáticamente con una suscripción de Azure cuando se crea. En cuanto servicio de identidad de Azure, Azure AD proporciona todas las funciones de administración de identidades y control de acceso para los recursos basados en la nube. Estos recursos pueden incluir usuarios, aplicaciones y grupos para un inquilino individual (organización), tal como se muestra en el diagrama siguiente:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure ofrece varias maneras de aprovechar la identidad como un servicio (IDaaS) con diferentes niveles de complejidad para satisfacer las necesidades individuales de la organización. El resto de este artículo le ayuda a comprender la terminología y los conceptos básicos de identidad de Azure. Además, se incluyen recomendaciones para que pueda elegir la mejor opción de entre todas las disponibles.

## <a name="terms-to-know"></a>Términos que debe conocer

Antes de poder tomar una decisión sobre una solución de identidad de Azure, debe tener un conocimiento básico de los términos usados habitualmente al hablar sobre los servicios de identidad de Azure.

|Término| Descripción|
|-----|-----|
|Suscripción de Azure |Las suscripciones se usan para pagar los servicios en la nube de Azure y normalmente están vinculadas a una tarjeta de crédito. Puede tener varias suscripciones, pero puede ser difícil compartir recursos entre suscripciones.|
|Inquilino de Azure | Un inquilino de Azure AD es representativo de una sola organización. Es una instancia dedicada y de confianza de Azure AD que se crear automáticamente cuando una organización se suscribe a un servicio en la nube, como Azure, Intune u Office 365. Los inquilinos pueden obtener acceso a servicios en un entorno dedicado (un solo inquilino) o en un entorno compartido con otras organizaciones (multiinquilino).|
|Directorio de Azure AD | Cada inquilino de Azure tiene un directorio de Azure AD dedicado y de confianza que contiene los usuarios, grupos y aplicaciones del inquilino. Sirve para realizar funciones de administración de identidad y acceso a los recursos del inquilino. Dado que un único directorio de Azure AD se aprovisiona automáticamente para representar su organización al suscribirse a un servicio en la nube de Microsoft como Office 365, Microsoft Intune o Azure, a veces, verá los términos *inquilino*, *Azure AD* y *directorio de Azure AD* utilizados indistintamente. |
|Dominio personalizado | La primera vez que se suscribe a un servicio en la nube de Microsoft, el inquilino (organización) usa un nombre de dominio *.onmicrosoft.com*. Sin embargo, la mayoría de las organizaciones tienen uno o más nombres de dominio que usan para hacer negocios y que los usuarios finales utilizan para acceder a los recursos de la empresa. Puede agregar su nombre de dominio personalizado a Azure AD para que el nombre de dominio resulte familiar a los usuarios, como *alice@contoso.com* en lugar de *alice@contoso.onmicrosoft.com*. |
|Cuenta de Azure AD | Se trata de identidades que se crean mediante Azure AD u otro servicio en la nube de Microsoft como Office 365. Se almacenan en Azure AD y son accesibles a cualquiera de las suscripciones de servicio en la nube de la organización. |
|Administrador de suscripciones de Azure| El administrador de cuenta es la persona que se inscribió o que adquirió la suscripción de Azure. Tiene autorización para usar el [Centro de cuentas](https://account.azure.com/Subscriptions) y realizar diversas tareas de administración como crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción o cambiar el administrador de servicios. |
|Administrador global de Azure AD | Los administradores globales de Azure AD tienen acceso total a todas las funciones administrativas de Azure AD. La persona que se suscribe a un servicio en la nube de Microsoft se convierte automáticamente en administrador global de forma predeterminada. Puede tener más de un administrador global, pero solo los administradores globales pueden asignar cualquiera de [los otros roles de administrador](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a los usuarios. |
|Cuenta Microsoft | Las cuentas Microsoft (creadas para su uso personal) proporcionan acceso a los productos y servicios en la nube de Microsoft orientados al cliente, como Outlook (Hotmail), OneDrive, Xbox LIVE u Office 365. Estas identidades se crean y almacenan en el sistema de cuentas de identidad de consumidor de Microsoft ejecutado por Microsoft.|
|Cuentas profesionales o educativas | Las cuentas profesionales o educativas (generadas por un administrador para uso empresarial o académico) proporcionan acceso a los servicios en la nube de Microsoft de nivel empresarial, como Azure, Intune u Office 365.|


## <a name="concepts-to-understand"></a>Conceptos que debe conocer

Ahora que conoce los términos básicos de la identidad de Azure, debería aprender más sobre ellos, ya que le ayudará a tomar una decisión informada sobre los servicios de identidad de Azure.

|Concepto |Descripción|
|-----|-----|
|[Asociación de las suscripciones de Azure con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Cada suscripción de Azure tiene una relación de confianza con un directorio de Azure AD para autenticar usuarios, servicios y dispositivos. *Varias suscripciones pueden confiar en el mismo directorio de Azure AD, pero una suscripción solo confiará en un único directorio de Azure AD*. Esta relación de confianza es diferente que la relación que tiene una suscripción con otros recursos de Azure (sitio web, bases de datos, etc.), que son más parecidos a los recursos secundarios de una suscripción. Si una suscripción expira, también se detiene el acceso a los recursos asociados a la suscripción distintos a Azure AD. Sin embargo, el directorio de Azure AD permanece en Azure, para que pueda asociar otras suscripciones a ese directorio y continuar con la administración de los recursos del inquilino.|
|[Cómo funcionan las licencias de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Cuando compra o activa Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, el directorio se actualiza con la suscripción, incluido el período de validez y las licencias de prepago. Una vez activa la suscripción, los administradores globales de Azure AD pueden administrar el servicio y los usuarios con licencia pueden usarlo. La información de suscripción, incluido el número de licencias asignadas o disponibles, se encuentra en Azure Portal en la hoja **Azure Active Directory** > **Licencias**. Es también el mejor lugar para administrar de las asignaciones de licencia.|
|[Control de acceso basado en roles en Azure Portal](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|El control de acceso basado en roles (RBAC) de Azure ayuda a proporcionar una administración de acceso detallada de los recursos de Azure. Un número elevado de permisos puede provocar que la cuenta esté expuesta a los atacantes. Si se conceden muy pocos, los empleados no podrán realizar su trabajo de manera eficaz. Con RBAC, puede dar a los empleados los permisos exactos que necesitan en función de los tres roles básicos que se aplican a todos los grupos de recursos: propietario, colaborador, lector. También puede crear hasta 2000 [roles personalizados de RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) propios para satisfacer sus necesidades específicas. |
|[Identidad híbrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|La identidad híbrida se consigue mediante la integración de su Windows Server Active Directory (AD DS) local con directorio de Azure AD mediante [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Esto le permite proporcionar una identidad común para los usuarios de aplicaciones de Office 365, Azure y aplicaciones locales o SaaS integradas con Azure AD. Con la identidad híbrida, su entorno local se extiende de una manera eficaz a la nube para identidad y acceso.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Diferencia entre Windows Server AD DS y Azure AD
Azure Active Directory (Azure AD) y Active Directory local (Active Directory Domain Services o AD DS) son los sistemas que almacenan datos de directorios y administran la comunicación entre los usuarios y los recursos, incluidos los procesos de inicio de sesión de usuario, la autenticación y las búsquedas de directorios.

Si ya está familiarizado con Windows Server Active Directory Domain Services (AD DS) local, presentado por primera vez con Windows 2000 Server, es probable que ya conozca el concepto básico de un servicio de identidad. Sin embargo, también es importante comprender que Azure AD no es solo un controlador de dominio en la nube. Es una forma totalmente nueva de proporcionar identidad como servicio (IDaaS) en Azure, que requiere una forma totalmente nueva de pensamiento para adoptar completamente las funcionalidades en la nube y proteger su organización frente a amenazas modernas. 

AD DS es un rol del servidor en Windows Server, lo que significa que se pueden implementar en máquinas físicas o virtuales. Tiene una estructura jerárquica basada en X.500. Utiliza DNS para localizar objetos, se puede interactuar usando LDAP y principalmente usa Kerberos para la autenticación. Active Directory habilita las unidades organizativas (OU) y los objetos de directiva de grupo (GPO), además de unir máquinas al dominio, y se crean confianzas entre dominios.

Los departamentos de TI han protegido el perímetro de seguridad durante años con AD DS, pero las empresas modernas, sin perímetro definido y con necesidades de identidad para empleados, clientes y asociados requieren un plano de control nuevo. Azure AD es ese plano de control de identidad. La seguridad se ha trasladado más allá del firewall corporativo a la nube, donde Azure AD protege los recursos y el acceso de la empresa al proporcionar una identidad común para los usuarios (tanto local como en la nube). Esto ofrece a los usuarios flexibilidad para acceder de forma segura a las aplicaciones que necesitan para realizar su trabajo desde casi cualquier dispositivo. También se proporcionan controles continuos de protección de datos basados en riesgo, respaldados por funcionalidades de aprendizaje automático e informes exhaustivos, que el departamento de TI necesita para proteger los datos de la empresa.

Azure AD es un servicio de directorio público de varios clientes, lo que significa que en Azure AD puede crear un inquilino para los servidores de la nube y aplicaciones como Office 365. Los usuarios y los grupos se crean en una estructura plana sin unidades organizativas ni objetos de directiva de grupo. La autenticación se realiza mediante protocolos como SAML, WS-Federation y OAuth. Es posible consultar Azure AD, pero en lugar de usar LDAP debe usar una API de REST denominada API Graph de AD. Todos funcionan mediante HTTP y HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Ampliar las funcionalidades de seguridad y administración de Office 365
¿Ya usa Office 365? Acelere la transformación digital mediante la extensión de las funcionalidades integradas de Office 365 con Azure AD para proteger todos los recursos y permitir la productividad segura de todos los recursos. Cuando usa Azure AD, además de las funcionalidades de Office 365, puede proteger toda su cartera de aplicaciones con una identidad que permite el inicio de sesión único en todas las aplicaciones. Puede expandir las funcionalidades de acceso condicional en función no solo del estado del dispositivo, sino también del usuario, la ubicación, la aplicación y el riesgo. Las funcionalidades de autenticación multifactor (MFA) proporcionan aún mayor protección cuando lo necesite. Podrá obtener una supervisión adicional de los privilegios de los usuarios y proporcionar acceso administrativo bajo petición, en el momento preciso. Los usuarios serán más productivos y se crearán menos incidencias de soporte técnico, gracias a las funcionalidades de autoservicio que proporciona Azure AD, como el restablecimiento de contraseñas olvidadas, las solicitudes de acceso a las aplicaciones y la creación y administración de grupos.

> [!TIP]
> ¿Desea más información acerca del uso de la administración de identidades de Azure AD con Office 365? [Obtener el libro electrónico](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Soluciones de identidad de Microsoft Azure

Microsoft Azure ofrece varias maneras de administrar las identidades de los usuarios tanto si se mantienen solo local, solo en la nube o incluso en algún punto intermedio. Estas opciones incluyen: AD DS de implementación personal en Azure, Azure Active Directory (Azure AD), identidad híbrida y Azure AD Domain Services.

### <a name="do-it-yourself-diy-ad-ds"></a>AD DS de implementación personal
Las empresas que necesitan solo una pequeña superficie en la nube pueden utilizar **AD DS de implementación personal** en Azure. Esta opción admite muchos escenarios de Windows Server AD DS que son adecuados para la implementación como máquinas virtuales (VM) en Azure. Por ejemplo, puede crear una máquina virtual de Azure como un controlador de dominio que se ejecuta en un centro de datos lejano que está conectado a la red remota. Desde allí, la máquina virtual sería capaz de admitir las solicitudes de autenticación de los usuarios remotos y mejorar el rendimiento de la autenticación. Esta opción también resulta muy indicada como sustituto de costo relativamente bajo frente a situaciones de recuperación ante desastres de otro modo costosas, al hospedar un pequeño número de controladores de dominio y una única red virtual en Azure. Por último, puede que necesite implementar una aplicación en Azure, como SharePoint, que requiere Windows Server AD DS, pero no tiene ninguna dependencia de la red local ni de la instancia de Windows Server Active Directory corporativo. En este caso, podría implementar un bosque aislado en Azure para cumplir los requisitos de la granja de servidores de SharePoint. También se admite la implementación de aplicaciones de red que requieren conectividad con la red local y con la instancia local de Active Directory.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
**Azure AD independiente** es una solución de identidad y administración de acceso como servicio (IDaaS) basada completamente en la nube. Azure AD ofrece un conjunto eficaz de funcionalidades para administrar usuarios y grupos. Ayuda a proteger el acceso a aplicaciones locales y en la nube, que incluyen servicios web de Microsoft como Office 365 y muchas aplicaciones de software como servicio (SaaS) que no son de Microsoft. Azure AD está disponible en tres ediciones: Gratis, Básico y Premium. Azure AD aumenta la eficacia de la organización y amplía la seguridad más allá del firewall perimetral a un nuevo plano de control protegido por Azure Machine Learning y otras características de seguridad avanzadas.

### <a name="hybrid-identity"></a>Identidad híbrida
En lugar de elegir entre soluciones de identidad basadas en la nube o locales, muchos CIO y empresas avanzadas, que han anticipado la dirección a largo plazo de la empresa, están ampliando sus directorios locales a la nube mediante soluciones de **identidad híbrida**. Con la identidad híbrida, se obtiene una solución de administración de identidades y acceso realmente global que proporciona acceso protegido y productivo a las aplicaciones que los usuarios necesitan para realizar su trabajo.

> [!TIP]
> Para más información acerca de cómo los CIO han hecho de Azure Active Directory una pieza fundamental de sus estrategias de TI, descargue la [Guía para el CIO de Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services
**Azure AD Domain Services** ofrece una opción en la nube para usar AD DS para el control ligero de la configuración de máquina virtual de Azure, así como una manera de cumplir requisitos de identidad local para el desarrollo y prueba de las aplicaciones. Azure AD Domain Services no está pensado para trasladar su infraestructura AD DS local a máquinas virtuales de Azure administradas por Azure AD Domain Services. En su lugar, las máquinas virtuales de Azure en dominios administrados deberían usarse para permitir el desarrollo, la prueba y la migración de aplicaciones locales que requieren métodos de autenticación de AD DS a la nube.

## <a name="common-scenarios-and-recommendations"></a>Escenarios comunes y recomendaciones

Estos son algunos escenarios comunes de identidad y acceso con recomendaciones sobre la opción de identidad de Azure que podría ser más adecuada.

|Escenario de identidad| Recomendación|
|-----|-----|
|La organización ha realizado grandes inversiones en Windows Server Active Directory locales, pero desea extender la identidad a la nube.| La solución de identidad de Azure más utilizada es la [identidad híbrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Si ya ha realizado inversiones en AD DS locales, puede extender fácilmente la identidad a la nube mediante Azure AD Connect.|
|La empresa ha surgido en la nube y no hay inversiones en soluciones de identidad locales.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es la mejor opción para las empresas solo en la nube que no tienen ninguna inversión en instancias locales.|
|Hace falta un control ligero de la configuración de las máquinas virtuales de Azure para satisfacer los requisitos de identidad local para el desarrollo y la prueba de aplicaciones.|[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) es una buena opción si tiene que usar AD DS para un control ligero de la configuración de las máquinas virtuales de Azure, o si desea desarrollar o migrar a la nube aplicaciones locales heredadas que utilizan el directorio.|  
|Hace falta administrar algunas máquinas virtuales en Azure, pero la empresa aún tiene inversiones fuertes en instancias locales de Active Directory (AD DS).|Use [AD DS de implementación personal](https://msdn.microsoft.com/library/azure/jj156090.aspx) para usar máquinas virtuales de Azure cuando necesite admitir algunas máquinas virtuales y tenga grandes inversiones en AD DS local. |

## <a name="where-can-i-learn-more"></a>¿Dónde puedo obtener más información?
Tenemos una gran cantidad de excelentes recursos en línea para ayudarle a aprender todo sobre Azure AD. Aquí se proporciona una lista de artículos excelentes para comenzar:

* [Habilitación del directorio para la administración híbrida con Azure AD Connect](active-directory-aadconnect.md)
* [Seguridad adicional en un mundo conectado permanentemente](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Introducción a los informes de Azure AD](active-directory-reporting-getting-started.md)
* [Administración de contraseñas desde cualquier lugar](active-directory-passwords-update-your-own-password.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)
* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [¿Qué es la licencia de Microsoft Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Pasos siguientes

Ahora que conoce los conceptos de identidad de Azure y las opciones disponibles, puede utilizar los siguientes recursos para comenzar la implementación de la opción elegida:

[Más información sobre las soluciones de identidad híbridas de Azure](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[Información sobre el entorno de prueba de concepto de Azure](https://aka.ms/aad-poc)

[Implementación de Azure AD en producción](https://aka.ms/aad-onboard)
