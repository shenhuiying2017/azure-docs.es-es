---
title: "Tutorial: integración de Azure Active Directory con Wdesk | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Wdesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 9338c8b8ef740885562ea3f2f1ac316b2a0783fb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Tutorial: integración de Azure Active Directory con Wdesk

En este tutorial, aprenderá a integrar Wdesk con Azure Active Directory (Azure AD).

La integración de Wdesk con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Wdesk
- Puede permitir que los usuarios inicien sesión automáticamente en Wdesk (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si quiere conocer más detalles sobre la integración de aplicaciones SaaS con Azure AD, vea: [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Wdesk, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Wdesk

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Wdesk desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-wdesk-from-the-gallery"></a>Adición de Wdesk desde la galería
Para configurar la integración de Wdesk en Azure AD, es preciso agregar Wdesk desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Wdesk desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Wdesk**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. En el panel de resultados, seleccione **Wdesk** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Wdesk con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Wdesk para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Wdesk.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Wdesk.

Para configurar y probar el inicio de sesión único de Azure AD con Wdesk, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Wdesk](#creating-a-wdesk-test-user)**: el objetivo es tener un homólogo de Britta Simon en Wdesk que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Wdesk.

**Para configurar el inicio de sesión único de Azure AD con Wdesk, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Wdesk**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. En la sección **Dominio y direcciones URL de Wdesk**, si quiere configurar la aplicación en el modo iniciado por **IDP**, realice los siguientes pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`.

4. Active **Mostrar configuración avanzada de URL**. Si desea configurar la aplicación en modo iniciado por **SP**, realice el siguientes paso:

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Estos valores se obtienen de portal de WDesk al configurar el SSO. 
  
5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. En otra ventana del explorador web, inicie sesión en Wdesk como Security Administrator.

8. En la parte inferior izquierda, haga clic en **Admin** (Administración) y elija **Account Admin** (Administrador de cuenta):
 
     ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. En Wdesk Admin, navegue hasta **Security** (Seguridad) y, después, **SAML** > **SAML Settings** (Configuración de SAML):

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. En **General Settings** (Configuración general), seleccione la casilla **Enable SAML Single Sign On** (Habilitar inicio de sesión único de SAML):

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. En **Service Provider Details** (Detalles del proveedor de servicios), siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copia el valor de **Login URL** (Dirección URL de inicio de sesión) y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** de Azure Portal.
   
      b. Copia el valor de **Metadata Url** (Dirección URL de metadatos) y péguelo en el cuadro de texto **Identificador** de Azure Portal.
       
      c. Copia el valor de **Consumer url** (Dirección URL del consumidor ) y péguelo en el cuadro de texto **URL de respuesta** de Azure Portal.
   
      d. Haga clic en **Guardar** en Azure Portal para guardar los cambios.      

12. Haga clic en **Configure IdP Settings** (Configurar valores de IdP) para abrir el cuadro de diálogo **Edit IdP Settings** (Editar valores de IdP). Haga clic en **Choose File** (Elegir archivo) para buscar el archivo **Metadata.xml** que guardó en Azure Portal y cárguelo.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Haga clic en **Guardar cambios**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-wdesk-test-user"></a>Creación de usuario de prueba de Wdesk

Para permitir que los usuarios de Azure AD inicien sesión en Wdesk, tienen que aprovisionarse en Wdesk. En el caso de Wdesk, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en Wdesk como administrador de seguridad.
2. Navegue hasta **Admin** (Administración)  > **Account Admin** (Administrador de cuenta).

     ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Haga clic en **Members** (Miembros) en **People** (Contactos).

4. A continuación, haga clic en **Add Member** (Agregar miembro) para abrir el cuadro de diálogo **(Agregar miembro)**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. En el cuadro de texto **User** (Usuario), escriba el nombre del usuario así **brittasimon@contoso.com** y haga clic en el botón **Continue** (Continuar).

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Escriba los detalles que se muestran a continuación:
  
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario con el siguiente formato **brittasimon@contoso.com**.

    b. En el cuadro de texto **First Name** (Nombre), escriba el nombre de usuario, en este caso **Britta**.

    c. En el cuadro de texto **Last Name** (Apellidos), escriba el nombre de usuario, en este caso **Simon**.

7. Haga clic en el botón **Save Member** (Guardar miembro).  

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Wdesk.

![Asignar usuario][200] 

**Para asignar Britta Simon a Wdesk, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Wdesk**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Wdesk en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Wdesk.
Para más información sobre el Panel de acceso, vea la [introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png

