---
title: "Tutorial: Integración de Azure Active Directory con Amazon Web Services (AWS) | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Amazon Web Services (AWS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 0fb9c8f428368271b548e3f174726fa01ea910c5
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: integración de Azure Active Directory con Amazon Web Service (AWS)

En este tutorial, obtendrá información sobre cómo integrar Amazon Web Services (AWS) con Azure Active Directory (Azure AD).

La integración de Amazon Web Services (AWS) con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Amazon Web Services (AWS).
- Puede permitir que los usuarios inicien sesión automáticamente en Amazon Web Services (AWS) (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Amazon Web Services (AWS), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Suscripción habilitada para el inicio de sesión único en Amazon Web Services (AWS)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Amazon Web Services (AWS) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adición de Amazon Web Services (AWS) desde la galería
Para configurar la integración de Amazon Web Services (AWS) en Azure AD, es preciso agregar Amazon Web Services (AWS) desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Amazon Web Services (AWS) desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Amazon Web Services (AWS)**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. En el panel de resultados, seleccione **Amazon Web Services (AWS)** y, después, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS) con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Amazon Web Services (AWS) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Amazon Web Services (AWS).

Esta relación de vínculo se establece al asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Amazon Web Services (AWS).

Para configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Amazon Web Services](#creating-an-amazon-web-services-test-user)**: para tener un homólogo de Britta Simon en Amazon Web Services (AWS) que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación de Amazon Web Services (AWS).

**Para configurar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación de **Amazon Web Services (AWS)**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. En la sección **Dominio y direcciones URL de Amazon Web Services (AWS)**, el usuario no tiene que realizar ningún paso ya que la aplicación se ha integrado previamente con Azure.

    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. La aplicación de software de Amazon Web Services (AWS) espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo  | Valor de atributo | Espacio de nombres |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rol            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Debe configurar el aprovisionamiento de usuarios en Azure AD para capturar todos los roles de la consola de AWS. Consulte los pasos de aprovisionamiento a continuación.

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila. Agregue el valor del espacio de nombres tal como se ha indicado anteriormente.
    
    d. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** para guardar la configuración en Azure.

    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Amazon Web Services (AWS) como administrador.

9. Haga clic en **Página principal de la consola**.
   
    ![Configurar inicio de sesión único][11]

10. Haga clic en **IAM** en el servicio **Security, Identity & Compliance** (Seguridad, identidad y cumplimiento).
   
    ![Configurar inicio de sesión único][12]

11. Haga clic en **Proveedores de identidades** y, después, en **Create Provider** (Crear proveedor).
   
    ![Configurar inicio de sesión único][13]

12. En la página de diálogo **Configure Provider** (Configurar proveedor), realice los pasos siguientes:
   
    ![Configurar inicio de sesión único][14]
 
    a. En **Tipo de proveedor**, seleccione **SAML**.

    b. En el cuadro de texto **Provider Name** (Nombre de proveedor), escriba un nombre de proveedor (por ejemplo, *WAAD*).

    c. Haga clic en **Elegir archivo**para cargar el archivo de metadatos descargado.

    d. Haga clic en **Siguiente paso**.

13. En la página de diálogo **Verify Provider Information** (Comprobar la información del proveedor), haga clic en **Crear**. 
    
    ![Configurar inicio de sesión único][15]

14. Haga clic en **Roles** y, después, en **Crear nuevo rol**. 
    
    ![Configurar inicio de sesión único][16]

15. En el cuadro de diálogo **Set Role Name** (Establecer nombre de rol), realice los siguientes pasos: 
    
    ![Configurar inicio de sesión único][17] 

    a. En el cuadro de texto **Nombre de rol**, escriba un nombre de rol (por ejemplo, *TestUser*). 

    b. Haga clic en **Siguiente paso**.

16. En el cuadro de diálogo **Select Role Type** (Seleccionar tipo de rol), realice los siguientes pasos: 
    
    ![Configurar inicio de sesión único][18] 

    a. Seleccione **Rol de acceso del proveedor de identidades**. 

    b. En la sección **Conceder acceso de inicio de sesión único web (WebSSO)** a los proveedores de SAML, haga clic en **Seleccionar**.

17. En el cuadro de diálogo **Establecer confianza** , realice los siguientes pasos:  
    
    ![Configurar inicio de sesión único][19] 

    a. Como proveedor de SAML, seleccione el proveedor de SAML que ha creado antes (por ejemplo, *WAAD*).
  
    b. Haga clic en **Siguiente paso**.

18. En el diálogo **Verify Role Trust** (Comprobar la confianza del rol), haga clic en **Paso siguiente**.
    
    ![Configurar inicio de sesión único][32]

19. En el diálogo **Attach Policy** (Adjuntar directiva), haga clic en **Paso siguiente**.
    
    ![Configurar inicio de sesión único][33]

20. En el cuadro de diálogo **Revisar** , realice los pasos siguientes:
    
    ![Configurar inicio de sesión único][34]
 
    a. Haga clic en **Crear rol**.

    b. Cree tantos roles como sea necesario y asígnelos al proveedor de identidades.

21. Configure ahora el aprovisionamiento de usuarios para capturar todos los roles de AWS.

    a. En la consola de AWS, regístrese con la cuenta raíz.

    b. En la esquina superior derecha, haga clic en su nombre y después haga clic en la opción **My Security Credentials** (Mis credenciales de seguridad). Se abrirá una pantalla como un mensaje de advertencia. Haga clic en el botón **Security Credentials** (Credenciales de seguridad) para pasar a la pantalla siguiente.
        
       ![Configurar inicio de sesión único][36]

       ![Configurar inicio de sesión único][37]

    c. En la sección de claves de acceso, haga clic en el botón **Create New Access Key** (Crear clave de acceso). Esto genera el identificador de clave de acceso y un valor de token.
    
       ![Configurar inicio de sesión único][38]

    d. Copie estos dos valores y descárguelos para no perderlos.

    e. En Azure Portal, en la página de integración de la aplicación de Amazon Web Services (AWS), haga clic en **Aprovisionamiento**.
        
       ![Configurar inicio de sesión único][35]

    f. Establezca el modo de aprovisionamiento en **Automático**.
        
       ![Configurar inicio de sesión único][39]

    g. Ahora, en los campos **clientsecret** y **Token secreto**, copie los valores correspondientes copiados de la consola de AWS.
    
    h. Puede hacer clic en el botón **Probar conexión** para probar la conectividad. Si la conexión es correcta, puede empezar con el aprovisionamiento del conector.
       
       ![Configurar inicio de sesión único][40]

    i. Ahora, habilite el Estado de aprovisionamiento en **Activado**. Esto permite capturar los roles de la aplicación.

       ![Configurar inicio de sesión único][41]

    > [!NOTE]
    > El aprovisionamiento de Azure AD se ejecuta después de un tiempo determinado para sincronizar los roles de AWS. Debería ver todos los roles de AWS adjuntos al proveedor de identidades en Azure AD, a fin de poder usarlos al asignar la aplicación a usuarios o grupos.

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-amazon-web-services-test-user"></a>Creación de un usuario de prueba de Amazon Web Services

Para permitir que los usuarios de Azure AD se registren en Amazon Web Services (AWS), es necesario aprovisionarlos en Amazon Web Services (AWS). En el caso de Amazon Web Services (AWS), el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de **Amazon Web Services (AWS)** como administrador.

2. Haga clic en el icono de **Página principal de la consola** . 
   
    ![Configurar inicio de sesión único][11]

3. Haga clic en Administración de identidades y acceso. 
   
    ![Configurar inicio de sesión único][28]

4. En el panel, haga clic en **Users** (Usuarios) y luego en **Create New Users** (Crear usuarios). 
   
    ![Configurar inicio de sesión único][29]

5. En el cuadro de diálogo Crear usuario, realice los pasos siguientes: 
   
    ![Configurar inicio de sesión único][30]   
    
    a. En los cuadros de texto **Escribir nombres de usuario** , escriba el nombre de usuario de Brita Simon (userprincipalname) en Azure AD.

    b. Haga clic en **Create** (Crear).
        
### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Amazon Web Services (AWS).

![Asignar usuario][200] 

**Para asignar Britta Simon a Amazon Web Services (AWS), realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Amazon Web Services (AWS)**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. En la pestaña **Seleccionar rol**, seleccione el rol apropiado para el usuario. Todos estos roles se muestran con el nombre de rol y el nombre del proveedor de identidades. De esta forma, puede identificar con facilidad los roles de AWS.

8. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Amazon Web Services (AWS) en el panel de acceso, automáticamente iniciará sesión en su aplicación de Amazon Web Services (AWS). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
