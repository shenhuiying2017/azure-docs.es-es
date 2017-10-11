---
title: "Tutorial: Integración de Azure Active Directory con FirmPlay - Employee Advocacy for Recruiting | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y FirmPlay - Employee Advocacy for Recruiting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 3cddd5b9508159089bf344dbb3882d462799747c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Tutorial: Integración de Azure Active Directory con FirmPlay - Employee Advocacy for Recruiting

En este tutorial, aprenderá a integrar FirmPlay - Employee Advocacy for Recruiting con Azure Active Directory (Azure AD).

La integración de FirmPlay - Employee Advocacy for Recruiting con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD que tenga acceso a FirmPlay - Employee Advocacy for Recruiting
- Puede permitir que los usuarios inicien sesión automáticamente en FirmPlay - Employee Advocacy for Recruiting (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con FirmPlay - Employee Advocacy for Recruiting, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión única de FirmPlay - Employee Advocacy for Recruiting


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de FirmPlay - Employee Advocacy for Recruiting desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Adición de FirmPlay - Employee Advocacy for Recruiting desde la galería
Para configurar la integración de FirmPlay - Employee Advocacy for Recruiting en Azure AD, debe agregar FirmPlay - Employee Advocacy for Recruiting de la galería a la lista de aplicaciones administradas de SaaS.

**Para agregar FirmPlay - Employee Advocacy for Recruiting desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **FirmPlay - Employee Advocacy for Recruiting**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. En el panel de resultados, seleccione **FirmPlay - Employee Advocacy for Recruiting** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con FirmPlay - Employee Advocacy for Recruiting utilizando usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de FirmPlay - Employee Advocacy for Recruiting para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de FirmPlay - Employee Advocacy for Recruiting.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en FirmPlay - Employee Advocacy for Recruiting.

Para configurar y probar el inicio de sesión único de Azure AD con FirmPlay - Employee Advocacy for Recruiting, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de FirmPlay - Employee Advocacy for Recruiting](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**: para tener un equivalente de Britta Simon en FirmPlay - Employee Advocacy for Recruiting que esté vinculado a la representación de Azure AD de ese usuario.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación FirmPlay - Employee Advocacy for Recruiting.

**Para configurar el inicio de sesión único de Azure AD con FirmPlay - Employee Advocacy for Recruiting, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **FirmPlay - Employee Advocacy for Recruiting**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. En la sección **Dominio y direcciones URL de FirmPlay - Employee Advocacy for Recruiting**, en el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL usando el patrón siguiente: `https://<your-subdomain>.firmplay.com/`

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Tenga en cuenta que este no es el valor real. Tiene que actualizar este valor con la dirección URL de inicio de sesión real. Póngase en contacto con [FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. En la sección **FirmPlay - Employee Advocacy for Recruiting**, haga clic en **Configurar FirmPlay - Employee Advocacy for Recruiting** para abrir el cuadro de diálogo **Configurar inicio de sesión**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el equipo de soporte técnico de [FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) y proporcione lo siguiente: 

    • El **archivo de certificado** descargado

    • La **dirección URL de servicio de inicio de sesión único de SAML**

    • El **identificador de entidad de SAML**

    • La **dirección URL de cierre de sesión**
  

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Creación de un usuario de prueba de FirmPlay - Employee Advocacy for Recruiting

En esta sección, creará un usuario llamado a Britta Simon en FirmPlay - Employee Advocacy for Recruiting. Trabaje con [FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) para agregar usuarios a la plataforma FirmPlay - Employee Advocacy for Recruiting.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a FirmPlay - Employee Advocacy for Recruiting.

![Asignar usuario][200] 

**Para asignar a Britta Simon a FirmPlay - Employee Advocacy for Recruiting desde la galería, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **FirmPlay - Employee Advocacy for Recruiting**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de FirmPlay - Employee Advocacy for Recruiting en el Panel de acceso, debe iniciar sesión automáticamente en FirmPlay - Employee Advocacy for Recruiting.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png