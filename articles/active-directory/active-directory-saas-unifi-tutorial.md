---
title: "Tutorial: Integración de Azure Active Directory con UNIFI | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y UNIFI."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e1f49ee4-d2d4-4a82-9baf-0587ca1f20f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 09074d4628825909f0bb961c8001e53fb06cf7c0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Tutorial: Integración de Azure Active Directory con UNIFI

En este tutorial, aprenderá a integrar UNIFI con Azure Active Directory (Azure AD).

La integración de UNIFI con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a UNIFI.
- Puede permitir que los usuarios inicien sesión automáticamente en UNIFI (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con UNIFI, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en UNIFI

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de UNIFI desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-unifi-from-the-gallery"></a>Adición de UNIFI desde la galería
Para configurar la integración de UNIFI en Azure AD, es preciso agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar UNIFI desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de texto, escriba **UNIFI**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_search.png)

5. En el panel de resultados, seleccione **UNIFI** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con UNIFI con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de UNIFI para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de UNIFI.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de UNIFI.

Para configurar y probar el inicio de sesión único de Azure AD con UNIFI, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de UNIFI](#creating-a-unifi-test-user)**: el objetivo es tener un homólogo de Britta Simon en UNIFI que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación UNIFI.

**Para configurar el inicio de sesión único de Azure AD con UNIFI, realice los pasos siguientes:**

1. En la página de integración de la aplicación **UNIFI** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_samlbase.png)

3. Vaya a la sección **Dominio y direcciones URL de UNIFI**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_url1.png)

    En el cuadro de texto **Identificador**, escriba el valor `INVIEWlabs`. 

4. Active **Mostrar configuración avanzada de URL**, si quiere configurar la aplicación en modo iniciado por **SP**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.discoverunifi.com/login`

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de UNIFI**, haga clic en **Configurar UNIFI** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_configure.png)

8. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **UNIFI** como administrador.

9. Haga clic en la pestaña **Users** (Usuarios).

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/app1.png) 

10. Haga clic en **Add New Identity Provider** (Agregar nuevo proveedor de identidades).

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/app2.png)

11. En la sección **Add Identity Provider** (Agregar proveedor de identidades), realice los pasos siguientes:   

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/app3.png) 

    a. En el cuadro de texto **Provider Name** (Nombre del proveedor, escriba el nombre del proveedor de identidades.

    b. En el cuadro de texto **SAML Login URL** (Dirección URL de inicio de sesión de SAML), pegue el valor de **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. Abra el certificado que ha descargado de Azure Portal en el Bloc de notas, quite las etiquetas **---BEGIN CERTIFICATE---** y **---END CERTIFICATE---** y pegue el resto del contenido en el cuadro de texto **Certificate** (Certificado).

    d. Active la casilla **is Default Provider** (Es el proveedor predeterminado).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-unifi-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-unifi-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-unifi-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-unifi-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-unifi-test-user"></a>Creación de un usuario de prueba de UNIFI

En esta sección, creará un usuario llamado Britta Simon. **UNIFI** admite el aprovisionamiento automático de usuarios, por lo que se requiere ningún paso manual. Los usuarios se crean automáticamente después de autenticarse correctamente en Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a UNIFI.

![Asignar usuario][200] 

**Para asignar a Britta Simon a UNIFI, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **UNIFI**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-unifi-tutorial/tutorial_unifi_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de UNIFI en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación UNIFI.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-unifi-tutorial/tutorial_general_203.png

