---
title: "Tutorial: Integración de Azure Active Directory con TeamSeer | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TeamSeer."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: b67b2e22d1b2b13ab0103d00ba6c1e62b2fdd17b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: integración de Azure Active Directory con TeamSeer

En este tutorial, aprenderá a integrar TeamSeer con Azure Active Directory (Azure AD).

La integración de TeamSeer con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a TeamSeer
- Puede permitir que los usuarios inicien sesión automáticamente en TeamSeer (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con TeamSeer, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en TeamSeer

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de TeamSeer desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-teamseer-from-the-gallery"></a>Incorporación de TeamSeer desde la galería
Para configurar la integración de TeamSeer en Azure AD, será preciso que agregue TeamSeer desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar TeamSeer desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **TeamSeer**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. En el panel de resultados, seleccione **TeamSeer** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con TeamSeer con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de TeamSeer para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario asociado de TeamSeer.

Para establecer la relación de vínculo, en TeamSeer, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con TeamSeer, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de TeamSeer](#creating-a-teamseer-test-user)**: para tener un homólogo de Britta Simon en TeamSeer vinculado a la representación del usuario de Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación TeamSeer.

**Para configurar el inicio de sesión único de Azure AD con TeamSeer, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **TeamSeer**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. En la sección de **TeamSeer Domain and URLs** (Dominio y direcciones URL de TeamSeer), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.teamseer.com/<companyid>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de TeamSeer](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) para obtener este valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. En la sección **TeamSeer Configuration** (Configuración de TeamSeer), haga clic en **Configure TeamSeer** (Configurar TeamSeer) para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de TeamSeer como administrador.

8. Vaya a **Administrador de RR. HH**.
   
    ![Administración de RR. HH.](./media/active-directory-saas-teamseer-tutorial/ic789634.png "Administración de RR. HH.")

9. Haga clic en **Configuración**.
   
    ![Instalación](./media/active-directory-saas-teamseer-tutorial/ic789635.png "Instalación")

10. Haga clic en **Configurar detalles del proveedor SAML**.
   
    ![Configuración de SAML](./media/active-directory-saas-teamseer-tutorial/ic789636.png "Configuración de SAML")

11. En la sección de detalles del proveedor SAML, lleve a cabo estos pasos:
   
    ![Configuración de SAML](./media/active-directory-saas-teamseer-tutorial/ic789637.png "Configuración de SAML")   

    a. Pegue el valor de **Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único) en el cuadro de texto **URL**.
          
    b. Abra el certificado codificado en base 64 en el bloc de notas, copie su contenido en el portapapeles y péguelo en el cuadro de texto **IdP Public Certificate** (Certificado público del proveedor de identidades).

12. Para completar la configuración del proveedor SAML, lleve a cabo estos pasos:
    
    ![Configuración de SAML](./media/active-directory-saas-teamseer-tutorial/ic789638.png "Configuración de SAML") 

    a. En las **Direcciones de correo electrónico de prueba**, escriba la dirección de correo electrónico del usuario de prueba. 
  
    b. En el cuadro de texto **Emisor** , escriba la dirección URL de emisor del proveedor de servicios. 
  
    c. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-teamseer-test-user"></a>Creación de un usuario de prueba de TeamSeer

Para permitir que los usuarios de Azure AD inicien sesión en TeamSeer, deben aprovisionarse en ShiftPlanning. En el caso de TeamSeer, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de **TeamSeer** como administrador.

2. Lleve a cabo los siguiente pasos:
   
    ![Administración de RR. HH.](./media/active-directory-saas-teamseer-tutorial/ic789640.png "Administración de RR. HH.")  
 
    a. Vaya a **Administrador de RR. HH. \> Usuarios**.
  
    b. Haga clic en **Ejecutar el Asistente para nuevos usuarios**.

3. En la sección **Detalles del usuario** , lleve a cabo estos pasos:
   
    ![Detalles del usuario](./media/active-directory-saas-teamseer-tutorial/ic789641.png "Detalles del usuario")

    a. Escriba el **Nombre**, **Apellido**, **Nombre de usuario (dirección de correo electrónico)** de una cuenta válida de AAD que desee aprovisionar en los cuadros de texto correspondientes.
  
    b. Haga clic en **Siguiente**.

4. Siga las instrucciones en pantalla para agregar un nuevo usuario y haga clic en **Finalizar**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TeamSeer ofrecida por TeamSeer para aprovisionar cuentas de usuario de Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a TeamSeer.

![Asignar usuario][200] 

**Para asignar a Britta Simon a TeamSeer, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **TeamSeer**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png

