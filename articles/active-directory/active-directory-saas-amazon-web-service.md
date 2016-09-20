<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Amazon Web Service (AWS) | Microsoft Azure"
	description="Aprenda a usar Amazon Web Service (AWS) con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# Tutorial: integración de Azure Active Directory con Amazon Web Service (AWS)

El objetivo de este tutorial es mostrar cómo integrar Amazon Web Services (AWS) con Azure Active Directory (Azure AD). La integración de Amazon Web Services (AWS) con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Amazon Web Service (AWS).
- Puede permitir que los usuarios inicien sesión automáticamente en Amazon Web Service (AWS) (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Amazon Web Service (AWS), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en Amazon Web Service (AWS)


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de tres bloques de creación principales:

1. Adición de Amazon Web Service (AWS) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Amazon Web Service (AWS) desde la galería
Para configurar la integración de Amazon Web Service (AWS) en Azure AD, es preciso agregar Amazon Web Service (AWS) desde la galería a la lista de aplicaciones SaaS administradas.

### Para agregar Amazon Web Service (AWS) desde la galería, realice los pasos siguientes:

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
   
	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Amazon Web Services (AWS)**.
   
	![Aplicaciones][5]

7. En el panel de resultados, seleccione **Amazon Web Services (AWS)** y, a continuación, haga clic en **Completar** para agregar la aplicación.

	![Aplicaciones][6]



##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Amazon Web Service (AWS) con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Amazon Web Service (AWS) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Amazon Web Services (AWS). Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Amazon Web Service (AWS).
 
Para configurar y probar el inicio de sesión único de Azure AD con Amazon Web Service (AWS), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en Amazon Web Service (AWS) que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación de Amazon Web Services (AWS). La aplicación Amazon Web Service (AWS) espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla le muestra un ejemplo de esto.


![Configurar inicio de sesión único][27]

**Para configurar el inicio de sesión único de Azure AD con Amazon Web Service (AWS), realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Amazon Web Services (AWS)**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][7]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Amazon Web Services (AWS)?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.

	![Configurar inicio de sesión único][8]

3. En la página de diálogo **Configurar las opciones de la aplicación**, haga clic en Siguiente.

	![Configurar las opciones de la aplicación][9]
 
4. En la página **Configuración de inicio de sesión único en Amazon Web Services (AWS)**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos localmente en el equipo.

	![Configurar inicio de sesión único][10]

5. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Amazon Web Service (AWS) como administrador.

6. Haga clic en **Página principal de la consola**.

	![Configurar inicio de sesión único][11]

7. Haga clic en **Administración de identidades y acceso**.

	![Configurar inicio de sesión único][12]

8. Haga clic en **Proveedores de identidades** y luego en **Create Provider** (Crear proveedor).

	![Configurar inicio de sesión único][13]

9. En la página de diálogo **Configure Provider** (Configurar proveedor), realice los pasos siguientes:

	![Configurar inicio de sesión único][14]

     a. En **Tipo de proveedor**, seleccione **SAML**.

     b. En el cuadro de texto **Provider Name** (Nombre de proveedor), escriba un nombre de proveedor (por ejemplo, *WAAD*).

     c. Haga clic en **Elegir archivo** para cargar el archivo de metadatos descargado.

     d. Haga clic en **Siguiente paso**.


10. En la página diálogo **Verify Provider Information** (Comprobar la información del proveedor), haga clic en **Crear**.

	![Configurar inicio de sesión único][15]

11. Haga clic en **Roles** y luego en **Crear nuevo rol**.

	![Configurar inicio de sesión único][16]

12. En el cuadro de diálogo **Set Role Name** (Establecer nombre de rol), realice los siguientes pasos:

	![Configurar inicio de sesión único][17]

	a. En el cuadro de texto **Nombre de rol**, escriba un nombre de role (p. ej.: *TestUser*).

	b. Haga clic en **Siguiente paso**.

13. En el cuadro de diálogo **Select Role Type** (Seleccionar tipo de rol), realice los siguientes pasos:

	![Configurar inicio de sesión único][18]

    a. Seleccione **Rol de acceso del proveedor de identidades**.

    b. En la sección **Conceder acceso de inicio de sesión único web (WebSSO) a los proveedores de SAML**, haga clic en **Seleccionar**.


14. En el cuadro de diálogo **Establecer confianza**, realice los siguientes pasos:

	![Configurar inicio de sesión único][19]
     
     a. Como proveedor de SAML, seleccione el proveedor de SAML que ha creado antes (p. ej.: *WAAD*).

     b. Haga clic en **Siguiente paso**.


15. En el cuadro de diálogo **Verify Role Trust** (Comprobar la confianza del rol), haga clic en **Siguiente paso**.

	![Configurar inicio de sesión único][32]


16. En el cuadro de diálogo **Attach Policy** (Adjuntar directiva), haga clic en **Siguiente paso**.

	![Configurar inicio de sesión único][33]


17. En el cuadro de diálogo **Revisar**, realice los pasos siguientes:

	![Configurar inicio de sesión único][34]

     a. Copie el valor de **ARN de rol**.

     b. Copie el valor de ARN de **Entidades de confianza**.

     c. Haga clic en **Crear rol**.

18. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

	![Qué es Azure AD Connect][20]

19. En la página **Confirmación de inicio de sesión único**, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

	![Qué es Azure AD Connect][22]


20. En el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.

	![Configurar inicio de sesión único][21]

21. Haga clic en **agregar atributo de usuario**.

	![Configurar inicio de sesión único][23]

22. En el cuadro de diálogo Agregar atributo de usuario, realice los pasos siguientes.

	![Configurar inicio de sesión único][24]

    a. En el cuadro de texto **Nombre de atributo**, escriba **https://aws.amazon.com/SAML/Attributes/Role**.

    b. En el cuadro de texto **Valor del atributo**, escriba **[el valor de ARN de rol], [el valor de ARN de la entidad de confianza]**.

    >[AZURE.TIP] Estos son los valores que se ha copiado del cuadro de diálogo de revisión cuando creó el rol.

    c. Haga clic en **Completar** para cerrar el cuadro de diálogo **Agregar atributo de usuario**.

23. Haga clic en **agregar atributo de usuario**.

	![Configurar inicio de sesión único][23]


24. En el cuadro de diálogo Agregar atributo de usuario, realice los pasos siguientes.

	![Configurar inicio de sesión único][25]


     a. En el cuadro de texto **Nombre de atributo**, escriba **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. En el cuadro de texto **Valor del atributo**, escriba o seleccione **user.userprincipalname** en la lista desplegable.
     
	![Configurar inicio de sesión único][35]
	

     c. Haga clic en **Completar** para cerrar el cuadro de diálogo **Agregar atributo de usuario**.


25. Haga clic en **Aplicar cambios**.

	![Configurar inicio de sesión único][26]





### Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)
 
4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)

  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
  3. Haga clic en Siguiente.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)

	a. En el cuadro de texto **Nombre**, escriba **Britta**.

	b. En el cuadro de texto **Apellidos**, escriba **Simon**.
  
	c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

	d. En la lista **Rol**, seleccione **Usuario**.
  
	e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)

	a. Anote el valor del campo **Nueva contraseña**.
  
	b. Haga clic en **Complete**.
  
 
### Creación de un usuario de prueba de Amazon Web Service (AWS)

El objetivo de esta sección es crear un usuario llamado Britta Simon en Amazon Web Service (AWS).

### Para crear un usuario llamado Britta Simon en Amazon Web Service (AWS), realice los pasos siguientes:

1. Inicie sesión en su sitio de la compañía de **Amazon Web Service (AWS)** como administrador.

2. Haga clic en el icono de **Página principal de la consola**.

	![Configurar inicio de sesión único][11]

3. Haga clic en Administración de identidades y acceso.

	![Configurar inicio de sesión único][28]

4. En el Panel, haga clic en Usuarios y luego, en Crear nuevos usuarios.

	![Configurar inicio de sesión único][29]

5. En el cuadro de diálogo Crear usuario, realice los pasos siguientes:

	![Configurar inicio de sesión único][30]

     a. En los cuadros de texto **Escribir nombres de usuario**, escriba el nombre de usuario de Brita Simon (userprincipalname) en Azure AD.

     b. Haga clic en **Crear**.




### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Amazon Web Service (AWS).

![Asignar usuario][31]

**Para asignar un usuario llamado Britta Simon a CloudPassage, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][26]

2. En la lista de aplicaciones, seleccione **Amazon Web Services (AWS)**.

	![Asignar usuario][27]

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][25]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][29]

### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso. Al hacer clic en el icono de Amazon Web Service (AWS) en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Amazon Web Service (AWS).


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png

<!---HONumber=AcomDC_0907_2016-->