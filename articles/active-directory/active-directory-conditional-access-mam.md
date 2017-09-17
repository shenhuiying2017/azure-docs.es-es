---
title: "Acceso condicional con administración de aplicaciones móviles en Azure Active Directory | Microsoft Docs"
description: "Más información sobre cómo funciona el acceso condicional con administración de aplicaciones móviles en Azure Active Directory."
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
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: c6bc39dc151c80ffe1306464da60a029e54cc6b1
ms.contentlocale: es-es
ms.lasthandoff: 09/05/2017

---
# <a name="conditional-access-with-mobile-app-management-in-azure-active-directory"></a>Acceso condicional con administración de aplicaciones móviles en Azure Active Directory  

El acceso condicional basado en aplicaciones de Azure Active Directory (Azure AD) de Azure Portal, junto con las directivas de protección de aplicaciones de Intune le permiten restringir el acceso de las aplicaciones en la nube a las aplicaciones móviles que admiten la protección de aplicaciones de Intune como, por ejemplo, restringir el acceso de Exchange Online a la aplicación Outlook. Este soporte permite a los dispositivos que no están inscritos en la administración de dispositivos móviles de Intune proteger los datos de la empresa.   

El acceso condicional con administración de aplicaciones móviles le permite combinar con otras directivas como las directivas de acceso condicional basadas en dispositivos para proporcionar flexibilidad en la manera de proteger los datos de los dispositivos personales y corporativos. 

##<a name="before-you-begin"></a>Antes de empezar

En este tema se da por supuesto que está familiarizado con:

- Los conceptos básicos del [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- La [configuración de la directiva de acceso condicional](active-directory-conditional-access-azure-portal-get-started.md).


Además, es posible que desee echar un vistazo a los [procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md).  



## <a name="prerequisites"></a>Requisitos previos

1.  Antes de crear una directiva de acceso condicional basada en la aplicación, debe tener una suscripción a Enterprise Mobility + Security o una suscripción premium a Azure Active Directory y los usuarios deben disponer de una licencia para EMS o Azure AD. 
2.  Antes de crear un nuevo acceso condicional con directiva de administración de aplicaciones móviles, debe revisar los escenarios y los puntos a tener en cuenta sobre la migración

## <a name="supported-platforms"></a>Plataformas compatibles

-   iOS

-   Android

## <a name="approved-client-applications"></a>Aplicaciones cliente aprobadas 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Equipos de Microsoft

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## <a name="exchange-online-policy"></a>Directiva de Exchange Online 

Este escenario consta de un acceso condicional con directiva de administración de aplicaciones móviles para el acceso a Exchange Online con aplicaciones aprobadas.


### <a name="scenario-playbook"></a>Guía de escenario

Este escenario supone que un usuario:

- Configura el correo electrónico mediante una aplicación de correo electrónico nativa de iOS o Android para conectarse a Exchange.

- Recibe un correo electrónico que indica que el acceso solo está disponible mediante la aplicación Outlook.

- Descarga la aplicación con el vínculo.

- Abre la aplicación Outlook e inicia sesión con las credenciales de Azure AD

- Se le pide que instale Authenticor (iOS) o el Portal de empresa (Android) para continuar.

- Instala la aplicación y puede volver a la aplicación Outlook para continuar.

- Se le solicita que registre un dispositivo.

- Puede acceder al correo electrónico.

Todas las directivas de protección de aplicaciones de Intune están activas en el momento de acceder a los datos corporativos y puede que se le pida al usuario que reinicie la aplicación, use un PIN adicional, etc (si esto está configurado para la aplicación y la plataforma).

### <a name="configuration"></a>Configuración 

**Paso 1: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/01.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.

3. **Aplicaciones en la nube:** como aplicación en la nube, debe seleccionar **Office 365 Exchange Online**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Plataformas de dispositivo** y **Aplicaciones cliente**:

    a. Como **Plataformas de dispositivo**, seleccione **Android** e **iOS**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicaciones cliente**, seleccione **Aplicaciones de escritorio y aplicaciones móviles**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acceso**, debe seleccionar **Requerir aplicación cliente aprobada (versión preliminar)**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/05.png)
 

**Paso 2: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online con Active Sync (EAS)**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/06.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.


3. **Aplicaciones en la nube:** como aplicación en la nube, debe seleccionar **Office 365 Exchange Online**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Aplicaciones cliente**. 

    a. Como **Aplicaciones cliente**, seleccione **Exchange Active Sync**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **Controles de acceso**, debe seleccionar **Requerir aplicación cliente aprobada (versión preliminar)**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/05.png)


**Paso 3: Configuración de la directiva de protección de aplicaciones de Intune para aplicaciones cliente de iOS y Android**


![Acceso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [Protección de aplicaciones y datos con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para más información.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Directiva de Exchange Online y SharePoint Online

Este escenario consta de un acceso condicional con directiva de administración de aplicaciones móviles para el acceso a Exchange Online y SharePoint Online con aplicaciones aprobadas.

### <a name="scenario-playbook"></a>Guía de escenario

Este escenario supone que un usuario:

- Intenta usar la aplicación de SharePoint para conectarse y también para ver sus sitios corporativos.

- Intenta iniciar sesión con las mismas credenciales que las credenciales de la aplicación Outlook

- No tiene que volver a registrarse y puede obtener acceso a los recursos.


### <a name="configuration"></a>Configuración

**Paso 1: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online y SharePoint Online**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/71.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.


3. **Aplicaciones en la nube:** como aplicaciones en la nube, debe seleccionar **Office 365 Exchange Online** y **Office 365 SharePoint Online**. 

    ![Acceso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Plataformas de dispositivo** y **Aplicaciones cliente**:

    a. Como **Plataformas de dispositivo**, seleccione **Android** e **iOS**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicaciones cliente**, seleccione **Aplicaciones de escritorio y aplicaciones móviles**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acceso**, debe seleccionar **Requerir aplicación cliente aprobada (versión preliminar)**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/05.png)




**Paso 2: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online con Active Sync (EAS)**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/06.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.

3. **Aplicaciones en la nube:** como aplicación en la nube, debe seleccionar **Office 365 Exchange Online**. En línea 

    ![Acceso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Aplicaciones cliente**:

    a. Como **Aplicaciones cliente**, seleccione **Exchange Active Sync**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **Controles de acceso**, debe seleccionar **Requerir aplicación cliente aprobada (versión preliminar)**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/05.png)




**Paso 3: Configuración de la directiva de protección de aplicaciones de Intune para aplicaciones cliente de iOS y Android**


![Acceso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [Protección de aplicaciones y datos con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para más información.


## <a name="mobile-application-management-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Directiva de administración de aplicaciones móviles o de dispositivo compatible para Exchange Online y SharePoint Online

Este escenario consta de un acceso condicional con directiva de administración de aplicaciones móviles o de dispositivo compatible para el acceso a Exchange Online con aplicaciones aprobadas.


### <a name="scenario-playbook"></a>Guía de escenario

En este escenario se supone que:
 
- Algunos usuarios ya están inscritos (con o sin dispositivos corporativos)

- Los usuarios que no están inscritos ni registrados con Azure AD mediante una aplicación protegida deben registrar un dispositivo para acceder a los recursos

- Los usuarios inscritos mediante la aplicación protegida no tienen que volver a registrar el dispositivo


### <a name="configuration"></a>Configuración

**Paso 1: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online y SharePoint Online**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/62.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.

3. **Aplicaciones en la nube:** como aplicaciones en la nube, debe seleccionar **Office 365 Exchange Online** y **Office 365 SharePoint Online**. 

     ![Acceso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Plataformas de dispositivo** y **Aplicaciones cliente**. 
 
    a. Como **Plataformas de dispositivo**, seleccione **Android** e **iOS**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicaciones cliente**, seleccione **Aplicaciones de escritorio y aplicaciones móviles**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acceso**, debe tener seleccionado lo siguiente:

    - **Requerir que el dispositivo esté marcado como compatible**

    - **Requerir aplicación cliente aprobada (versión preliminar)**

    - **Requerir uno de los controles seleccionados**   
 
    ![Acceso condicional](./media/active-directory-conditional-access-mam/11.png)



**Paso 2: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online con Active Sync (EAS)**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/61.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.

3. **Aplicaciones en la nube:** como aplicación en la nube, debe seleccionar **Office 365 Exchange Online**. 

    ![Acceso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Aplicaciones cliente**. 

    Como **Aplicaciones cliente*, seleccione **Exchange Active Sync**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **Controles de acceso**, debe seleccionar **Requerir aplicación cliente aprobada (versión preliminar)**.
 
    ![Acceso condicional](./media/active-directory-conditional-access-mam/11.png)




**Paso 3: Configuración de la directiva de protección de aplicaciones de Intune para aplicaciones cliente de iOS y Android**


![Acceso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [Protección de aplicaciones y datos con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para más información.





## <a name="mobile-application-management-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Directiva de administración de aplicaciones móviles y de dispositivo compatible para Exchange Online y SharePoint Online

Este escenario consta de un acceso condicional con directiva de administración de aplicaciones móviles y de dispositivo compatible para el acceso a Exchange Online con aplicaciones aprobadas.

### <a name="scenario-playbook"></a>Guía de escenario

Este escenario supone que un usuario:
 
-   Configura el correo electrónico mediante una aplicación de correo electrónico nativa de iOS o Android para conectarse a Exchange.
-   Recibe un correo electrónico que indica que es necesario que el dispositivo esté inscrito para poder acceder.
-   Descarga el portal de la empresa e inicia sesión en este.
-   Comprueba el correo electrónico y se le solicita que use la aplicación Outlook.
-   Descarga la aplicación Outlook.
-   Abre Outlook y escribe las credenciales usadas en la inscripción.
-   El usuario puede acceder al correo electrónico.

Todas las directivas de protección de aplicaciones de Intune están activas en el momento de acceder a los datos corporativos y puede que se le pida al usuario que reinicie la aplicación, use un PIN adicional, etc (si esto está configurado para la aplicación y la plataforma).


### <a name="configuration"></a>Configuración

**Paso 1: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online y SharePoint Online**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/62.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.

3. **Aplicaciones en la nube:** como aplicaciones en la nube, debe seleccionar **Office 365 Exchange Online** y **Office 365 SharePoint Online**. 

     ![Acceso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Plataformas de dispositivo** y **Aplicaciones cliente**. 
 
    a. Como **Plataformas de dispositivo**, seleccione **Android** e **iOS**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **Aplicaciones cliente**, seleccione **Aplicaciones de escritorio y aplicaciones móviles**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **Controles de acceso**, debe tener seleccionado lo siguiente:

    - **Requerir que el dispositivo esté marcado como compatible**

    - **Requerir aplicación cliente aprobada (versión preliminar)**

    - **Requerir uno de los controles seleccionados**   
 
    ![Acceso condicional](./media/active-directory-conditional-access-mam/11.png)



**Paso 2: Configuración de una directiva de acceso condicional de Azure AD para Exchange Online con Active Sync (EAS)**

Para la directiva de acceso condicional descrita en este paso, debe configurar los componentes siguientes:

![Acceso condicional](./media/active-directory-conditional-access-mam/61.png)

1. El **nombre** de la directiva de acceso condicional.

2. Los **usuarios y grupos**: cada directiva de acceso condicional debe tener al menos un usuario o grupo seleccionado.

3. **Aplicaciones en la nube:** como aplicación en la nube, debe seleccionar **Office 365 Exchange Online**. 

    ![Acceso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condiciones:** como **Condiciones**, debe configurar **Aplicaciones cliente**. 

    Como **Aplicaciones cliente**, seleccione **Exchange Active Sync**.

    ![Acceso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **Controles de acceso**, debe tener seleccionado lo siguiente:

    - **Requerir que el dispositivo esté marcado como compatible**

    - **Requerir aplicación cliente aprobada (versión preliminar)**

    - **Requerir todos los controles seleccionados**   
 
    ![Acceso condicional](./media/active-directory-conditional-access-mam/64.png)




**Paso 3: Configuración de la directiva de protección de aplicaciones de Intune para aplicaciones cliente de iOS y Android**


![Acceso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [Protección de aplicaciones y datos con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para más información.



## <a name="migration-considerations"></a>Consideraciones sobre la migración

Si tiene directivas configuradas en el Portal de Azure clásico, debe migrarlas a Azure Portal ya que:


- Un usuario que se encuentra en una directiva del Portal de Azure clásico y en una directiva de Azure Portal debe cumplir los requisitos de las dos directivas 

- Si no se migran las directivas existentes, no podrá implementar las directivas que conceden acceso


## <a name="migration-from-the-azure-classic-portal"></a>Migración desde el Portal de Azure clásico

En este escenario: 

- En su [Portal de Azure clásico](https://manage.windowsazure.com), ha configurado:

    - SharePoint Online

    ![Acceso condicional](./media/active-directory-conditional-access-mam/14.png)

    - Una directiva de acceso condicional basada en dispositivos

    ![Acceso condicional](./media/active-directory-conditional-access-mam/15.png)

- Quiere configurar una directiva de acceso condicional de administración de aplicaciones móviles en Azure Portal 
 

### <a name="configuration"></a>Configuración 

- Revise sus directivas de acceso condicional basadas en dispositivos

- Mígrelas a Azure Portal 

- Agregue las directivas de acceso condicional con administración de aplicaciones móviles


## <a name="migrating-from-intune"></a>Migración desde Intune 

En este escenario:

- En [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), tiene una directiva de acceso condicional con administración de aplicaciones móviles para cualquier Exchange Online o SharePoint Online configurados

    ![Acceso condicional](./media/active-directory-conditional-access-mam/15.png)

- Quiere realizar la migración para poder usar una directiva de acceso condicional con administración de aplicaciones móviles en Azure Portal


### <a name="configuration"></a>Configuración 
 
- Revise sus directivas de acceso condicional basadas en dispositivos

- Mígrelas a Azure Portal 

- Revise sus directivas de acceso condicional con administración de aplicaciones móviles configuradas para cualquier Exchange Online o SharePoint Online en Intune

- Agregue el control para **requerir aplicaciones aprobadas** además del control basado en dispositivos 
 

## <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Migración desde el Portal de Azure clásico e Intune

En este escenario:

- Tiene la siguiente configuración:

    - **Portal de Azure clásico:** condicional basado en dispositivos 

    - **Intune:** directivas de acceso condicional con administración de aplicaciones móviles 
    
- Quiere realizar la migración de ambas directivas para poder usar directivas de acceso condicional con administración de aplicaciones móviles en Azure Portal


### <a name="configuration"></a>Configuración

- Revise sus directivas de acceso condicional basadas en dispositivos

- Mígrelas a Azure Portal 

- Revise la directiva de acceso condicional con administración de aplicaciones móviles configurada para cualquier Exchange Online o SharePoint Online en Intune

- Agregue el control para **requerir aplicaciones aprobadas** además del basado en dispositivos 




## <a name="next-steps"></a>Pasos siguientes

Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
