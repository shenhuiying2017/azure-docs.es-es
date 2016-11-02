<properties 
    pageTitle="Tutorial: configuración de Workday para la sincronización de entrada | Microsoft Azure" 
    description="Aprenda a usar la sincronización de entrada con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="04/06/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-configuring-workday-for-inbound-synchronization"></a>Tutorial: configuración de Workday para la sincronización de entrada
>[AZURE.NOTE]Azure Active Directory (AD) Premium está disponible para los clientes de China mediante la instancia internacional de Azure AD.    
Azure AD Premium no es compatible actualmente en el servicio de Microsoft Azure operado por 21Vianet en China.    

El objetivo de este tutorial es mostrar los pasos que se deben realizar en Workday y Microsoft Azure AD para importar contactos de Workday en Microsoft Azure AD.    
 En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:  

-   Una suscripción de Azure válida  
-   Un inquilino en Workday  

La situación descrita en este tutorial consta de los siguientes bloques de creación:  

1.  Habilitación de la integración de aplicaciones en Workday  
2.  Creación de un usuario del sistema de integración  
3.  Creación de un grupo de seguridad  
4.  Asignación del usuario del sistema de integración al grupo de seguridad  
5.  Configuración de las opciones del grupo de seguridad  
6.  Activación de cambios en directiva de seguridad  
7.  Configuración importación de usuarios en Microsoft Azure AD  

##<a name="enabling-the-application-integration-for-workday"></a>Habilitación de la integración de aplicaciones en Workday

El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Salesforce.    

###<a name="to-enable-the-application-integration-for-workday,-perform-the-following-steps:"></a>Siga estos pasos para habilitar la integración de aplicaciones para Workday:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.    

    ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  

2.  En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.    

3.  Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.    

    ![Aplicaciones](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Applications")  

4.  Para abrir la **Galería de aplicaciones**, haga clic en **Agregar una aplicación** y en **Agregar una aplicación que mi organización use**.    

    ![¿Qué desea hacer?](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "What do you want to do?")  

5.  En el **cuadro de búsqueda**, escriba **Workday**.    

    ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")  

6.  En el panel de resultados, seleccione **Workday** y haga clic en **Completar** para agregar la aplicación.    

    ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")  

##<a name="creating-an-integration-system-user"></a>Creación de un usuario del sistema de integración

1.  En el **Área de trabajo de Workday** escriba **crear usuario** en el cuadro de búsqueda y haga clic en el vínculo **Crear usuario del sistema de integración**.     

    ![crear usuario](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "create user")  

2.  Para completar la tarea Create Integration System User (Crear usuario del sistema de integración), especifique el nombre de usuario y la contraseña del nuevo usuario del sistema de integración.  Deje la opción Require New Password at Next Sign In (Solicitar una nueva contraseña en el siguiente inicio de sesión) sin activar, ya que este usuario iniciará sesión mediante programación.    
    Deje Session Timeout Minutes (Minutos de tiempo de espera de la sesión) en 0 (su valor predeterminado), con el fin de evitar que las sesiones del usuario agoten el tiempo de espera de manera prematura.    

    ![Create Integration System User](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Create Integration System User")  

##<a name="creating-a-security-group"></a>Creación de un grupo de seguridad

Para el escenario que se describe en este tutorial, es preciso crear un grupo de seguridad del sistema de integración sin restricciones y asignarle el usuario.    

1.  Escriba crear grupo de seguridad en el cuadro de búsqueda y, a continuación, haga clic en el vínculo Create Security Group (Crear grupo de seguridad).     

    ![Crear grupos de seguridad](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "CreateSecurity Group")  

2.  Complete la tarea Create Security Group (Crear grupo de seguridad).  En la lista desplegable Type of Tenanted Security Group (Tipo de grupo de seguridad con inquilinos), seleccione Integration System Security Group—Unconstrained (Grupo de seguridad del sistema de integración: sin restringir) para crear un grupo de seguridad al que se agregarán los miembros de forma explícita.     

    ![Crear grupos de seguridad](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "CreateSecurity Group")  

##<a name="assigning-the-integration-system-user-to-the-security-group"></a>Asignación del usuario del sistema de integración al grupo de seguridad

1.  Escriba editar grupo de seguridad en el cuadro de búsqueda y haga clic en el vínculo **Editar grupo de seguridad**.     

    ![Edit Security Group](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Edit Security Group")  

2.  Busque y seleccione el nuevo grupo de seguridad de integración por nombre    

    ![Editar grupo de seguridad](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Edit Security Group")  

3.  Agregue el nuevo usuario del sistema de integración al nuevo grupo de seguridad.       

    ![Grupo de seguridad del sistema](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "System Security Group")  

##<a name="configuring-security-group-options"></a>Configuración de las opciones del grupo de seguridad

En este paso, se conceden al nuevo grupo de seguridad permisos para las operaciones Get y Put en los objetos protegidos por las siguientes directivas de seguridad del dominio:  

-   External Account Provisioning  
-   Worker Data: Public Worker Reports  
-   Worker Data: All Positions  
-   Worker Data: Current Staffing Information  
-   Worker Data: Business Title on Worker Profile  

&nbsp;  

1.  Escriba directivas de seguridad de dominio en el cuadro de búsqueda y, a continuación, haga clic en el vínculo Domain Security Policies for Functional Area (Directivas de seguridad de dominio para área funcional).     

    ![Directivas de seguridad de dominio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Domain Security Policies")  

2.  Busque el sistema y seleccione el área funcional System (Sistema).  Haga clic en el botón OK (Aceptar).     

    ![Directivas de seguridad de dominio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Domain Security Policies")  

3.  En la lista de directivas de seguridad del área funcional System (Sistema), expanda Security Administration (Administración de seguridad) y seleccione la directiva de seguridad de dominio, External Account Provisioning.     

    ![Directivas de seguridad de dominio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Domain Security Policies")  

4.  Haga clic en el botón Edit Permissions (Editar permisos) y, a continuación, en la pantalla Edit Permissions (Editar permisos), agregue el nuevo grupo de seguridad a la lista de grupos de seguridad con permisos de integración de Get y Put.     

    ![Editar permisos](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Edit Permission")  

5.  Repita el paso 1 anterior para volver a la pantalla para seleccionar las áreas funcionales y esta vez busque personal, seleccione el área funcional Staffing (Personal) y haga clic en el botón OK (Aceptar).    

    ![Directivas de seguridad de dominio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Domain Security Policies")  

6.  En la lista de directivas de seguridad del área funcional Staffing (Personal), expanda Worker Data: Staffing y repita el paso 4 anterior en cada una de las restantes directivas de seguridad:    

    -   Worker Data: Public Worker Reports  
    -   Worker Data: All Positions  
    -   Worker Data: Current Staffing Information  
    -   Worker Data: Business Title on Worker Profile    

    ![Directivas de seguridad de dominio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Domain Security Policies")  

##<a name="activating-security-policy-changes"></a>Activación de cambios en directiva de seguridad

1.  Escriba activar en el cuadro de búsqueda y, a continuación, haga clic en el vínculo Activate Pending Security Policy Changes (Activar cambios en la directiva de seguridad pendientes).    

    ![Activar](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Activate")  

2.   Inicie la tarea Activate Pending Security Policy Changes (Activar cambios en la directiva de seguridad pendientes), para lo que debe escribir un comentario para fines de auditoría y, a continuación, hacer clic en el botón OK (Aceptar).      

    ![Activar seguridad pendiente](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Activate Pending Security")  

3.  Complete la tarea de la siguiente pantalla, para lo que debe activar la casilla Confirm (Confirmar) y hacer clic en el botón OK (Aceptar).     

    ![Activar seguridad pendiente](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Activate Pending Security")  

##<a name="configuring-user-import-in-microsoft-azure-ad"></a>Configuración importación de usuarios en Microsoft Azure AD

El objetivo de esta sección es describir cómo configurar Microsoft Azure AD para importar contactos de Workday.    

###<a name="to-configure-user-import-in-microsoft-azure-ad,-perform-the-following-steps:"></a>Para configurar la importación de usuarios en Microsoft Azure AD, siga estos pasos:

1.  En la página de integración de aplicaciones de **Workday**, haga clic en **Configurar importación de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento**.    

2.  En la página **Configuración y credenciales de administrador** , realice los pasos siguientes y luego haga clic en Siguiente:    

    ![Configuración y credenciales de administrador](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Settings and admin credentials")    

    1.  En el cuadro de texto **Nombre de usuario de administrador de Workday** , escriba el nombre del usuario que ha creado en la sección [Creación de un usuario del sistema de integración](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    2.  En el cuadro de texto **Contraseña de administrador de Workday** , escriba la contraseña del usuario que ha creado en la sección [Creación de un usuario del sistema de integración](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    3.  En el cuadro de texto **Dirección URL de inquilino de Workday** , escriba la dirección URL o el inquilino de Workday.    

3.  En la página **Probar conexión**, haga clic en **Iniciar prueba** para confirmar la conectividad y en **Siguiente**.    

    ![Probar conexión](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Test connection")  

4.  En la página **Opciones de aprovisionamiento**, haga clic en **Siguiente**.    

    ![Opciones de aprovisionamiento](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Provisioning options")  

5.  En el cuadro de diálogo **Iniciar aprovisionamiento**, haga clic en **Completar**.    

    ![Iniciar aprovisionamiento](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Start provisioning")  

Ahora puede ir a la sección **Usuarios** y comprobar si se importó el usuario de Workday.    



<!--HONumber=Oct16_HO2-->


