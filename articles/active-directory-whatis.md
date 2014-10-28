<properties linkid="manage-services-identity-what-is-windows-azure-AD" urlDisplayName="What is Azure AD?" pageTitle="What is Azure Active Directory?" metaKeywords="" description="Use Azure Active Directory to extend your existing on-premises identities into the cloud for an improved admin and end user experience while Microsoft keeps Active Directory running in the cloud with high scale, high availability, and integrated disaster recovery. Or, develop Azure AD integrated applications for your organization or for other organizations." metaCanonical="" services="active-directory" documentationCenter="" title="What is Azure Active Directory?" authors="justinha" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="justinha"></tags>

# ¿Qué es Azure Active Directory?

Azure Active Directory es un servicio que proporciona funcionalidades de administración de identidad y acceso en la nube. En un proceso muy similar al que sigue Active Directory para permitir a los clientes administrar identidades en entornos locales a través del sistema operativo Windows Server, Azure Active Directory (Azure AD) es un servicio disponible a través de Azure para administrar identidades en la nube. [Más información][]

Debido a que es el directorio en la nube de su organización, usted es quien decide quiénes son sus usuarios, qué información desea mantener en la nube, quién puede usar la información o administrarla y qué aplicaciones o servicios pueden tener acceso a esa información.

Al usar Azure AD, es responsabilidad de Microsoft mantener a Active Directory en funcionamiento en la nube con una alta escala, alta disponibilidad y recuperación ante desastres integrada, mientras respeta cabalmente sus requisitos de privacidad y seguridad de la información de su organización.

### Integración con Active Directory de un entorno local

Azure AD se puede usar como un directorio de la nube independiente para su organización; sin embargo, también puede integrar Active Directory existente en un entorno local con Azure AD. Algunas de las características de integración incluyen la sincronización de directorios y el inicio de sesión único, las que extienden aún más el alcance de sus identidades del entorno local existentes hacia la nube para lograr una mejor administración y experiencia de usuario final.
 [Más información][1]

### Integración con sus aplicaciones

Los desarrolladores de aplicaciones pueden integrar sus aplicaciones con Azure AD para ofrecer a sus usuarios la funcionalidad de inicio de sesión único. Esto permite que las aplicaciones empresariales se hospeden en la nube y autentiquen fácilmente a los usuarios con las credenciales corporativas. También permite que los proveedores de software como servicio (SaaS) faciliten la autenticación para los usuarios en las organizaciones de Azure AD cuando se autentican en sus servicios. Los desarrolladores pueden también usar el API Graph para consultar los datos del directorio a fin de administrar entidades, como por ejemplo usuarios o grupos. [Más información][2]

**Recursos adicionales**

-   [Inicio de sesión en Azure como una organización][]
-   [Identidad de Azure][]
-   [Biblioteca de Azure AD en MSDN][]

  [Más información]: http://msdn.microsoft.com/library/hh967611.aspx
  [1]: http://msdn.microsoft.com/library/jj573653
  [2]: http://go.microsoft.com/fwlink/?LinkID=290817&clcid=0x409
  [Inicio de sesión en Azure como una organización]: /es-es/manage/services/identity/organizational-account/
  [Identidad de Azure]: /es-es/manage/windows/fundamentals/identity/
  [Biblioteca de Azure AD en MSDN]: http://go.microsoft.com/fwlink/?LinkId=293425
