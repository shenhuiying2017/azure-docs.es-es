---
title: "Tutorial: Integración de Azure Active Directory con Veracode | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Veracode."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 270b373943ea72f1ddbee4f9c6fa4967ff85b861
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: integración de Azure Active Directory con Veracode

En este tutorial, obtendrá información sobre cómo integrar Veracode con Azure Active Directory (Azure AD).

La integración de Veracode con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Veracode.
- Puede permitir que los usuarios inicien sesión automáticamente en Veracode (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Veracode, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Veracode

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregación de Veracode desde la galería
2. Configuración y prueba del inicio de sesión único en Azure AD

## <a name="add-veracode-from-the-gallery"></a>Agregación de Veracode desde la galería
Para configurar la integración de Veracode en Azure AD, deberá agregar Veracode desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Veracode desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Veracode**, seleccione **Veracode** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Veracode en la lista de resultados](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Veracode con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Veracode para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Veracode.

Para establecer la relación de vínculo, en Veracode, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Veracode, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Veracode](#create-a-veracode-test-user)**: para tener un homólogo de Britta Simon en Veracode que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Veracode.

**Para configurar el inicio de sesión único de Azure AD con Veracode, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Veracode**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. En la sección **Dominio y direcciones URL de Veracode**, el usuario no tiene que realizar ningún paso ya que la aplicación se ha integrado previamente con Azure. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Veracode con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

    La aplicación Veracode espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** . La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "Atributos")

6. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    | Nombre del atributo | Valor de atributo |
    |--- |--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    a. En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "Atributos")
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "Atributos")
    
    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
    
    c. En el cuadro de texto **Valor de atributo** , seleccione el valor de atributo que se muestra para la fila.
    
    d. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. En la sección **Configuración de Veracode**, haga clic en **Configurar Veracode** para abrir la ventana **Configurar inicio de sesión**. Copie el **identificador de entidad de SAML** de la **sección de referencia rápida**.

    ![Configuración de Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Veracode como administrador.

10. En el menú de la parte superior, haga clic en **Configuración** y luego en **Administración**.
   
    ![Administración](./media/active-directory-saas-veracode-tutorial/ic802911.png "Administración")

11. Haga clic en la pestaña **SAML** .

12. En la sección **Configuración de SAML de organización** siga estos pasos:
   
    ![Administración](./media/active-directory-saas-veracode-tutorial/ic802912.png "Administración")
   
    a.  En el cuadro de texto **Issuer** (Emisor), pegue el valor de **Identificador de entidad de SAML** que ha copiado de Azure Portal.
    
    b. Para cargar el certificado descargado de Azure Portal, haga clic en **Choose file** (Elegir archivo).
   
    c. Seleccione **Habilitar registro automático**.

13. En la sección **Configuración de registro automático**, siga este procedimiento y, después, haga clic en **Guardar**:
   
    ![Administración](./media/active-directory-saas-veracode-tutorial/ic802913.png "Administración")
   
    a. Como **Activación de nuevo usuario**, seleccione **No se requiere ninguna activación**.
   
    b. Como **Actualizaciones de datos de usuario**, seleccione **Datos de usuario de Veracode de preferencia**.
   
    c. Para **Detalles de atributo de SAML**, seleccione lo siguiente:
      * **Roles de usuario**
      * **Administrador de directivas**
      * **Revisor**
      * **Principal de seguridad**
      * **Ejecutivo**
      * **Remitente**
      * **Creador**
      * **Todos los tipos de detección**
      * **Pertenencias a equipos**
      * **Equipo predeterminado**

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-veracode-test-user"></a>Creación de un usuario de prueba de Veracode
Para permitir que los usuarios de Azure AD inicien sesión en Veracode, deben aprovisionarse en Veracode. En el caso de Veracode, el aprovisionamiento es una tarea automatizada. No hay ningún elemento de acción para usted. Los usuarios se crean automáticamente si es necesario durante el primer intento de inicio de sesión único.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Veracode ofrecida por Veracode para aprovisionar cuentas de usuario de Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Veracode.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Veracode, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Veracode**.

    ![Vínculo a Veracode en la lista de aplicaciones](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Veracode en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Veracode.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png

