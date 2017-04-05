---
title: "Tutorial: Integración de Azure Active Directory con SugarCRM | Microsoft Docs"
description: "Aprenda cómo usar SugarCRM con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: acd11de9f2b987c3c0bc6d74cff8019aca92f437
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>Tutorial: integración de Azure Active Directory con SugarCRM
El objetivo de este tutorial es mostrar la integración de Azure y SugarCRM.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un suscripción habilitada para el inicio de sesión único (SSO) en SugarCRM

Después de completar este tutorial, los usuarios de Azure AD que asignó a SugarCRM podrán iniciar sesión en la aplicación mediante SSO en el sitio de la compañía de SugarCRM (inicio de sesión iniciado por el proveedor de servicios) o mediante el procedimiento explicado en la [introducción al panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para SugarCRM
2. Configuración del inicio de sesión único (SSO)
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Escenario")

## <a name="enable-the-application-integration-for-sugar-crm"></a>Habilitación de la integración de aplicaciones para SugarCRM
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SugarCRM.

**Siga estos pasos con el fin de habilitar la integración de aplicaciones para SugarCRM:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **SugarCRM**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **SugarCRM** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![CRM de Sugar](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "CRM de Sugar")

## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SugarCRM con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  

Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de SugarCRM.  

Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **SugarCRM**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en SugarCRM?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de SugarCRM**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de SugarCRM (por ejemplo, "*http://company.sugarondemand.com*") y, después, haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configurar dirección URL de la aplicación")

4. En la página **Configurar inicio de sesión único en SugarCRM**, para descargar el certificado, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de SugarCRM como administrador.

6. Vaya a **Administración**.
   
    ![Administración](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administración")

7. En la sección **Administración**, haga clic en **Administración de contraseñas**.
   
    ![Administración](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administración")

8. Seleccione **Habilitar autenticación SAML**.
   
    ![Administración](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administración")

9. En la sección **Autenticación SAML** , realice los pasos siguientes:
   
    ![Autenticación SAML](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "Autenticación SAML")   
  1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SugarCRM**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
  2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SugarCRM**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **SLO URL** (Dirección URL de SLO).
  3. Cree un archivo **codificado en base 64** a partir del certificado descargado.
      
     >[!TIP]
     >Para más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o). 
     > 

  4. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego pegue todo el certificado en el cuadro de texto **Certificado X.509** .
  5. Haga clic en **Guardar**.

10. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SugarCRM**, seleccione la confirmación de configuración de inicio de sesión único y, después, haga clic en **Completar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configurar inicio de sesión único")

## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios
Para permitir que los usuarios de Azure AD inicien sesión en SugarCRM, deben aprovisionarse a SugarCRM.

En el caso de SugarCRM, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de compañía de **SugarCRM** como administrador.
2. Vaya a **Administración**.
   
    ![Administración](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administración")

3. En la sección **Administración**, haga clic en **Administración de usuarios**.
   
    ![Administración](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administración")

4. Vaya a **Usuarios \> Crear nuevo usuario**.
   
    ![Creación de nuevos usuarios](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Creación de nuevos usuarios")

5. En la pestaña **Perfil de usuario** , realice los siguientes pasos:
   
    ![Nuevo usuario](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Nuevo usuario")
  * Escriba el nombre de usuario, el apellido y la dirección de correo electrónico de un usuario de Azure Active Directory válido en los cuadros de texto relacionados.
6. Como **Estado**, seleccione **Activo**.

7. En la pestaña Contraseña, realice los pasos siguientes:
   
    ![Nuevo usuario](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Nuevo usuario")
  1. Escriba la contraseña en el cuadro de texto relacionado.
  2. Haga clic en **Guardar**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SugarCRM ofrecida por SugarCRM para aprovisionar cuentas de usuario de AAD. 
> 

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a SugarCRM, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de aplicaciones de **SugarCRM**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


