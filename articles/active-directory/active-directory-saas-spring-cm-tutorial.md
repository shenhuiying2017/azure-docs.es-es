---
title: "Tutorial: Integración de Azure Active Directory con SpringCM | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SpringCM."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.openlocfilehash: 7538c749b3fc31ad02e52ca986e43e3293e2fe20
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: integración de Azure Active Directory con SprinkCM

En este tutorial, obtendrá información sobre cómo integrar SpringCM con Azure Active Directory (Azure AD).

La integración de SpringCM con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SpringCM.
- Puede permitir que los usuarios inicien sesión automáticamente en SpringCM (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SpringCM, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SpringCM

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar SpringCM desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-springcm-from-the-gallery"></a>Agregar SpringCM desde la galería
Para configurar la integración de SpringCM en Azure AD, deberá agregar SpringCM desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SpringCM desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **SpringCM**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_search.png)

5. En el panel de resultados, seleccione **SpringCM** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SpringCM con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SpringCM para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SpringCM.

Para establecer la relación de vínculo, en SpringCM, asigne el valor del **nombre de usuario** de Azure AD como el valor del **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SpringCM, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SpringCM](#creating-a-springcm-test-user)**: para tener un homólogo de Britta Simon en SpringCM vinculado a la representación del usuario de Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación SpringCM.

**Para configurar el inicio de sesión único de Azure AD con SpringCM, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **SpringCM**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_samlbase.png)

3. En la sección **Dominio y direcciones URL de SpringCM**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de SpringCM](https://knowledge.springcm.com/support) para obtener este valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (sin procesar)** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de SpringCM**, haga clic en **Configurar SpringCM** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y Dirección URL del servicio de inicio de sesión único de SAML) de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_configure.png)   

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **SpringCM** como administrador.

8. En el menú de la parte superior, haga clic en**IR A**, haga clic en **Preferencias** y, luego, en la sección **Preferencias de cuenta**, haga clic en**SSO de SAML**.
   
    ![SSO DE SAML](./media/active-directory-saas-spring-cm-tutorial/ic797051.png "SSO DE SAML")

9. En la sección de configuración del proveedor de identidades, realice los pasos siguientes:
   
    ![Configuración del proveedor de identidades](./media/active-directory-saas-spring-cm-tutorial/ic797052.png "Configuración del proveedor de identidades")
    
    a. Para cargar el certificado descargado de Azure Active Directory, haga clic en **Seleccionar certificado de emisor** o **Cambiar el certificado del emisor**.
    
    b. Pegue el valor del **SAML Entity ID** (Identificador de entidad de SAML) que ha copiado de Azure Portal en el cuadro de texto **Issuer** (Emisor).
    
    c. Pegue la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal en el cuadro de texto **Service Provider (SP) Initiated Endpoint** (Punto de conexión del proveedor de servicios iniciado).
            
    d. Seleccione **SAML Enabled** (SAML habilitado) como **Enable** (Habilitar).

    e. Haga clic en **Guardar**.
 
> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-springcm-test-user"></a>Creación de un usuario de prueba de SpringCM

Para permitir que los usuarios de Azure Active Directory inicien sesión en SpringCM, deben aprovisionarse en SpringCM. En el caso de SpringCM, el aprovisionamiento es una tarea manual.

>[!NOTE]
>Para obtener más información, consulte [Create and Edit a SpringCM User](http://knowledge.springcm.com/create-and-edit-a-springcm-user) (Creación y edición de un usuario de SpringCM). 

**Para aprovisionar cuentas de usuario en SpringCM, realice los siguientes pasos:**

1. Inicie sesión en su sitio de compañía de **SpringCM** como administrador.

2. Haga clic en **GOTO** y, luego, en **ADDRESS BOOK** (LIBRETA DE DIRECCIONES).
   
    ![Creación de usuarios](./media/active-directory-saas-spring-cm-tutorial/ic797054.png "Creación de usuarios")

3. Haga clic en **Crear usuario**.

4. Seleccione un **Rol de usuario**.

5. Seleccione **Enviar correo electrónico de activación**.

6. Escriba el nombre, el apellido y la dirección de correo electrónico de una cuenta de usuario de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.

7. Agregue el usuario a un **Grupo de seguridad**.

8. Haga clic en **Save**.

  >[!NOTE]
  >Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SpringCM ofrecida por SpringCM para aprovisionar cuentas de usuario de AAD.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SpringCM.

![Asignar usuario][200] 

**Para asignar el usuario Britta Simon a SpringCM, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SpringCM**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.
 
Al hacer clic en el icono de SpringCM en el panel de acceso, debería iniciar sesión automáticamente en su aplicación SpringCM.

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_203.png

