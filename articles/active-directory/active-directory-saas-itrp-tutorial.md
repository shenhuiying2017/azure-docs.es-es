---
title: "Tutorial: integración de Azure Active Directory con ITRP | Microsoft Docs"
description: "Aprenda a usar ITRP con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 3d1f24cb23a58b4478a30c5e4a0858b474d5d90e
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integración de Azure Active Directory con ITRP
El objetivo de este tutorial es mostrar la integración de Azure e ITRP.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de ITRP

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ITRP podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ITRP (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para ITRP
2. Configuración del inicio de sesión único (SSO)
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-itrp-tutorial/IC775551.png "Escenario")

## <a name="enable-the-application-integration-for-itrp"></a>Habilitación de la integración de aplicaciones para ITRP
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para ITRP.

**Siga estos pasos con el fin de habilitar la integración de aplicaciones para ITRP:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-itrp-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-itrp-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-itrp-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **ITRP**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-itrp-tutorial/IC775565.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **ITRP** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en ITRP con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  

La configuración de un inicio de sesión único para ITRP requiere la recuperación de un valor de huella digital de un certificado.  

Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **ITRP**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en ITRP?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inicio de sesión de ITRP**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino\>.ITRP.com*" y, a continuación, haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurar dirección URL de la aplicación")

4. En la página **Configurar inicio de sesión único en ITRP**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo como **c:\\ITRP.cer**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de ITRP.

6. En la barra de herramientas de la parte superior, haga clic en el icono de **Configuración**.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7. En el panel de navegación izquierdo, haga clic en **Inicio de sesión único**.
   
    ![Inicio de sesión único](./media/active-directory-saas-itrp-tutorial/IC775571.png "Inicio de sesión único")

8. Siga estos pasos en la sección de configuración de Single Sign-On (Inicio de sesión único):
   
    ![Inicio de sesión único](./media/active-directory-saas-itrp-tutorial/IC775572.png "Inicio de sesión único")
    
    ![Inicio de sesión único](./media/active-directory-saas-itrp-tutorial/IC775573.png "Inicio de sesión único")   
  1. Hacer clic en **Habilitar**.
  2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ITRP**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Remote Logout URL** (Dirección URL de cierre de sesión remoto).
  3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ITRP**, copie el valor de **Dirección URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión único de SAML).
  4. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Certificate Fingerprint** (Huella digital del certificado).
      
     >[!TIP]
     >Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).
     >
    
  5. Haga clic en **Guardar**.

9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en ITRP, tienen que aprovisionarse en ITRP.  

En el caso de ITRP, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **ITRP** .

2. En la barra de herramientas de la parte superior, haga clic en el icono de **Registros**.
   
    ![Administración](./media/active-directory-saas-itrp-tutorial/IC775575.png "Administración")

3. En el menú emergente, seleccione **Contactos**.
   
    ![Personas](./media/active-directory-saas-itrp-tutorial/IC775587.png "Personas")

4. Haga clic en **Agregar nueva persona** (“+”).
   
    ![Administración](./media/active-directory-saas-itrp-tutorial/IC775576.png "Administración")

5. En el cuadro de diálogo Add New Person (Agregar nueva persona), realice los pasos siguientes:
   
    ![Usuario](./media/active-directory-saas-itrp-tutorial/IC775577.png "Usuario")   
  1. Escriba el **nombre** y el **correo electrónico** de la cuenta válida de AAD que desee aprovisionar.
  2. Haga clic en **Save**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ITRP que proporcione ITRP para aprovisionar cuentas de usuario de AAD. 
> 

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a ITRP, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure AD, cree una cuenta de prueba.

2. En la página de integración de la aplicación **ITRP**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-itrp-tutorial/IC775588.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-itrp-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


