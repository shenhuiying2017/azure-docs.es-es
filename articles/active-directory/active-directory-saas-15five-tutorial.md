---
title: "Tutorial: integración de Azure Active Directory con 15Five | Microsoft Docs"
description: "Aprenda a usar 15Five con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: ce98338e6b21eb35a17f0183f409dd54d1123bb9


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>Tutorial: integración de Azure Active Directory con 15Five
El objetivo de este tutorial es mostrar la integración de Azure y 15Five. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en 15Five

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a 15Five podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de 15Five (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para 15Five
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-15five-tutorial/IC784667.png "Escenario")

## <a name="enabling-the-application-integration-for-15five"></a>Habilitación de la integración de aplicaciones para 15Five
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en 15Five.

### <a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para 15Five:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-15five-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-15five-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-15five-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **15Five**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-15five-tutorial/IC784668.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **15Five** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en 15Five con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **15Five**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784670.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en 15Five?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784671.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión en 15Five**, escriba su dirección URL con el siguiente patrón "*https://company.15Five.com*" y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-15five-tutorial/IC784672.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en 15Five**, haga clic en **Descargar metadatos** y, luego, reenvíe el archivo de metadatos al equipo de soporte técnico de 15Five.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784673.png "Configurar inicio de sesión único")
   
   > [!NOTE]
   > El inicio de sesión único debe habilitarlo el equipo de soporte técnico de 15Five.
   > 
   > 
5. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-15five-tutorial/IC784674.png "Configurar inicio de sesión único")
   

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en 15Five, deben aprovisionarse en 15Five.  
En el caso de 15Five, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en el sitio de la compañía de **15Five** como administrador.
2. Vaya a **Administrar compañía**.
   
   ![Administrar compañía](./media/active-directory-saas-15five-tutorial/IC784675.png "Administrar compañía")
3. Vaya a **Contactos \> Agregar contactos**.
   
   ![Personas](./media/active-directory-saas-15five-tutorial/IC784676.png "Personas")
4. En la sección Add New Person (Agregar nueva persona), lleve a cabo estos pasos:
   
   ![Agregar nueva persona](./media/active-directory-saas-15five-tutorial/IC784677.png "Agregar nueva persona")
   
   1. Especifique **First Name** (Nombre), **Last Name** (Apellido), **Title** (Título), **Email address** (dirección de correo electrónico) de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
   2. Haga clic en **Done**(Listo).
   
   > [!NOTE]
   > El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
   > 
   > 



## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-15five-perform-the-following-steps"></a>Para asignar usuarios a 15Five, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **15Five**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-15five-tutorial/IC784678.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-15five-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


