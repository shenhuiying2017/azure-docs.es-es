---
title: "Novedades sobre la administración de aplicaciones empresariales en Azure Active Directory | Microsoft Docs"
description: "Conozca las novedades sobre la administración de aplicaciones empresariales en Azure Active Directory."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 0c32a6719292aa903aa32dfdc4a31114e7a28346
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Novedades sobre la administración de aplicaciones empresariales en Azure Active Directory 

Azure Active Directory (Azure AD) ha mejorado las herramientas de administración de aplicaciones empresariales, con nuevas características y funcionalidades que simplifican la administración de aplicaciones y aumentan su eficiencia.

Las siguientes son algunas de las mejoras de Azure AD en el [portal de Azure](https://portal.azure.com):

- Una nueva y mejorada experiencia de la galería de aplicaciones, con un modelo de creación de aplicaciones simplificado y compatibilidad con todos los tipos de aplicaciones que suele usar. 
- Una experiencia de inicio rápido novedosa que puede ayudarle a empezar con una prueba piloto de la aplicación. 
- Configure directivas de autoservicio con unos pocos clics. 
- Mejoras en las experiencias del proxy de aplicación, la configuración del inicio de sesión único y "traiga sus propias aplicaciones", que le permiten realizar más tareas que nunca.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Mejoras en la Galería de aplicaciones de Azure Active Directory

Agregue sus aplicaciones favoritas tanto si son de la [galería de aplicaciones](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), aplicaciones personalizadas que se extienden a la nube o nuevas aplicaciones en proceso de desarrollo.  Puede empezar a trabajar con esta nueva experiencia haciendo clic en el botón **Agregar** situado en las hojas de información general de **Aplicaciones empresariales** o **Todas las aplicaciones**.
 
  ![Agregar una aplicación](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Una vez en la galería, aparecerán todas las aplicaciones destacadas que admiten el aprovisionamiento de usuarios en la zona central frontal de la pantalla.  Puede examinar todos los tipos de categorías diferentes para buscar las aplicaciones que le interesan, o puede usar la experiencia de búsqueda para encontrar rápidamente las aplicaciones que desea integrar.

  ![La galería de aplicaciones](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Incorporación de aplicaciones personalizadas desde un solo lugar

Además de agregar aplicaciones previamente integradas de la galería, todas las experiencias de configuración de aplicaciones personalizadas que solía usar en el portal de administración clásico son ahora posibles en el nuevo portal. Tanto si está extendiendo una aplicación desde una ubicación local mediante el proxy de aplicación, como si está integrando su propia contraseña o la aplicación federada de SSO, o creando una aplicación nueva mediante el registro de aplicaciones, ahora ya puede hacer todo eso desde un único lugar.

  ![Incorporación de su propia aplicación](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Para empezar a agregar su propia aplicación**:

1. Haga clic en el **vínculo para agregar su propia aplicación** en la parte superior de la galería de aplicaciones. 
2. Se le presentarán dos opciones: **implementar una aplicación existente** o **desarrollar una nueva aplicación**. Siga leyendo para conocer las diferencias entre las dos opciones y cómo utilizarlas.

### <a name="deploying-existing-applications"></a>Implementación de aplicaciones existentes

1. Si tiene una aplicación ya en ejecución y solo desea integrarla en Azure AD para el inicio de sesión único o el aprovisionamiento, elija la opción de **implementar una aplicación existente**. Elija un nombre para la aplicación y haga clic en **Agregar**.
2. Eso es todo. En lugar de tener que conocer todos los detalles acerca de la aplicación por adelantado, ahora puede configurar cómo funciona la nueva aplicación navegando por el menú izquierdo y configurando la aplicación a su gusto en cualquier momento.

  ![Incorporación de una aplicación existente con un solo clic](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Desarrollo de nuevas aplicaciones

1. Si está desarrollando una nueva aplicación, hay una manera sencilla de llegar al registro de aplicaciones directamente desde la galería:
2. Haga clic en la opción para **agregar sus propias aplicaciones** desde la galería de aplicaciones, seleccione la opción para **desarrollar una aplicación existente** y aparecerá un vínculo rápido que le lleva directamente a la experiencia de incorporación de la aplicación.

  ![Incorporación de una aplicación recién desarrollada con unos pocos clics](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Una vez que agrega una aplicación mediante el registro de aplicaciones, verá que esta aparece en la lista de aplicaciones empresariales en la que podrá configurar el inicio de sesión único y administrar las directivas de acceso de la nueva aplicación.

  ![Administración del acceso a la nueva aplicación en Aplicaciones empresariales](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Inicio rápido: póngase en marcha con la nueva aplicación de forma inmediata 

Una vez agregada una aplicación, tanto si esta ya estaba previamente integrada como si se trata de su propia aplicación, hemos creado una experiencia de inicio rápido diseñada para ayudarle a manejar las nuevas aplicaciones rápidamente. Si sigue sistemáticamente cada opción, le conduciremos a través de la interfaz de usuario y le mostraremos cómo empezar a manejar la aplicación lo antes posible mediante una prueba piloto. 
 
  ![Experiencia de inicio rápido de nuevas aplicaciones](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Puede visitar esta nueva experiencia de inicio rápido en cualquier momento y para cualquier aplicación, haciendo clic en **Inicio rápido** en el menú de navegación izquierdo de la aplicación.


## <a name="updated-application-proxy-configuration"></a>Configuración de proxy para aplicaciones actualizadas
Supongamos ahora que una de las nuevas aplicaciones que agregó se está ejecutando en su entorno local y desea integrarla con Azure AD.  Una de las estupendas novedades de la nueva experiencia de configuración de la aplicación en el portal de Azure AD es que mediante la separación entre el modo de inicio de sesión de la aplicación y la configuración de proxy de su aplicación, puede ahora fácilmente exponer el inicio de sesión único con contraseña, o las aplicaciones federadas que se ejecutan en la red corporativa, directamente en la nube, sin tener que crear varias instancias de la aplicación.

Además, ahora también puede configurar todas las aplicaciones que se hayan agregado para su uso mediante el proxy de aplicación de Azure AD directamente desde el nuevo portal, incluidas aquellas aplicaciones que son compatibles con experiencias de autenticación de Windows nativo.

  ![Configuración de una aplicación para que use la opción de inicio de sesión con autenticación integrada de Windows](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Para empezar a configurar una aplicación de autenticación de Windows nativo con el proxy de aplicación:
1. Haga clic en el elemento de navegación de inicio de sesión único y elija **Autenticación integrada de Windows** desde la hoja de configuración del inicio de sesión y configure las opciones como desee.
2. Además de la compatibilidad con estos nuevos modos de autenticación, ahora también puede cargar certificados de dominios personalizados para que se admitan aplicaciones que se ejecutan en puntos de conexión seguros dentro de la organización.  
 
   ![Cargar un certificado para su uso con el proxy de aplicación](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Para cargar un certificado nuevo para su aplicación local preferida, haga clic en la opción **Proxy de aplicación** del menú de navegación izquierdo, haga clic en el selector **Certificado** y cargue un archivo de certificado que se pueda utilizar para cifrar las solicitudes del punto de conexión en la nube en la aplicación.

## <a name="advanced-federated-single-sign-on-configuration"></a>Configuración del inicio de sesión único federado avanzado

Para todos aquellos que usan aplicaciones federadas actualmente, hay muchas características nuevas en la hoja de configuración de inicio de sesión basado en SAML. Para empezar, ahora ya puede personalizar, agregar, quitar y asignar por completo los atributos de usuario existentes emitidos como notificaciones en tokens SAML.
 
  ![Personalización de los atributos de usuario de tokens SAML pasados a una aplicación federada](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Para comprobar la nueva configuración de inicio de sesión único federado:
1. Abra la hoja **Inicio de sesión único** de una aplicación federada desde el menú de navegación izquierdo y asegúrese de que el modo "*Inicio de sesión basado en SAML*"* está seleccionado. 
2. Una vez allí, habilite la casilla del encabezado **Atributos de usuario** para modificar todos los atributos que se incluyen en el token SAML pasado a esa aplicación.

Puede también crear, sustituir y administrar certificados usados para un inicio de sesión único federado, así como editar quién recibe notificaciones cuando el certificado está a punto de expirar. Verá estas nuevas opciones en el encabezado **Certificados** de la misma hoja Inicio de sesión único.
 
  ![Creación de un nuevo certificado, personalización del correo electrónico de notificación de expiración y opciones de firma de certificado](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Parámetro de estado de la retransmisión
Por último, hemos ampliado también el conjunto de parámetros de dirección URL SAML admitidos para incluir el **parámetro de estado de la retransmisión**, que es la página, dentro de una aplicación federada, a la que se dirigirán los usuarios una vez completado el inicio de sesión. Se trata de una opción muy útil para configurar si desea enviar a los usuarios a una ubicación específica dentro de la aplicación para que se puedan poner en marcha rápidamente.

  ![Configuración del parámetro de estado de la transmisión SAML](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Para establecer el parámetro de estado de la retransmisión**:

1. Active la casilla **Mostrar configuración avanzada de URL** del encabezado **Dominio y direcciones URL** en la hoja de configuración del inicio de sesión único. 
2. Una vez hecho esto, aparecerá un conjunto de nuevos cuadros de entrada de direcciones URL que le permitirán configurar esta opción, y otras, de la dirección URL de SAML.

## <a name="bring-your-own-password-sso-applications"></a>Cómo traer sus propias aplicaciones de inicio de sesión único con contraseña

Somos conscientes de que no todas las aplicaciones admiten la federación directamente. Por ejemplo, puede que una de las nuevas aplicaciones que ha agregado tenga una pantalla de inicio de sesión personalizada en la que los usuarios usan un nombre de usuario y una contraseña para iniciar sesión. Aun así podrá integrar estos tipos de aplicaciones con Azure AD mediante la característica **Bring your own applications** (Traiga sus propias aplicaciones) que ya está disponible para configurar en el nuevo portal.
 
  ![Integración de aplicaciones personalizadas de almacenamiento de contraseñas con Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Para comprobar la característica "Bring your own applications" (Traiga sus propias aplicaciones)**:

1. Después de establecer el modo de inicio de sesión único para una nueva aplicación personalizada que ha agregado al **Inicio de sesión basado en contraseña**, escriba la dirección URL en la que la aplicación muestra su pantalla de inicio de sesión y haga clic en **Guardar**.  
2. Una vez hecho esto, se cargará automáticamente esa dirección URL con un cuadro de entrada de un nombre de usuario y una contraseña y se le permitirá usar Azure AD para transmitir de forma segura las contraseñas de esa aplicación mediante la extensión de explorador del panel de acceso.

## <a name="configure-self-service-application-access"></a>Configuración del acceso a aplicaciones en modo autoservicio

Después de agregar una gran cantidad de nuevas aplicaciones, quizá desee permitir a los usuarios examinar y agregar esas aplicaciones a sus propios paneles de acceso, sin necesidad de molestarle como administrador. Ahora puede, con esta última versión, configurar y administrar el acceso a las aplicaciones en modo autoservicio directamente desde el nuevo portal.

  ![Configuración del acceso a aplicaciones en modo autoservicio para una aplicación de inicio de sesión único con contraseña](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Para configurar y administrar el acceso a aplicaciones en modo autoservicio**:

1. Para empezar, seleccione la opción **Autoservicio** del menú de navegación izquierdo de la aplicación y establezca la opción **Allow users to request access to this application?** (¿Desea permitir a los usuarios solicitar acceso a esta aplicación?) en "**Sí**". 
2. Esto le permitirá configurar a quién se le permite aprobar el acceso a esta aplicación y qué grupo de usuarios del modo autoservicio se agregará. Además, si la aplicación está configurada para el inicio de sesión único con contraseña, también verá otra opción que, opcionalmente, le dejará conceder permiso a esos aprobadores para administrar las contraseñas asignadas a la aplicación.

##<a name="feedback"></a>Comentarios

Esperamos que le guste usa la experiencia mejorada de Azure AD. Envíenos sus comentarios. Publique sus comentarios y sugerencias para la mejora en la sección **Portal de administración** de nuestro [foro de comentarios](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nos interesa cómo crear innovaciones estupendas todos los días y usamos sus comentarios para dar forma y definir qué será lo próximo que crearemos.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [Administración de aplicaciones con Azure Active Directory](active-directory-enable-sso-scenario.md).



