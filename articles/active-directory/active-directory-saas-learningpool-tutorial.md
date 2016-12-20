---
title: "Tutorial: Integración de Azure Active Directory con Learningpool | Microsoft Docs"
description: "Aprenda a usar Learningpool con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 52fec971875bb797b5de679918528c5c472e4182


---
# <a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Tutorial: Integración de Azure Active Directory con Learningpool
El objetivo de este tutorial es mostrar la integración de Azure y Learningpool.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Learningpool

Después de completar este tutorial, los usuarios de Azure AD asignados a Learningpool podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Learningpool (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Learningpool
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-learningpool"></a>Habilitación de la integración de aplicaciones para Learningpool
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Learningpool.

### <a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Learningpool:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Learningpool**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Application Gallery")
7. En el panel de resultados, seleccione **Learningpool** y, a continuación, haga clic en **Completar** para agregar la aplicación.
   
   ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Learningpool con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

La aplicación Learningpool espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .  
La siguiente captura de pantalla muestra un ejemplo.

![atributos del token de SAML](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Learningpool**, en el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.
   
   ![Atributos](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributes")
2. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
   
   ### 
   | Nombre del atributo | Valor de atributo |
   | --- | --- |
   |  | |
   
   | urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname |
   | --- | --- |
   |  urn:oid:2.5.4.42 |User.givenname |
   | urn:oid:0.9.2342.19200300.100.1.3 |User.mail |
   | urn:oid:2.5.4.4 |User.surname |
   
   1. En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
   2. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
   3. En la lista **Valor de atributo** , seleccione el valor de atributo que se muestra para esa fila.
   4. Haga clic en **Completo**.
3. Haga clic en **Aplicar cambios**.
4. En el explorador, haga clic en **Atrás** para volver a abrir el cuadro de diálogo **Inicio rápido**.
5. Para abrir el cuadro de diálogo **Configurar inicio de sesión único**, haga clic en **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configure Singel Sign-On")
6. En la página **How would you like users to sign on to Learningpool** (¿Cómo desea que los usuarios inicien sesión en Learningpool?), seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")
7. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Learningpool**, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación de Learningpool (por ejemplo: https://parliament.preview.learningpool.com/auth/shibboleth/index.php) y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configure App URL")
8. En la página **Configurar inicio de sesión único en Learningpool**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo localmente en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")
9. Reenvíe el archivo de metadatos al equipo de soporte técnico de Learningpool.
   
   > [!NOTE]
   > El equipo de soporte técnico de Learningpool es el que debe habilitar el inicio de sesión único.
   > 
   > 
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Learningpool, deben aprovisionarse en Learningpool.

No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Learningpool.  
Los usuarios deben ser creados por el equipo de soporte de Learningpool.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Learningpool ofrecida por Learningpool para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Para asignar usuarios a Learningpool, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Learningpool**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


