---
title: "Tutorial: integración de Azure Active Directory con Litmos | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Litmos."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 2447cc5dbb01aa718e4418386499c6e50e9f5184
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Tutorial: Integración de Azure Active Directory con Litmos

En este tutorial, obtendrá información sobre cómo integrar Litmos con Azure Active Directory (Azure AD).

La integración de Litmos con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Litmos.
- Puede permitir que los usuarios inicien sesión automáticamente en Litmos (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Litmos, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Litmos

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Litmos desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-litmos-from-the-gallery"></a>Adición de Litmos desde la galería
Para configurar la integración de Litmos en Azure AD, deberá agregar Litmos desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Litmos desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Litmos**, seleccione **Litmos** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Litmos en la lista de resultados](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Litmos con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Litmos para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Litmos.

Para establecer la relación de vínculo, en Litmos, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Litmos, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Litmos](#create-a-litmos-test-user)**: para tener un homólogo de Britta Simon en Litmos que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Litmos.

**Para configurar el inicio de sesión único de Azure AD con Litmos, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Litmos**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_samlbase.png)

3. En la sección **Dominio y direcciones URL de Litmos**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Litmos](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.litmos.com/account/Login`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.litmos.com/integration/samllogin`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y dirección URL de respuesta reales, que se explican más adelante en el tutorial o póngase en contacto con el [equipo de soporte técnico de Litmos](https://www.litmos.com/contact-us/) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_certificate.png)

5. Como parte de la configuración, debe personalizar los **atributos de token de SAML** para la aplicación Litmos.

    ![Sección atributos](./media/active-directory-saas-litmos-tutorial/tutorial_attribute.png)
           
    | Nombre del atributo   | Valor de atributo |   
    | ---------------  | ----------------|
    | Nombre |user.givenname |
    | Apellidos  |user.surname |
    | Email |user.mail |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Agregar atributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_04.png)

    ![Cuadro de diálogo Agregar atributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.     

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-litmos-tutorial/tutorial_general_400.png)

7. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Litmos como administrador.

8. En la barra de navegación del lado izquierdo, haga clic en el icono de **cuentas**.
   
    ![Sección Cuentas en la aplicación][22] 

9. Haga clic en la pestaña **Integraciones** .
   
    ![Pestaña Integración][23] 

10. En la pestaña **Integraciones**, desplácese hacia abajo hasta **3rd Party Integrations (Integraciones de terceros)** y, después, haga clic en la pestaña **SAML 2.0**.
   
    ![Sección SAML 2.0][24] 

11. Copie el valor situado bajo **El punto de conexión de SAML para Litmos es:** y péguelo en el cuadro de texto **Dirección URL de respuesta** en la sección **Dominio y direcciones URL de Litmos** en Azure Portal. 
   
    ![Punto de conexión de SAML][26] 

12. En la aplicación **Litmos** , realice los pasos siguientes:
    
     ![Aplicación Litmos][25] 
     
     a. Haga clic en **Enable SAML**(Habilitar SAML).
    
     b. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado X.509 de SAML** .
     
     c. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-litmos-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
  
### <a name="create-a-litmos-test-user"></a>Creación de un usuario de prueba en Litmos

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Litmos.  
La aplicación Litmos admite el aprovisionamiento Just-in-Time. Esto significa que, si es necesario, se crea automáticamente una cuenta de usuario durante un intento de acceso a la aplicación mediante el panel de acceso.

**Para crear un usuario llamado Britta Simon en Litmos, realice los pasos siguientes:**

1. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Litmos como administrador.

2. En la barra de navegación del lado izquierdo, haga clic en el icono de **cuentas**.
   
    ![Sección Cuentas en la aplicación][22] 

3. Haga clic en la pestaña **Integraciones** .
   
    ![Pestaña Integraciones][23] 

4. En la pestaña **Integraciones**, desplácese hacia abajo hasta **3rd Party Integrations (Integraciones de terceros)** y, después, haga clic en la pestaña **SAML 2.0**.
   
    ![SAML 2.0][24] 
    
5. Seleccione **Autogenerate Users**(Generar usuarios automáticamente)
   
    ![Generar usuarios automáticamente][27] 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Litmos.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a Litmos, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Litmos**.

    ![Vínculo a Litmos en la lista de aplicaciones](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de Litmos en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Litmos. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png

