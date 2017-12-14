---
title: "Tutorial: Integración de Azure Active Directory con BenefitHub | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y BenefitHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4069fe32-a452-463f-973e-7aa0baa4c2fa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: 1494666f63caefa53deddd7cd40567b96c5d5f40
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-benefithub"></a>Tutorial: Integración de Azure Active Directory con BenefitHub

En este tutorial, obtendrá información sobre cómo integrar BenefitHub con Azure Active Directory (Azure AD).

Integrar BenefitHub con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a BenefitHub
- Puede permitir que los usuarios inicien sesión automáticamente en BenefitHub (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con BenefitHub, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en BenefitHub

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar BenefitHub desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-benefithub-from-the-gallery"></a>Agregar BenefitHub desde la galería
Para configurar la integración de BenefitHub en Azure AD, deberá agregar BenefitHub desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar BenefitHub desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **BenefitHub**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_search.png)

5. En el panel de resultados, seleccione **BenefitHub** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con BenefitHub con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de BenefitHub para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de BenefitHub.

Para establecer la relación de vínculo, en BenefitHub, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con BenefitHub, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de BenefitHub](#creating-a-benefithub-test-user)**: para tener un homólogo de Britta Simon en LBenefitHub que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación BenefitHub.

**Para configurar el inicio de sesión único de Azure AD con BenefitHub, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **BenefitHub**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_samlbase.png)

3. En la sección de **Dominio y direcciones URL de BenefitHub**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_url1.png)
  
    a. En el cuadro de texto **Identificador**, escriba: `urn:benefithub:passport`
    
    b. En el cuadro de texto **URL de respuesta**, escriba: `https://passport.benefithub.info/saml/post/ac`

4. La aplicación BenefitHub espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_attribute.png)

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |    
    | organizationid | < identificador de organización > |

    > [!NOTE]
    > Este valor de atributo no es real. Actualícelo con el identificador de organización real. Póngase en contacto con el [equipo de soporte técnico de BenefitHub](https://www.benefithub.com/Home/ContactUs) para obtener el identificador real.
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

    > [!NOTE] 
    > Antes de configurar la aserción SAML, debe ponerse en contacto con el [equipo de soporte técnico de BenefitHub](https://www.benefithub.com/Home/ContactUs) y solicitar el valor del atributo de identificador único para el inquilino. Necesitará este valor para configurar la notificación personalizada para su aplicación.

6. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_general_400.png)

8. Para configurar el inicio de sesión único en el lado de **BenefitHub**, es preciso enviar los datos descargados de **XML de metadatos** al [equipo de soporte técnico de BenefitHub](https://www.benefithub.com/Home/ContactUs). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-benefithub-test-user"></a>Creación de un usuario de prueba de BenefitHub

En esta sección, creará un usuario llamado Britta Simon en BenefitHub. Trabaje con el [equipo de soporte técnico de BenefitHub](https://www.benefithub.com/Home/ContactUs) para agregar usuarios a la plataforma de BenefitHub. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a BenefitHub.

![Asignar usuario][200] 

**Para asignar a Britta Simon a BenefitHub, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **BenefitHub**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de BenefitHub en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación BenefitHub.
Para más información sobre el Panel de acceso, vea la [introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_203.png

