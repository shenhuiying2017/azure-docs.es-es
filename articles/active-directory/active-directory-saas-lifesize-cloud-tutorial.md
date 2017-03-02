---
title: "Tutorial: Integración de Azure Active Directory con Lifesize Cloud | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Lifesize Cloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 8edbee8e554a7818b97669a4bb64b31ada67b1a5
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integración de Azure Active Directory con Lifesize Cloud
En este tutorial, aprenderá a integrar Lifesize Cloud con Azure Active Directory (Azure AD).

La integración de Lifesize Cloud con Azure AD ofrece las ventajas siguientes:

* En Azure AD se puede controlar quién tiene acceso a Lifesize Cloud.
* Puede permitir que los usuarios inicien sesión automáticamente en el inicio de sesión único (SSO) de Lifesize Cloud con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Lifesize Cloud se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único (SSO) en Lifesize Cloud

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

* Agregar Lifesize Cloud desde la galería
* Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-lifesize-cloud-from-the-gallery"></a>Incorporación de Lifesize Cloud desde la galería
Para configurar la integración de Lifesize Cloud en Azure AD, necesita agregar Lifesize Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Lifesize Cloud desde la galería, siga este procedimiento:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Lifesize Cloud**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)
7. En el panel de resultados, seleccione **Lifesize Cloud** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Lifesize Cloud con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo de Lifesize Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Lifesize Cloud.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Lifesize Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con Lifesize Cloud, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Crear un usuario de prueba en Lifesize Cloud](#creating-a-lifesize-cloud-test-user)**: para tener un homólogo de Britta Simon en Lifesize Cloud que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el SSO de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Lifesize Cloud.

**Para configurar el inicio de sesión único de Azure AD con Lifesize Cloud, siga este procedimiento:**

1. En el portal clásico, en la página de integración de aplicaciones de **Lifesize Cloud**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Lifesize Cloud?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png)   
  1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación Lifesize Cloud con el patrón siguiente: **https://login.lifesizecloud.com/ls/?acs**.
  2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en Lifesize Cloud**, siga este procedimiento:
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)
   1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   2. Haga clic en **Siguiente**.
5. Para configurar SSO para su aplicación, inicie sesión en la aplicación de Lifesize Cloud con privilegios de administrador.
6. En la esquina superior derecha, haga clic en su nombre y, después, en **Configuración avanzada**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)
7. En Configuración avanzada, haga clic en el vínculo **Configuración de SSO**. Se abrirá la página de configuración de SSO de la instancia.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)
8. Ahora, configure los valores siguientes en la interfaz de configuración de SSO.    
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
  1. Copie el valor de URL del emisor de Azure AD y péguelo en el cuadro de texto **Emisor del proveedor de identidades**. 
  2. Copie el valor de Dirección URL de inicio de sesión remoto de Azure AD y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.   
  3. Abra el certificado descargado en el Bloc de notas, copie el contenido de certificado (excepto las líneas Begin Certificate y End Certificate) y péguelo en el cuadro de texto **Certificado X.509**.
  4. En la asignación de atributos de SAML, en el cuadro de texto **Nombre**, escriba el valor **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  5. En la asignación de atributos de SAML, en el cuadro de texto **Apellidos**, escriba el valor **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
  6. En la asignación de atributos de SAML, en el cuadro de texto **Correo electrónico**, escriba el valor **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
9. Para comprobar la configuración, puede hacer clic en el botón **Probar**.
   
   >[!NOTE]
   >Para realizar una prueba correctamente, necesita completar el asistente para configuración en Azure AD y, además, proporcionar acceso los usuarios o grupos que pueden realizar la prueba.
   >  
10. Para habilitar SSO, seleccione el botón **Habilitar SSO**.
11. Ahora, haga clic en el botón **Actualizar** para guardar la configuración. Se generará el valor RelayState. Copie el valor RelayState generado en el cuadro de texto. Necesitará este valor en los pasos siguientes.
12. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
   ![Inicio de sesión único de Azure AD ][10]
13. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]
    
**Para iniciar sesión en el Portal de administración de Azure:**

1. Inicie sesión en **https://portal.azure.com** usando credenciales de administrador.
2. Haga clic en el vínculo **Más servicios** del panel de navegación izquierdo.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)
3. Busque Azure Active Directory y haga clic en el vínculo **Azure Active Directory**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)
4. Encontrará todas sus aplicaciones SaaS debajo del botón **Aplicaciones empresariales**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)
5. Ahora, haga clic en el vínculo **Todas las aplicaciones** de la hoja siguiente.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)
6. Busque la aplicación de Lifesize para la que quiera configurar RelayState. 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)
7. Ahora, haga clic en el vínculo **Inicio de sesión único** en la hoja.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)
8. Verá la casilla **Mostrar configuración avanzada de URL**. Haga clic en la casilla.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
9. Ahora, configure el RelayState para la aplicación, que verá en la página de configuración de SSO de la aplicación de Lifesize. 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)
10. Guarde la configuración

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 
   1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 
   1. En el cuadro de texto **Nombre**, escriba **Britta**.   
   2. En el cuadro de texto **Apellidos**, escriba **Simon**.
   3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   4. En la lista **Rol**, seleccione **Usuario**.
   5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 
    1. Anote el valor del campo **Nueva contraseña**.
    2. Haga clic en **Completo**.   

### <a name="create-an-lifesize-cloud-test-user"></a>Creación de un usuario de prueba de Lifesize Cloud
En esta sección, creará el usuario Britta Simon en Lifesize Cloud. Lifesize Cloud no admite el aprovisionamiento de usuarios automático. Después de autenticarse correctamente en Azure AD, el usuario se aprovisionará automáticamente en la aplicación. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, va a habilitar a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Lifesize Cloud.

![Asignar usuario][200] 

**Para asignar Britta Simon a Lifesize Cloud, siga este procedimiento:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Lifesize Cloud**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
En esta sección, probará la configuración de SSO de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Lifesize Cloud en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Lifesize Cloud.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

