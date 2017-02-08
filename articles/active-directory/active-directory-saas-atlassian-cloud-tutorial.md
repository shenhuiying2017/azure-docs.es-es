---
title: "Tutorial: Integración de Azure Active Directory con Atlassian Cloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6d53de158a44116058ab8241d5182414f8f306fb
ms.openlocfilehash: 243e81ecd1e1648bfd6b2ca6e6268ea1a18d2036


---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integración de Azure Active Directory con Atlassian Cloud

En este tutorial, aprenderá a integrar Atlassian Cloud con Azure Active Directory (Azure AD).

La integración de Atlassian Cloud con Azure AD ofrece las ventajas siguientes:

- En Azure AD se puede controlar quién tiene acceso a Atlassian Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en Atlassian Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Atlassian Cloud se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Atlassian Cloud


>[!NOTE] 
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Atlassian Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-atlassian-cloud-from-the-gallery"></a>Incorporación de Atlassian Cloud desde la galería
Para configurar la integración de Atlassian Cloud en Azure AD, necesita agregar Atlassian Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Atlassian Cloud desde la galería, siga este procedimiento:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Atlassian Cloud**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_01.png)

7. En el panel de resultados, seleccione **Atlassian Cloud** y, después, haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Atlassian Cloud con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo de Atlassian Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Atlassian Cloud.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Atlassian Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con Atlassian Cloud, es necesario completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Atlassian Cloud](#creating-Atlassian-cloud-test-user)**: para tener un homólogo de Britta Simon en Atlassian Cloud que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Atlassian Cloud.


**Para configurar el inicio de sesión único de Azure AD con Atlassian Cloud, siga este procedimiento:**

1. En el portal clásico, en la página de integración de aplicaciones de **Atlassian Cloud**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
     
    ![Configurar inicio de sesión único][6] 

2. En la página **¿Cómo desea que los usuarios inicien sesión en Atlassian Cloud?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_03.png) 

3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_04.png) 

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en su aplicación Atlassian Cloud con el siguiente patrón: `https://<instancename>.atlassian.net`
    
    b. En el cuadro de texto **Identificador**, escriba la dirección URL con el siguiente patrón: `https://id.atlassian.com/login`

    >[!NOTE] 
    >Puede obtener el valor exacto del **identificador** desde la pantalla de configuración de SAML de Atlassian Cloud.

    c. click **Siguiente**
 
4. En la página **Configurar inicio de sesión único en Atlassian Cloud**, siga este procedimiento:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_05.png)

    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Para configurar el inicio de sesión único de la aplicación, inicie sesión en el portal de Atlassian mediante los derechos de administrador.

6. En la sección Autenticación del panel de navegación izquierdo, haga clic en **Dominios**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

    a. En el cuadro de texto, escriba el nombre de dominio y, a continuación, haga clic en **Agregar dominio**.
        
    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)

    b. Para comprobar el dominio, haga clic en **Comprobar**. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png)

    c. Descargue el archivo html de comprobación de dominio, cárguelo en la carpeta raíz del sitio web del dominio y, a continuación, haga clic en **Comprobar dominio**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)

    d. Una vez comprobado el dominio, el valor del campo **Estado** será **Comprobado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

7. En la barra de navegación de la izquierda, haga clic en **SAML**.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

8. Cree una nueva configuración de SAML y agregue la configuración del proveedor de identidades.

    a. Copie el valor de Identificador de entidad de Azure AD y péguelo en el campo Identificador de entidad del proveedor de identidades.
    
    b. Copie la dirección URL de inicio de sesión único de SAML y péguela en el cuadro de texto Dirección URL de inicio de sesión único de proveedor de identidades.

    c. Abra el certificado descargado de Azure AD en el bloc de notas y copie los valores sin las líneas de inicio y fin y péguelos en el cuadro de certificado Public X509.
    
    d. Guarde la configuración

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)
  
9. Actualice la configuración de Azure AD para asegurarse de que ha configurado la dirección URL de identificador correcta

    a. Copie el identificador de identidad de SP desde la pantalla SAML y péguelo en Azure AD como el valor del **identificador**.

    b. La dirección URL de inicio de sesión es la dirección URL del inquilino de Atlassian Cloud.   

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
10. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
 
    ![Inicio de sesión único de Azure AD ][11]


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.


![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

5. En el cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_05.png) 

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En el cuadro de diálogo **Perfil de usuario**, siga estos pasos: ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_06.png) 

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_08.png) 

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.   



### <a name="creating-an-atlassian-cloud-test-user"></a>Creación de un usuario de prueba de Atlassian Cloud


En esta sección, creará el usuario Britta Simon en Atlassian Cloud. Es importante que el usuario esté presente en Atlassian Cloud antes de realizar el inicio de sesión único. Inicie sesión en la instancia de Atlassian Cloud con derechos de administrador y realice los pasos siguientes.

>[!NOTE] 
>También puede crear los afortunados usuarios haciendo clic en el botón **Bulk Create** (Crear de forma masiva) en la sección Usuarios.

1. En la sección de administración del sitio, haga clic en el botón **Users** (Usuarios).

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Haga clic en el botón **Crear usuario** para crear un usuario en Atlassian Cloud

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Escriba la dirección de correo electrónico del usuario, el nombre de usuario y el nombre completo y asigne el acceso a la aplicación. 

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Haga clic en el botón **Crear usuario**, se enviará la invitación de correo electrónico al usuario y después de aceptar la invitación, el usuario estará activo en el sistema. 


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a habilitar a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Atlassian Cloud.

![Asignar usuario][200] 

**Para asignar Britta Simon a Atlassian Cloud, siga este procedimiento:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Atlassian Cloud**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_50.png) 

3. En el menú de la parte superior, haga clic en **Usuarios**.

    ![Asignar usuario][203]

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Atlassian Cloud en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Atlassian Cloud.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_205.png



<!--HONumber=Jan17_HO2-->


