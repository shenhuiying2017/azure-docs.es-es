<properties umbracoNaviHide="0" pageTitle="How To Configure Cloud Services" metaKeywords="Azure cloud services, cloud service, configure cloud service" description="Learn how to configure Azure cloud services." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Sign up for Azure as an organization" authors="" />

Inicio de sesión en Azure como una organización
===============================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Hasta hace poco, solo era posible registrarse para obtener una suscripción a Azure con su cuenta Microsoft (Windows Live ID). Azure ahora es compatible con cualquiera de los dos métodos de cuenta siguientes para registrarse:

-   **Cuentas Microsoft** (creadas por usted para su uso personal): Proporcionan acceso a todos los productos y servicios en la nube de Microsoft orientados al consumidor, como Outlook (Hotmail), Messenger, SkyDrive, MSN, Xbox LIVE u Office Live. Al registrarse para Outlook automáticamente se crea una cuenta Microsoft con una dirección @Outlook.com. Una vez creada, es posible utilizar una cuenta Microsoft para tener acceso a servicios en la nube de Microsoft relacionados con el cliente y/o a Azure. [Más información](http://windows.microsoft.com/en-US/windows-live/sign-in-what-is-microsoft-account)

-   **Cuentas de organizaciones** (emitidas por un administrador para uso comercial/académico): Proporcionan acceso a todos los servicios en la nube de Microsoft en el nivel de pequeñas, medianas y grandes empresas, como Azure, Windows Intune u Office 365. Cuando se registre como organización para obtener uno de estos servicios, se aprovisiona automáticamente un inquilino basado en la nube en Azure Active Directory para representar a su organización. [Más información](http://technet.microsoft.com/en-us/library/jj573650)

    Una vez que se ha creado este inquilino, un administrador puede emitir cuentas de organización para cada uno de sus empleados/estudiantes y asignar licencias a esas cuentas según las suscripciones de servicio en la nube a las que necesiten tener acceso, como Azure.

    *¿Desea registrarse como organización en Azure?* **Regístrese ahora**

¿Qué es Azure Active Directory?
-------------------------------

Igual que Active Directory es un servicio que se pone a disposición de los clientes a través del sistema operativo Windows Server para que administren su directorio local, Azure Active Directory (Azure AD) es un servicio que está disponible a través de Azure para que pueda administrar el directorio en la nube de su organización. [Más información](http://technet.microsoft.com/en-us/library/hh967619)

Debido a que se trata del directorio en la nube de su organización, usted es quien decide quiénes son sus usuarios, qué información desea mantener en la nube, quién puede usar la información o administrarla y qué aplicaciones o servicios pueden tener acceso a esa información. Cuando utiliza Azure AD, es responsabilidad de Microsoft mantener a Active Directory en funcionamiento en la nube con una alta escala, alta disponibilidad y recuperación ante desastres integrada, y respetar al mismo tiempo completamente sus requisitos de privacidad y seguridad de la información de la organización.

### Integración con Active Directory en un entorno local

Si su organización ya usa Active Directory en un entorno local, puede utilizar las capacidades de integración de directorios de Azure AD, como la sincronización de directorios y el inicio de sesión único, para ampliar todavía más el alcance de las identidades locales existentes en la nube y conseguir una administración y una experiencia del usuario mejoradas. [Más información](http://technet.microsoft.com/en-us/library/jj573653)

