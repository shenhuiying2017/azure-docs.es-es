---
title: "Tutorial: Integración de Azure Active Directory con Citrix GoToMeeting | Microsoft Docs"
description: "Aprenda cómo usar Citrix GoToMeeting con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7d7897f6-b88e-4dd5-8f3a-e612337b1413
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 400793331aa2d56358a83a51ce64c67f59bbf3b7
ms.openlocfilehash: d81de30a46f7b849e70b99e7f7423cbc90f25326
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Tutorial: Integración de Azure Active Directory con Citrix GoToMeeting

El objetivo de este tutorial es mostrar la integración de Azure y Citrix GoToMeeting. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino en Citrix GoToMeeting

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Citrix GoToMeeting
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Configuración](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuración")

## <a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Habilitación de la integración de aplicaciones para Citrix GoToMeeting
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Citrix GoToMeeting.

### <a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Citrix GoToMeeting:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Agregar una aplicación de la Galería")

6. En el **cuadro de búsqueda** escriba** Citrix GoToMeeting**.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7. En el panel de resultados, seleccione **Citrix GoToMeeting** y, a continuación, haga clic en **Completar** para agregar la aplicación.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
   
## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Citrix GoToMeeting con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario cargar un certificado codificado en base&64; en su inquilino de Citrix GoToMeeting.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En la página de integración de aplicaciones de **Citrix GoToMeeting**, haga clic en** Configurar inicio de sesión único** para abrir el cuadro de diálogo **CONFIGURAR INICIO DE SESIÓN ÚNICO**.
   
    ![Habilitar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Habilitar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en Citrix GoToMeeting?**, seleccione **Inicio de sesión único de Microsoft Azure AD**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurar inicio de sesión único")

3. En la página **Configurar las opciones de la aplicación**, haga clic en **Siguiente**. 
   
    ![Habilitar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Habilitar inicio de sesión único")

4. En la página **Configuración de inicio de sesión único en Citrix GoToMeeting**, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurar inicio de sesión único")

5. En otra ventana del explorador, inicie sesión en su [Centro de organización de Citrix](https://account.citrixonline.com/organization/administration/).

6. Haga clic en la pestaña **Proveedor de identidades** y realice los pasos siguientes:  
   
    ![Configuración de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Configuración de SAML")
   
    a. Seleccione **Manual**

    b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Citrix GoToMeeting**, copie el valor **Dirección URL de la página de inicio de sesión** y péguelo en el cuadro de texto **Sign-In Page URL** (Dirección URL de la página de inicio de sesión). 

    c. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Citrix GoToMeeting**, copie el valor **Dirección URL de la página de cierre de sesión** y péguelo en el cuadro de texto **Sign-out Page URL** (Dirección URL de la página de cierre de sesión).

    d. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Citrix GoToMeeting**, copie el valor **ID de entidad** y péguelo en el cuadro de texto **Identity Provider Entity ID** (Id. de entidad del proveedor de identidades).

    e. Para cargar el certificado descargado, haga clic en **Cargar certificado**.

    f. Haga clic en **Guardar**.


1. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurar inicio de sesión único")

2. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Configuración de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Configuración de SAML")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Citrix GoToMeeting.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Citrix GoToMeeting**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento de usuarios**.
   
    ![Configurar aprovisionamiento de usuarios](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurar aprovisionamiento de usuarios")

2. En la página **Configuración y credenciales de administrador** , realice los pasos siguientes:
   
    ![Configurar aprovisionamiento de usuarios](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurar aprovisionamiento de usuarios")
   
    a. En el cuadro de texto **Nombre de usuario de administrador de Citrix GoToMeeting** , escriba el nombre de usuario de un administrador.

    b. En el cuadro de texto **Contraseña de administrador de Citrix GoToMeeting** , escriba la contraseña del administrador.

    c. Haga clic en **Next**.

1. En la página **Confirmación** , haga clic en la marca de verificación para guardar la configuración.
2. Haga clic en el botón **Validar** para verificar la configuración.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Para asignar usuarios a Citrix GoToMeeting, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Citrix GoToMeeting**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sí")

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Dropbox for Business.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento. Para ello, haga clic en la opción Panel de la página de integración de la aplicación **Citrix GoToMeeting**, que se encuentra en el Portal de Azure clásico.

![Panel](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Panel")

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Estado de integración](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Estado de integración")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso.

Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).


