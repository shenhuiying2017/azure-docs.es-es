---
title: "Tutorial: integración de Azure Active Directory con Reflektive | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Reflektive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 799a08b9-1ce6-46d1-9064-aa9f36f6604e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: 7ce23d7940b99d50adf558dcd41e63c7e83b5f28
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-reflektive"></a>Tutorial: integración de Azure Active Directory con Reflektive

En este tutorial, obtendrá información sobre cómo integrar Reflektive con Azure Active Directory (Azure AD).

La integración de Reflektive con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Reflektive.
- Puede permitir que los usuarios inicien sesión automáticamente en Reflektive (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Reflektive, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Reflektive

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Reflektive desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-reflektive-from-the-gallery"></a>Adición de Reflektive desde la galería
Para configurar la integración de Reflektive en Azure AD, deberá agregar Reflektive desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Reflektive desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Reflektive**, seleccione **Reflektive** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Reflektive en la lista de resultados](./media/active-directory-saas-reflektive-tutorial/tutorial_reflektive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Reflektive con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Reflektive para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Reflektive.

Para establecer la relación de vínculo, en Reflektive, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Reflektive, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Reflektive](#create-a-reflektive-test-user)**: para tener un homólogo de Britta Simon en Reflektive que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Reflektive.

**Para configurar el inicio de sesión único de Azure AD con Reflektive, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Reflektive**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-reflektive-tutorial/tutorial_reflektive_samlbase.png)

3. En la sección **Dominio y direcciones URL de Reflektive**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por IDP:

    ![Información sobre dominio y direcciones URL de inicio de sesión único de Reflektive](./media/active-directory-saas-reflektive-tutorial/tutorial_reflektive_url.png)

    En el cuadro de texto **Identificador**, utilice una de las siguientes direcciones URL según confirmación del equipo de soporte técnico de Reflektive:
    | |
    |--|
    | `reflektive.com` |
    | `https://www.reflektive.com/saml/metadata` |

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información sobre dominio y direcciones URL de inicio de sesión único de Reflektive](./media/active-directory-saas-reflektive-tutorial/tutorial_reflektive_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL: `https://www.reflektive.com/app`

    > [!NOTE]
    > Para el modo SP necesita obtener el identificador de correo electrónico registrado con el [equipo de soporte técnico de Reflektive](https://support@reflektive.com). Cuando escriba el identificador en el cuadro de texto **Correo electrónico**, se habilitará la opción de inicio de sesión único.
         
5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-reflektive-tutorial/tutorial_reflektive_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-reflektive-tutorial/tutorial_general_400.png)

7. Para configurar el inicio de sesión único en **Reflektive**, necesita enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Reflektive](https://support@reflektive.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-reflektive-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-reflektive-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-reflektive-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-reflektive-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-reflektive-test-user"></a>Creación de un usuario de prueba Reflektive

En esta sección, se crea un usuario denominado Britta Simon en Reflektive. Trabaje con el [equipo de soporte técnico de Reflektive](mailto:support@reflektive.com) para agregar los usuarios a la plataforma de Reflektive. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Reflektive.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Reflektive, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Reflektive**.

    ![Vínculo a Reflektive en la lista de aplicaciones](./media/active-directory-saas-reflektive-tutorial/tutorial_reflektive_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Reflektive en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Reflektive.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-reflektive-tutorial/tutorial_general_203.png

