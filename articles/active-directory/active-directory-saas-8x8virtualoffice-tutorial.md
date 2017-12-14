---
title: "Tutorial: integración de Azure Active Directory con 8x8 Virtual Office | Microsoft Azure"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y 8x8 Virtual Office."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 71362243bf56e7a8b6e013ab7a91ecac89a32117
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integración de Azure Active Directory con 8x8 Virtual Office

En este tutorial, obtendrá información sobre cómo integrar 8x8 Virtual Office con Azure Active Directory (Azure AD).

La integración de 8x8 Virtual Office con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a 8x8 Virtual Office.
- Puede permitir que los usuarios inicien sesión automáticamente en 8x8 Virtual Office (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con 8x8 Virtual Office, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en 8x8 Virtual Office

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de 8x8 Virtual Office desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Incorporación de 8x8 Virtual Office desde la galería
Para configurar la integración de 8x8 Virtual Office en Azure AD, es preciso agregar 8x8 Virtual Office desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar 8x8 Virtual Office desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **8x8 Virtual Office**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_search.png)

5. En el panel de resultados, seleccione **8x8 Virtual Office** y, después, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con 8x8 Virtual Office con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de 8x8 Virtual Office para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de 8x8 Virtual Office.

Para establecer la relación de vínculo, en 8x8 Virtual Office, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con 8x8 Virtual Office, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)**: para tener un homólogo de Britta Simon en 8x8 Virtual Office que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación 8x8 Virtual Office.

**Para configurar el inicio de sesión único de Azure AD con 8x8 Virtual Office, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **8x8 Virtual Office**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_samlbase.png)

3. En la sección **Dominio y direcciones URL de 8x8 Virtual Office**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:

    | `https://sso.8x8.com/<companyname>` |
    | `https://www.8x8.com/<companyname>` |
    | `https://sso.8x8pilot.com/<companyname>` |

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:

    | `https://<subdomain>.8x8.com/saml2` |
    | `https://<subdomain>.8x8pilot.com/saml2`|

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us) para obtener estos valores.
 


4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (sin procesar)** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de 8x8 Virtual Office**, haga clic en **Configurar 8x8 Virtual Office** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_configure.png) 

7. Inicie la sesión en el inquilino de 8x8 Virtual Office como administrador.

8. Seleccione el **administrador de cuentas de Virtual Office** en el panel de la aplicación.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Seleccione la cuenta **empresarial** que se va a administrar y haga clic en el botón **Iniciar sesión**.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Haga clic en la pestaña **Cuentas** en la lista de menús.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Haga clic en **Inicio de sesión único** en la lista de cuentas.
   
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Seleccione **Inicio de sesión único** en el método de autenticación y haga clic en **SAML**.
    
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. Copie la **dirección URL de inicio de sesión único de SAML**, la **dirección URL del servicio de cierre de sesión único** y la **dirección URL del emisor** de Azure AD a **Sign In URL** (URL de inicio de sesión), **Sign Out URL** (URL de cierre de sesión) e **Issuer URL** (URL de emisor) en 8x8 Virtual Office. 
    
    ![Configurar en la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
14. Haga clic en el botón **Explorador** para cargar el certificado que descargó de Azure AD y luego haga clic en el botón **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-8x8-virtual-office-test-user"></a>Creación de un usuario de prueba de 8x8 Virtual Office

El objetivo de esta sección es crear un usuario llamado Britta Simon en 8x8 Virtual Office. 8x8 Virtual Office admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a 8x8 Virtual Office se crea un nuevo usuario, en caso de que no exista. 

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el [equipo de soporte técnico de 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us). 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a 8x8 Virtual Office.

![Asignar usuario][200] 

**Para asignar a Britta Simon a 8x8 Virtual Office, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **8x8 Virtual Office**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de 8x8 Virtual Office en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación 8x8 Virtual Office.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png

