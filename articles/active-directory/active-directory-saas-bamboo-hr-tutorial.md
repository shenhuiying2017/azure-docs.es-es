---
title: "Tutorial: Integración de Azure Active Directory con BambooHR | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y BambooHR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 081144a645683d4d00ed0d464e23558378dc1b38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Tutorial: Integración de Azure Active Directory con BambooHR

En este tutorial, obtendrá información sobre cómo integrar BambooHR con Azure Active Directory (Azure AD).

La integración de BambooHR con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a BambooHR.
- Puede permitir que los usuarios inicien sesión automáticamente en BambooHR mediante el inicio de sesión único (SSO) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con BambooHR, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de BambooHR habilitada para el inicio de sesión único

> [!NOTE]
> No se recomienda usar un entorno de producción para probar los pasos de este tutorial.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión gratuita durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de BambooHR desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-bamboohr-from-the-gallery"></a>Incorporación de BambooHR desde la galería
Para configurar la integración de BambooHR en Azure AD, agregue BambooHR desde la galería a la lista de aplicaciones SaaS administradas haciendo lo siguiente:

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Panel Aplicaciones empresariales][2]
    
3. Para agregar una aplicación, seleccione **Nueva aplicación**.

    ![Botón “Nueva aplicación”][3]

4. En el cuadro de búsqueda, escriba **BambooHR**. En la lista de resultados, seleccione **BambooHR**  y, a continuación, seleccione **Agregar**.

    ![BambooHR en la lista de resultados](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único (SSO) de Azure AD con BambooHR mediante un usuario de prueba llamado "Britta Simon".

Para que SSO funcione, Azure AD necesita saber cuál es su usuario equivalente en BambooHR. Es decir, debe establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de BambooHR.

Para establecer la relación de vínculo en BambooHR, asigne el valor **nombre de usuario** de Azure AD como valor de **nombre de usuario** de BambooHR.

Para configurar y probar el inicio de sesión único de Azure AD con BambooHR, es preciso completar los bloques de creación que se detallan en las siguientes cinco secciones.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación BambooHR de la siguiente manera:

1. En Azure Portal, en la página de integración de la aplicación **BambooHR**, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En la ventana **Inicio de sesión único**, en la lista del cuadro desplegable **Modo**, seleccione **SAML-based Sign-on** (Inicio de sesión basado en SAML).
 
    ![Ventana de inicio de sesión único](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. En **Dominio y direcciones URL de BambooHR**, haga lo siguiente:

    ![La sección Dominio y direcciones URL de BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. En el cuadro **Sign on URL** (URL de inicio de sesión), escriba una dirección URL con el siguiente patrón: `https://<company>.bamboohr.com`.

    b. En el cuadro **Identificador**, escriba un valor: `BambooHR-SAML`.

    > [!NOTE] 
    > El valor de la **dirección URL de inicio de sesión** no es real. Actualícelo con su dirección de URL de inicio de sesión real. Para obtener el valor, póngase en contacto con el [equipo de soporte técnico de cliente de BambooHR](https://www.bamboohr.com/contact.php). 
 
4. En **Certificado de firma de SAML**, seleccione **Certificado (Base64)** y, luego, guarde el archivo del certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Seleccione **Guardar**.

    ![Botón Guardar](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. En **Configuración de BambooHR**, seleccione **Configurar BambooHR** para abrir la ventana **Configurar inicio de sesión**. En la sección **Referencia rápida**, copie los valores de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML).

    ![Configuración de BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. En otra ventana, inicie sesión como administrador en el sitio de la compañía de BambooHR.

8. En la página principal, haga lo siguiente:
   
    ![Página de inicio de sesión único de BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Inicio de sesión único")   

    a. Seleccione **Aplicaciones**.
   
    b. En el panel **Aplicaciones**, seleccione **Single Sign-On** (Inicio de sesión único).
   
    c. Seleccione **SAML Single Sign-On** (Inicio de sesión único de SAML).

9. En el panel **SAML Single Sign-On** (Inicio de sesión único de SAML), haga lo siguiente:
   
    ![El panel SAML Single Sign-O](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")
   
    a. En el cuadro **SSO Login URL** (URL de inicio de sesión de SSO), pegue la **dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal en el paso 6.
      
    b. En el Bloc de notas, abra el certificado codificado en Base 64 que descargó de Azure Portal, copie el contenido y, luego, péguelo en el cuadro de texto **X.509 Certificate** (Certificado X.509).
   
    c. Seleccione **Guardar**.

> [!TIP]
> Mientras configura la aplicación, puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com). Después de agregar la aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Single Sign-On** (Inicio de sesión único) y, a continuación, acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Para información, consulte la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Azure Portal.

   ![Creación del usuario de prueba de Azure AD Britta Simon][100]

Haga lo siguiente para crear un usuario de prueba en Azure AD:

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, seleccione **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. En la parte superior del panel **Todos los usuarios**, seleccione **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. En la ventana **Usuario**, haga lo siguiente:

    ![Ventana Usuario](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-bamboohr-test-user"></a>Creación de un usuario de prueba de BambooHR

Para permitir que los usuarios de Azure AD inicien sesión en BambooHR, configúrelos manualmente en BambooHR haciendo lo siguiente:

1. Inicie sesión en su sitio de **BambooHR** como administrador.

2. En la barra de herramientas en la parte superior, seleccione **Configuración**.
   
    ![El botón Configuración](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuración")

3. Seleccione **Información general**.

4. En el panel izquierdo, seleccione **Seguridad** > **Usuarios**.

5. Escriba el nombre de usuario, la contraseña y la dirección de correo electrónico de la cuenta válida de Azure AD que desee configurar.

6. Seleccione **Guardar**.
        
>[!NOTE]
>Para configurar cuentas de usuario de Azure AD, también puede utilizar las herramientas de creación de cuentas de usuario de BambooHR o las API.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

Permita que el usuario Britta Simon use el inicio de sesión único de Azure, para lo cual le concederá acceso a BambooHR.

![Asignación de rol de usuario][200] 

Para asignar el usuario Britta Simon a BambooHR, haga lo siguiente:

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista del directorio y seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista **Aplicaciones empresariales**, seleccione**BambooHR**.

    ![Vínculo a BambooHR en la lista de aplicaciones empresariales](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. En el panel izquierdo, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione el botón **Agregar** y, después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En la ventana **Usuarios y grupos**, en la lista **Usuarios**, seleccione **Britta Simon**.

6. Seleccione el botón **Seleccionar**.

7. En el panel **Agregar asignación**, seleccione el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

Pruebe la configuración de inicio de sesión único de Azure AD con el Panel de acceso.

Al seleccionar el icono de **BambooHR** en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación BambooHR.

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

