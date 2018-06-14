---
title: 'Tutorial: Integración de Azure Active Directory con ContractWorks | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ContractWorks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e7b269d6-3c4e-4bc4-a55f-5071d1f52591
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 459b8f424a05f6332f6285f35645ec93c67403cf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340002"
---
# <a name="tutorial-azure-active-directory-integration-with-contractworks"></a>Tutorial: Integración de Azure Active Directory con ContractWorks

En este tutorial, aprenderá a integrar ContractWorks con Azure Active Directory (Azure AD).

Integrar ContractWorks con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ContractWorks.
- Puede permitir que los usuarios inicien sesión automáticamente en ContractWorks (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con ContractWorks, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en ContractWorks

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de ContractWorks desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-contractworks-from-the-gallery"></a>Adición de ContractWorks desde la galería
Para configurar la integración de ContractWorks en Azure AD, es preciso agregar ContractWorks desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ContractWorks desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **ContractWorks**, seleccione **ContractWorks** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![ContractWorks en la lista de resultados](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con ContractWorks con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ContractWorks para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ContractWorks.

Para configurar y probar el inicio de sesión único de Azure AD con ContractWorks, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ContractWorks](#create-a-contractworks-test-user)**: para tener un homólogo de Britta Simon en ContractWorks que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación ContractWorks.

**Para configurar el inicio de sesión único de Azure AD con ContractWorks, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **ContractWorks**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_samlbase.png)

3. En la sección **ContractWorks Domain and URLs** (Dominio y direcciones URL de ContractWorks), realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de ContractWorks](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_url.png)

    En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://login.securedocs.com/saml/metadata`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de ContractWorks](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL: `https://login.securedocs.com/saml/hint`
     
5. La aplicación ContractWorks espera que las aserciones SAML se encuentren en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-contractworks-tutorial/tutorial_ContractWorks_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ---------------| --------------- |
    | mail | user.mail |
    | DisplayName | user.displayname |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-contractworks-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-contractworks-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje **Espacio de nombres** en blanco.
    
    d. Haga clic en **Aceptar**.

7. En la sección **Certificado de firma de SAML**, haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-contractworks-tutorial/tutorial_metadataurl.png)
     
8. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-contractworks-tutorial/tutorial_general_400.png)

9. Para configurar el inicio de sesión único en **ContractWorks**, hay que enviar la **dirección URL de metadatos de federación de la aplicación** generada al [equipo de soporte técnico de ContractWorks](mailto:support@contractworks.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-contractworks-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-contractworks-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-contractworks-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-contractworks-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-contractworks-test-user"></a>Creación de un usuario de prueba de ContractWorks

En esta sección, va a crear un usuario llamado Britta Simon en ContractWorks. Trabaje con el [equipo de soporte técnico de ContractWorks](mailto:support@contractworks.com) para agregar los usuarios a la plataforma de ContractWorks. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a habilitar a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ContractWorks.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a ContractWorks, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ContractWorks**.

    ![Vínculo a ContractWorks en la lista de aplicaciones](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ContractWorks en el panel de acceso, debería iniciar sesión automáticamente en su aplicación ContractWorks.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_203.png

