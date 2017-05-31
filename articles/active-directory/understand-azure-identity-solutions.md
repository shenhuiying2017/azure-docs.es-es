---
title: "Información acerca de la identidad en Azure | Microsoft Docs"
description: "Obtenga un conocimiento básico de los términos, los conceptos y las recomendaciones de las soluciones de identidad de Microsoft Azure para tomar la mejor decisión de regulación de las identidades para la organización."
keywords: 
author: jeffgilb
manager: femila
ms.date: 5/12/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 8d550a9c2a6d2f28687aefc3b9db97ca3c68eb82
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="understand-azure-identity-solutions"></a>Información acerca de las soluciones de identidad de Azure
Microsoft Azure Active Directory (Azure AD) es una solución en la nube de administración de acceso e identidades completa y de alta disponibilidad, que combina una administración de acceso a aplicaciones, gobierno de identidades avanzado y servicios de directorio fundamentales. Azure AD [habilita el inicio de sesión único (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) rápidamente a miles de aplicaciones comerciales y personalizadas previamente integradas en la [Galería de aplicaciones de Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), incluidas las que se utilizan a diario, como Office 365, Salesforce.com, Box, ServiceNow y Workday.

En su forma más simple, la identidad de Azure la proporciona un único directorio de Azure AD, que se asocia automáticamente con una suscripción de Azure cuando se crea. Como el servicio de identidad de Azure, Azure AD proporciona toda la administración de identidad y las funciones de control de acceso para los recursos en la nube, como usuarios, aplicaciones y grupos, para un inquilino individual (organización), tal como se muestra en el diagrama siguiente:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure ofrece varias maneras de aprovechar la identidad como un servicio (IDaaS) con diferentes niveles de complejidad para satisfacer las necesidades individuales de la organización. El resto de este artículo le ayudará a comprender la terminología y los conceptos que debe conocer, así como recomendaciones para que pueda elegir la mejor opción de identidad de Azure disponible.

## <a name="terms-to-know"></a>Términos que debe conocer

Antes de que pueda tomar una decisión de solución de servicio de identidad de Azure para su organización, debe tener un conocimiento básico de los términos usados habitualmente al hablar sobre los servicios de identidad de Azure.

|Término| Descripción|
|-----|-----|
|Suscripción de Azure |Las suscripciones se usan para pagar los servicios en la nube de Azure y normalmente están vinculadas a una tarjeta de crédito. Puede tener varias suscripciones, pero puede ser difícil compartir recursos entre suscripciones.|
|Inquilino de Azure | Un inquilino de Azure AD es representativo de una sola organización. Es una instancia dedicada y de confianza de Azure AD que una organización recibe y posee cuando se suscribe a un servicio en la nube de Microsoft como Office 365, Intune o Azure. Los inquilinos pueden obtener acceso a servicios en un entorno dedicado (un solo inquilino) o en un entorno compartido con otras organizaciones (multiinquilino).|
|Directorio de Azure AD | Cada inquilino de Azure tiene un directorio de Azure AD dedicado y de confianza que contiene los usuarios, grupos y aplicaciones del inquilino. Sirve para realizar funciones de administración de identidad y acceso a los recursos del inquilino. Dado que un único directorio de Azure AD se aprovisiona automáticamente para representar su organización al suscribirse a un servicio en la nube de Microsoft como Office 365, Microsoft Intune o Azure, a veces, verá los términos *inquilino*, *Azure AD* y *directorio de Azure AD* utilizados indistintamente. |
|Dominio personalizado | La primera vez que se suscribe a un servicio en la nube de Microsoft, el inquilino (organización) crea un nombre de dominio *.onmicrosoft.com*. Sin embargo, la mayoría de las organizaciones tienen uno o más nombres de dominio que usan para hacer negocios y que los usuarios finales utilizan para tener acceso a los recursos de la empresa. Puede agregar su nombre de dominio personalizado a Azure AD para que el nombre de dominio resulte familiar a los usuarios, como *alice@contoso.com* en lugar de *alice@contoso.onmicrosoft.com*. |
|Cuenta de Azure AD | Se trata de identidades que se crean mediante Azure AD u otro servicio en la nube de Microsoft como Office 365. Se almacenan en un directorio Azure AD del inquilino y son accesibles a cualquiera de las suscripciones de servicio en la nube de la organización. |
|Administrador de suscripciones de Azure| El administrador de cuenta es la persona que se inscribió o que adquirió la suscripción de Azure. Están autorizados para tener acceso al [Centro de cuentas](https://account.windowsazure.com/Home/Index) y realizar diversas tareas de administración como crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción o cambiar el administrador de servicios. |
|Administrador global de Azure AD | Los administradores globales de Azure AD tienen acceso total a todas las funciones administrativas de Azure AD. La persona que se suscribe a un servicio en la nube de Microsoft se convierte automáticamente en administrador global de forma predeterminada. Puede tener más de un administrador global, pero solo los administradores globales pueden asignar cualquiera de [los otros roles de administrador](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a los usuarios. |
|Cuenta Microsoft | Las cuentas Microsoft (creadas para su uso personal) proporcionan acceso a todos los productos y servicios en la nube de Microsoft orientados al cliente, como Outlook (Hotmail), Messenger, OneDrive, MSN, Xbox LIVE u Office 365. Estas identidades se crean y almacenan en el sistema de cuentas de identidad de consumidor de Microsoft ejecutado por Microsoft.|
|Cuentas profesionales o educativas | Las cuentas profesionales o educativas (generadas por un administrador para uso empresarial o académico) proporcionan acceso a los servicios en la nube de Microsoft de nivel empresarial, como Azure, Intune u Office 365.|


## <a name="concepts-to-understand"></a>Conceptos que debe conocer

Ahora que conoce la terminología básica de identidad, es preciso ser consciente de los siguientes conceptos fundamentales que le ayudarán a tomar una decisión de servicio de identidad de Azure informada.

|Concepto |Descripción|
|-----|-----|
|[Asociación de las suscripciones de Azure con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Cada suscripción de Azure tiene una relación de confianza con un directorio de Azure AD para autenticar usuarios, servicios y dispositivos. *Varias suscripciones pueden confiar en el mismo directorio de Azure AD, pero una suscripción solo confía en un único directorio de Azure AD*. Esta relación de confianza es diferente que la relación que tiene una suscripción con otros recursos de Azure (sitio web, bases de datos, etc.), que son más parecidos a los recursos secundarios de una suscripción. Si una suscripción expira, también se detiene el acceso a los recursos asociados a la suscripción distintos a Azure AD. Sin embargo, el directorio de Azure AD permanece en Azure, para que pueda asociar otras suscripciones a ese directorio y continuar con la administración de los recursos del inquilino.|
|[Cómo funcionan las licencias de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Cuando compra o activa Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, el directorio se actualiza con la suscripción, incluido el período de validez y las licencias de prepago. Una vez activa la suscripción, los administradores globales de Azure AD pueden gestionar las funcionalidades del servicio y los usuarios con licencia pueden usarlas. La información de suscripción, incluido el número de licencias asignadas o disponibles, se encuentra en Azure Portal en la hoja **Azure Active Directory** > **Licencias**. Es también el mejor lugar para administrar de las asignaciones de licencia.|
|[Control de acceso basado en roles en Azure Portal](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|El control de acceso basado en roles (RBAC) de Azure ayuda a proporcionar una administración de acceso detallada de los recursos de Azure. Un número elevado de permisos puede provocar que la cuenta esté expuesta a los atacantes. Si se conceden muy pocos, los empleados no podrán realizar su trabajo de manera eficaz. Con RBAC, puede dar a los empleados los permisos exactos que necesitan en función de los tres roles básicos que se aplican a todos los grupos de recursos: propietario, colaborador, lector. También puede crear hasta 2000 [roles personalizados de RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) propios para satisfacer sus necesidades específicas. |
|[Identidad híbrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|La identidad híbrida se consigue mediante la integración de su Windows Server Active Directory (AD DS) local con directorio de Azure AD mediante [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Esto le permite proporcionar una identidad común para los usuarios de aplicaciones de Office 365, Azure y aplicaciones locales o SaaS integradas con Azure AD. Con la identidad híbrida, su entorno local se extiende de una manera eficaz a la nube para identidad y acceso.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Diferencia entre Windows Server AD DS y Azure AD
Si ya está familiarizado con Windows Server Active Directory Domain Services (AD DS) local, presentado por primera vez con Windows 2000 Server, es probable que ya conozca el concepto básico de un servicio de identidad. Sin embargo, también es importante comprender que Azure AD no es solo un controlador de dominio en la nube; es una forma totalmente nueva de proporcionar identidad como un servicio (IDaaS) en Azure, que requiere una forma totalmente nueva de pensamiento para adoptar completamente las funcionalidades en la nube y proteger su organización frente a amenazas modernas. Los departamentos de TI han protegido el perímetro de seguridad durante años con AD DS, pero las empresas modernas, sin perímetro definido y con necesidades de identidad para empleados, clientes y asociados requieren un plano de control nuevo. Azure AD es ese plano de control de identidad.

La seguridad se ha trasladado más allá del firewall corporativo a la nube, donde Azure AD protege los recursos y el acceso de la empresa al proporcionar una identidad común para los usuarios (tanto local como en la nube). Esto proporciona a los usuarios la flexibilidad necesaria para tener acceso seguro a las aplicaciones que necesitan para realizar su trabajo desde casi cualquier dispositivo y proporciona controles de protección de datos en función del riesgo, apoyado en funcionalidades de aprendizaje automático y los informes en profundidad que el departamento de TI necesita para mantener seguros los datos de la empresa.

### <a name="extend-office-365-management-and-security-capabilities"></a>Ampliar las funcionalidades de seguridad y administración de Office 365
¿Ya usa Office 365? Puede acelerar la transformación digital mediante la extensión de las funcionalidades de administración y seguridad integradas de Office 365 con Azure AD para proteger el acceso a todos los recursos y permitir y asegurar la productividad de toda la fuerza de trabajo. Cuando usa las funcionalidades de Azure AD, además de las proporcionadas por Office 365, puede proteger toda su cartera de aplicaciones con una identidad que habilita el inicio de sesión único para todas las aplicaciones. Puede expandir las funcionalidades de acceso condicional en función no solo del estado del dispositivo, sino también del usuario, la ubicación, la aplicación y el riesgo. Las funcionalidades de autenticación multifactor (MFA) proporcionan aún mayor protección cuando lo necesite. Podrá obtener una supervisión adicional de los privilegios de los usuarios y proporcionar acceso administrativo bajo petición, en el momento preciso. Los usuarios serán más productivos y se crearán menos tickets de soporte, gracias a las funcionalidades de autoservicio que proporciona Azure AD, como el restablecimiento de contraseñas olvidadas, las solicitudes de acceso de aplicación y la creación y administración de grupos.

> [!TIP]
> ¿Desea más información acerca del uso de la administración de identidades de Azure AD con Office 365? [Obtener el libro electrónico](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-identity-solutions"></a>Soluciones de identidad de Microsoft

Microsoft Azure ofrece varias maneras de administrar las identidades de los usuarios tanto si se mantienen solo local, solo en la nube o incluso en algún punto intermedio. Estas opciones incluyen: AD DS de implementación personal en Azure, Azure Active Directory (Azure AD), identidad híbrida y Azure AD Domain Services.

Las empresas que necesitan solo una pequeña superficie en la nube pueden utilizar **AD DS de implementación personal** en Azure. Esta opción admite muchos escenarios de Windows Server AD DS que son adecuados para la implementación como máquinas virtuales (VM) en Azure. Por ejemplo, puede crear una máquina virtual de Azure como un controlador de dominio que se ejecuta en un centro de datos lejano que está conectado a la red remota. Desde allí, la máquina virtual sería capaz de admitir las solicitudes de autenticación de los usuarios remotos y mejorar el rendimiento de la autenticación. Esta opción también resulta muy indicada como sustituto de costo relativamente bajo frente a situaciones de recuperación ante desastres de otro modo costosas, al hospedar un pequeño número de controladores de dominio y una única red virtual en Azure. Por último, puede que desee implementar una aplicación en Azure, como SharePoint, que requiere Windows Server AD DS pero no tiene ninguna dependencia de la red local ni del Windows Server Active Directory corporativo. En este caso, podría implementar un bosque aislado en Azure para cumplir los requisitos de la granja de servidores de SharePoint. También se admite la implementación de aplicaciones de red que requieren conectividad con la red local y con la instancia local de Active Directory.

**Azure Active Directory (Azure AD)** independiente es una solución de identidad y administración de acceso como servicio (IDaaS) basada completamente en la nube. Azure AD ofrece un conjunto eficaz de funcionalidades para administrar usuarios y grupos. Ayuda a proteger el acceso a aplicaciones locales y en la nube, que incluyen servicios web de Microsoft como Office 365 y muchas aplicaciones de software como servicio (SaaS) que no son de Microsoft. Azure AD está disponible en tres ediciones: Gratis, Básico y Premium. Azure AD aumenta la eficacia de la organización y amplía la seguridad más allá del firewall perimetral a un nuevo plano de control protegido por Azure Machine Learning y otras características de seguridad avanzadas.

En lugar de elegir entre soluciones de identidad basadas en la nube o locales, muchos CIO y empresas avanzadas, que han anticipado la dirección a largo plazo de la empresa, están ampliando sus directorios locales a la nube mediante soluciones de **identidad híbrida**. Con la identidad híbrida, se obtiene una solución de identidad y administración de acceso realmente global que proporciona acceso protegido y productivo a las aplicaciones que los usuarios necesitan para realizar su trabajo, tanto ahora como en el futuro.

> [!TIP]
> Para más información acerca de cómo los CIO han hecho de Azure Active Directory una pieza fundamental de sus estrategias de TI, descargue la [Guía para el CIO de Azure Active Directory](https://aka.ms/AzureADCIOGuide).

Por último, **Azure AD Domain Services** ofrece una opción en la nube para aprovechar AD DS para un control muy ligero de la configuración de la máquina virtual de Azure, así como una manera de cumplir requisitos de identidad local para desarrollo de aplicaciones de red y pruebas. Azure AD Domain Services no está pensado para trasladar su infraestructura AD DS local a máquinas virtuales de Azure administradas por Azure AD Domain Services. En su lugar, las máquinas virtuales de Azure en dominios administrados deberían utilizarse para permitir el desarrollo, pruebas y migración de aplicaciones locales que requieren métodos de autenticación AD DS a la nube.

## <a name="common-scenarios-and-recommendations"></a>Escenarios comunes y recomendaciones

Estos son algunos escenarios comunes de identidad y acceso con recomendaciones sobre la opción de identidad de Azure que podría ser más adecuada.

  |Escenario de identidad| Recomendación|
  |-----|-----|
  |Se necesita administrar un pequeño número de máquinas virtuales en Azure, pero la empresa aún tiene inversiones fuertes en instancias locales de Active Directory (AD DS).|Utilice [AD DS de implementación personal](https://msdn.microsoft.com/library/azure/jj156090.aspx) para utilizar máquinas virtuales de Azure cuando necesite admitir un número mínimo de máquinas virtuales y tiene grandes inversiones en AD DS local. |
  |La empresa ha surgido en la nube y no hay inversiones en soluciones de identidad locales.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es la mejor opción para las empresas solo en la nube que no tienen ninguna inversión en instancias locales.|
  |La organización ha realizado grandes inversiones en Windows Server Active Directory locales, pero desea extender la identidad a la nube.| La solución de identidad de Azure más utilizada es la [identidad híbrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Si ya ha realizado inversiones en AD DS locales, puede extender fácilmente la identidad a la nube mediante Azure AD Connect.|
  |Se necesitan una configuración y un control de máquinas virtuales de Azure muy ligeros para cumplir los requisitos de identidad locales para el desarrollo de aplicaciones y pruebas.|[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) es una buena opción si tiene que usar AD DS para un control muy ligero de la configuración de las máquinas virtuales de Azure, o si desea desarrollar o migrar a la nube aplicaciones locales heredadas que utilizan el directorio.|

## <a name="next-steps"></a>Pasos siguientes

Ahora que conoce los conceptos de identidad de Azure y las opciones disponibles, puede utilizar los siguientes recursos para comenzar la implementación de la opción elegida:

[Comprobar su conocimiento sobre las soluciones de identidad de Azure](https://aka.ms/aad-understand-quiz)

[Información sobre el entorno de prueba de concepto de Azure](https://aka.ms/aad-poc)

[Implementación de Azure AD en producción](https://aka.ms/aad-onboard)

