---
title: "Tutorial: Integración de Azure Active Directory con Hosted Graphite | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Hosted Graphite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 57ee7770193641d0e13da2c7f8aaa6cfc6aefe73
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integración de Azure Active Directory con Hosted Graphite

En este tutorial, aprenderá a integrar Hosted Graphite con Azure Active Directory (Azure AD).

La integración de Hosted Graphite con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Hosted Graphite.
- Puede permitir que los usuarios inicien sesión automáticamente en Hosted Graphite (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Hosted Graphite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Hosted Graphite

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Hosted Graphite desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adición de Hosted Graphite desde la galería
Para configurar la integración de Hosted Graphite en Azure AD, deberá agregar Hosted Graphite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Hosted Graphite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Hosted Graphite**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_search.png)

5. En el panel de resultados, seleccione **Hosted Graphite** y haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Hosted Graphite con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Hosted Graphite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Hosted Graphite.

Para establecer la relación de vínculo, en Hosted Graphite, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Hosted Graphite, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Hosted Graphite](#creating-a-hosted-graphite-test-user)** : para tener un homólogo de Britta Simon en Hosted Graphite que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Hosted Graphite.

**Para configurar el inicio de sesión único de Azure AD con Hosted Graphite, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Hosted Graphite**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_samlbase.png)

3. En la sección **Dominio y direcciones URL de Hosted Graphite**, si quiere configurar la aplicación en el **modo iniciado por IDP**, realice los siguientes pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/metadata/<user id>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/complete/saml/<user id>`.

4. En la sección **Dominio y direcciones URL de Hosted Graphite**, si quiere configurar la aplicación en el **modo iniciado por SP**, realice los siguientes pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  
    a. Haga clic en la opción **Mostrar configuración avanzada de URL**.

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/login/saml/<user id>/`.   

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tiene que actualizarlos con el identificador, la dirección URL de respuesta y la URL de inicio de sesión reales. Para obtener estos valores, puede ir a Acceso -> Configuración de SAML en la aplicación o ponerse en contacto con el [equipo de soporte técnico de Hosted Graphite](mailto:help@hostedgraphite.com).
    >
 
5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Hosted Graphite**, haga clic en **Configurar Hosted Graphite** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la sección de **referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_configure.png) 

8. Inicie la sesión en el inquilino de Hosted Graphite como administrador.

9. Vaya a la **página de configuración de SAML** en la barra lateral (**Acceso -> Configuración de SAML**).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

10. Confirme que estos identificadores URI coinciden con la configuración realizada en la sección **Dominio y direcciones URL de Hosted Graphite** de Azure Portal.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

11. En los cuadros de texto **Entity or Issuer ID** (Id. de entidad o de emisor) y **SSO Login URL** (Dirección URL de inicio de sesión de SSO), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAM) y **SAML Single Sign-On Service URL** que ha copiado de Azure Portal. 
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   

12. Seleccione "**Solo lectura**" en **Default User Role** (Rol de usuario predeterminado).
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

13. Abra el certificado codificado en base 64 descargado de Azure Portal en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

14. Haga clic en el botón **Guardar** .

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-hosted-graphite-test-user"></a>Creación de un usuario de prueba de Hosted Graphite

El objetivo de esta sección es crear un usuario llamado Britta Simon en Hosted Graphite. Hosted Graphite admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Hosted Graphite se creará un nuevo usuario, en caso de que no exista.

>[!NOTE]
>Si necesita crear un usuario manualmente, es preciso que se ponga contacto con el equipo de soporte técnico de Hosted Graphite a través de <mailto:help@hostedgraphite.com>. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Hosted Graphite.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Hosted Graphite, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Hosted Graphite**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Hosted Graphite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Hosted Graphite.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png

