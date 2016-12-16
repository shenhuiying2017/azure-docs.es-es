---
title: "Tutorial: integración de Azure Active Directory con Druva | Microsoft Docs"
description: "Aprenda a usar Druva con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51e6da24517479c7fe47e811cc2355ccdeb961b3


---
# <a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Tutorial: integración de Azure Active Directory con Druva
El objetivo de este tutorial es mostrar la integración de Azure y Druva.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Druva

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Druva podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Druva (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Druva
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-druva-tutorial/IC795084.png "Scenario")

## <a name="enabling-the-application-integration-for-druva"></a>Habilitación de la integración de aplicaciones para Druva
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Druva.

### <a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Druva:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-druva-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-druva-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Druva**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-druva-tutorial/IC795085.png "Application Gallery")
7. En el panel de resultados, seleccione **Druva** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Druva con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

La aplicación Druva espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .  
La siguiente captura de pantalla muestra un ejemplo:

![atributos del token de SAML](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Druva**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795027.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Druva?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795088.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Druva**, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación Druva (por ejemplo: *https://cloud.druva.com/home/*) y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-druva-tutorial/IC795089.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Druva**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795090.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Druva como administrador.
6. Vaya a **Administrar \> Configuración**.
   
   ![Configuración](./media/active-directory-saas-druva-tutorial/IC795091.png "Settings")
7. En el cuadro de diálogo Single Sign-On Settings (Configuración de inicio de sesión único), siga estos pasos:
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl Sign-On Settings")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Druva**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de proveedor de Id.**
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Druva**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de proveedor de Id.**
   3. Cree un archivo **codificado en base 64** a partir del certificado descargado.  
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado de proveedor de Id.** .
   5. Para abrir la página **Configuración**, haga clic en **Guardar**.
8. En la página **Configuración**, haga clic en**Generar token de SSO**.
   
   ![Configuración](./media/active-directory-saas-druva-tutorial/IC795093.png "Settings")
9. En el cuadro de diálogo **Token de autenticación de inicio de sesión único** , siga estos pasos:
   
   ![Token de SSO](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")
   
   1. Haga clic en **Copiar**.
   2. Haga clic en **Cerrar**.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-druva-tutorial/IC795095.png "Configure Single Sign-On")
11. En el menú de la parte superior, haga clic en **Atributos** to open the **SAML Token Atributos** .
    
    ![Atributos](./media/active-directory-saas-druva-tutorial/IC795096.png "Attributes")
12. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
    
    | Nombre del atributo | Valor de atributo |
    | --- | --- |
    | insync\_auth\_token |<*valor Portapapeles*> |
    
    1. En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    2. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para esa fila.
    3. En el cuadro de texto **Valor de atributo** , escriba el valor de atributo que se muestra para la fila.
    4. Haga clic en **Completo**.
13. Haga clic en **Aplicar cambios**.
    
    ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Druva, deben aprovisionarse en Druva.  
En el caso de Druva, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en el sitio de la compañía de **Druva** como administrador.
2. Vaya a **Administrar \> usuarios**.
   
   ![Administrar usuarios](./media/active-directory-saas-druva-tutorial/IC795097.png "Manage Users")
3. Haga clic en **Create New**.
   
   ![Administrar usuarios](./media/active-directory-saas-druva-tutorial/IC795098.png "Manage Users")
4. En el cuadro de diálogo Create New User (Crear nuevo usuario), realice los pasos siguientes:
   
   ![Crear usuario nuevo](./media/active-directory-saas-druva-tutorial/IC795099.png "Create NewUser")
   
   1. Escriba la dirección de correo electrónico y el nombre de la cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
   2. Haga clic en **Crear usuario**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Druva que proporcione Druva para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-druva-perform-the-following-steps"></a>Para asignar usuarios a Druva, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Druva**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-druva-tutorial/IC795100.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-druva-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


