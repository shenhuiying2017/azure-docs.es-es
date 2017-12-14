---
title: "Tutorial: Integración de Azure Active Directory con Compliance ELF | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Compliance ELF."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69c6efc3-54c7-49ec-b827-33177c09aa13
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/2/2017
ms.author: jeedes
ms.openlocfilehash: f05c43e2d8f6e777ae128e90040d8d5c270c212b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-compliance-elf"></a>Tutorial: Integración de Azure Active Directory con Compliance ELF

En este tutorial, aprenderá a integrar Compliance ELF con Azure Active Directory (Azure AD).

La integración de Compliance ELF con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Compliance ELF.
- Puede permitir que los usuarios inicien sesión automáticamente en Compliance ELF (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Compliance ELF, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Compliance ELF

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Compliance ELF de la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-compliance-elf-from-the-gallery"></a>Adición de Compliance ELF de la galería
Para configurar la integración de Compliance ELF en Azure AD, es preciso agregar Compliance ELF desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Compliance ELF desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Compliance ELF**, seleccione **Compliance ELF** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Compliance ELF en la lista de resultados](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Compliance ELF con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Compliance ELF para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Compliance ELF.

Para establecer la relación de vínculo, en Compliance ELF asigne el valor del **nombre de usuario** en Azure AD como el valor del **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Compliance ELF, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Compliance ELF](#create-a-compliance-elf-test-user)**: para tener un homólogo de Britta Simon en Compliance ELF que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Compliance ELF.

**Para configurar el inicio de sesión único de Azure AD con Compliance ELF, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Compliance ELF**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_samlbase.png)

3. En la sección **Dominio y direcciones URL de Compliance ELF**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información sobre dominio y direcciones URL de inicio de sesión único de Compliance ELF](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url.png)

    En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://sso.cordium.com`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Dominio y direcciones URL de inicio de sesión único de Compliance ELF](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.complianceelf.com`.
    
    > [!NOTE] 
    > Este valor no es real. Actualice estos valores con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Compliance ELF](mailto:support@complianceelf.com) para obtener este valor.

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-complianceelf-tutorial/tutorial_general_400.png)

6. Para generar la dirección URL de **Metadatos**, lleve a cabo los pasos siguientes:

    a. Haga clic en **Registros de aplicaciones**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_appregistrations.png)
   
    b. Haga clic en **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.  
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_endpointicon.png)

    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_endpoint.png)
     
    d. Ahora, vaya a la página de propiedades de **Compliance ELF** y copie el **Identificador de la aplicación** con el botón **Copiar** y péguelo en el Bloc de notas.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_appid.png)

    e. Genere la **Dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

7. Para configurar el inicio de sesión único en **Compliance ELF**, debe enviar la **URL de metadatos** al [equipo de atención al cliente de Compliance ELF](mailto:support@complianceelf.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
  
### <a name="create-a-compliance-elf-test-user"></a>Creación de un usuario de prueba de Compliance ELF

En esta sección, creará un usuario llamado Britta Simon en Compliance ELF. Póngase en contacto con el [equipo de soporte técnico de Compliance ELF](mailto:support@complianceelf.com) para agregar los usuarios en la plataforma de Compliance ELF. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Compliance ELF.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Compliance ELF, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Compliance ELF**.

    ![Vínculo a Compliance ELF en la lista de aplicaciones](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Compliance ELF en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación Compliance ELF.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_203.png

