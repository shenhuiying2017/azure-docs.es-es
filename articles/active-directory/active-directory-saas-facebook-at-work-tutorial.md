---
title: "Tutorial: Integración de Azure Active Directory con Workplace by Facebook | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 4f34f6509d762cba6aba98a6eba010fd94656e67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: integración de Azure Active Directory con Workplace by Facebook

En este tutorial obtendrá información sobre cómo integrar Workplace by Facebook con Azure Active Directory (Azure AD).

Integrar Workplace by Facebook con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Workplace by Facebook.
- Puede permitir que los usuarios inicien sesión automáticamente en Workplace by Facebook (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Workplace by Facebook, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único (SSO) de Workplace by Facebook

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único (SSO) de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Workplace by Facebook desde la galería.
2. Configuración y prueba del inicio de sesión único en Azure AD.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Adición de Workplace by Facebook desde la galería
Para configurar la integración de Workplace by Facebook en Azure AD, agregue Workplace by Facebook desde la galería a la lista de aplicaciones SaaS administradas.

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Workplace by Facebook** y seleccione **Workplace by Facebook** desde los resultados. Después, seleccione **Agregar** para agregar la aplicación.

    ![Workplace by Facebook en la lista de resultados](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección podrá configurar y probar el SSO de Azure AD con Workplace by Facebook con un usuario de prueba llamado "Britta Simon".

Para que el SSO funcione, Azure AD debe saber cuál es el usuario homólogo de Workplace by Facebook para un usuario de Azure AD. Es decir, es necesario establecer una relación vinculada entre un usuario de Azure AD y el usuario relacionado de Workplace by Facebook.

Establezca esta relación mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Workplace by Facebook.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección habilitará el SSO de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Workplace by Facebook.

1. En la página de integración de la aplicación **Workplace by Facebook** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, seleccione el **Modo** en **Inicio de sesión basado en SAML** para habilitar el SSO.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. En la sección **Dominio y direcciones URL de Workplace by Facebook**, haga lo siguiente:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company subdomain>.facebook.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://www.facebook.com/company/<scim company id>`.

    > [!NOTE]
    > Estos valores son solo un ejemplo. Debe actualizarlos con la dirección URL de inicio de sesión y el identificador reales. Póngase en contacto con el [equipo de soporte técnico al cliente de Workplace by Facebook](https://workplace.fb.com/faq/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, seleccione **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Seleccione **Guardar**.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Workplace by Facebook**, seleccione **Configurar Workplace by Facebook** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. En otra ventana del explorador web, inicie sesión en el sitio de empresa de Workplace by Facebook como administrador.
  
   > [!NOTE] 
   > Como parte del proceso de autenticación SAML, Workplace puede usar cadenas de consulta de hasta 2,5 kilobytes de tamaño para pasar parámetros a Azure AD.

8. En el **panel de empresa**, vaya a la pestaña **Autenticación**.

9. En **Autenticación SAML**, seleccione **Solo SSO** en la lista desplegable.

10. Escriba los valores copiados de la sección **Configuración de Workplace by Facebook** de Azure Portal en los campos correspondientes:

    *   En el cuadro de texto **Dirección URL de SAML**, pegue el valor de **Dirección URL del servicio de inicio de sesión único** que ha copiado de Azure Portal.
    *   En el cuadro de texto **SAML Issuer URL** (Dirección URL del emisor SAML), pegue el valor de **SAML Entity ID** (Identificador de entidad SAML) que ha copiado de Azure Portal.
    *   En el cuadro de texto **Logout URL SAML** (Dirección URL de cierre de sesión de SAML), pegue el valor de **Dirección URL de cierre de sesión** que ha copiado de Azure Portal.
    *   Abra su **certificado codificado en base 64** en el Bloc de notas, descargado de Azure Portal. Copie el contenido del mismo en el Portapapeles y, después, péguelo en el cuadro de texto **Certificado SAML**.

11. Es posible que tenga que escribir la dirección URL del público, la dirección URL del destinatario y la dirección URL de ACS (Servicio de consumidor de aserciones) que aparecen en la sección **Configuración de SAML**.

12. Desplácese hasta la parte inferior de la sección y seleccione **Probar SSO**. Aparece una ventana emergente, con la página de inicio de sesión de Azure AD. Para autenticarse, escriba las credenciales de la forma habitual. Asegúrese de que la dirección de correo electrónico que se devuelve desde Azure AD es la misma que la cuenta de Workplace con la que ha iniciado sesión.

13. Si la prueba ha finalizado correctamente, desplácese hasta la parte inferior de la página y seleccione **Guardar**.

14. A todo el que use Workplace se le presentará la página de inicio de sesión de Azure AD para la autenticación.

Puede optar por configurar una dirección URL de cierre de sesión de SAML, que puede usarse para apuntar a la página de cierre de sesión de Azure AD. Si esta opción está habilitada y configurada, ya no se dirige al usuario a la página de cierre de sesión de Workplace, sino que se le redirige a la dirección URL agregada en la opción de redirección del cierre de sesión de SAML.


> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, simplemente seleccione la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada en el artículo sobre la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="configure-reauthentication-frequency"></a>Configuración de la frecuencia de reautenticación

Puede configurar Workplace para que solicite una comprobación SAML cada día, cada tres días, cada semana, cada dos semanas, cada mes o nunca.

> [!NOTE] 
>El valor mínimo para la comprobación SAML en aplicaciones móviles se establece en una semana.

También puede forzar un restablecimiento de SAML para todos los usuarios. Para ello, use el botón **Require SAML authentication for all users now** (Requerir autenticación de SAML para todos los usuarios ahora).


### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

1. En el panel izquierdo de **Azure Portal**, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y seleccione **Todos los usuarios**.
    
    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, seleccione **Agregar**.
 
    ![Botón Agregar](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. En el cuadro de diálogo **Usuario**, haga lo siguiente:
 
    ![Cuadro de diálogo Usuario](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anótela.

    d. Seleccione **Crear**.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Creación de un usuario de prueba de Workplace by Facebook

En esta sección se crea un usuario llamado Britta Simon en Workplace by Facebook. Workplace by Facebook admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

El usuario no tiene que hacer nada en esta sección. Si no existe un usuario en Workplace by Facebook, se crea uno cuando intenta obtener acceso a Workplace by Facebook.

>[!Note]
>Si tiene que crear un usuario de forma manual, póngase en contacto con el [equipo de soporte técnico al cliente de Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para usar el SSO de Azure al concederle acceso a Workplace by Facebook.

![Asignar usuario][200] 

1. En Azure Portal, abra la vista de aplicaciones. Vaya a la vista de directorios, vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Workplace by Facebook**.

    ![Vínculo de Workplace by Facebook en la lista de aplicaciones](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

4. Seleccione **Agregar**. Después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon**.

6. En el cuadro de diálogo **Usuarios y grupos**, elija **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

Si desea probar la configuración de inicio de sesión único (SSO), abra el panel de acceso.
Para más información, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Pasos siguientes

* Consulte la [lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md).
* Consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).
* Más información acerca de cómo [configurar el aprovisionamiento de usuarios](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png

