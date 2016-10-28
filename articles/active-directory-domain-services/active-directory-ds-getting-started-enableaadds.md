<properties
	pageTitle="Servicios de dominio de Azure AD: habilitación de los Servicios de dominio de Azure AD | Microsoft Azure"
	description="Introducción a los Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/21/2016"
	ms.author="maheshu"/>

# Habilitación de Azure AD Domain Services

## Tarea 3: Habilitación de los Servicios de dominio de Azure AD
En esta tarea va a habilitar Azure AD Domain Services para su directorio. Realice los siguientes pasos de configuración para habilitar Azure AD Domain Services para su directorio.

1. Navegue hasta el **Portal de Azure clásico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. En el panel izquierdo, seleccione **Active Directory**.

3. Seleccione al inquilino (directorio) de Azure AD para el que quiere habilitar los Servicios de dominio de Azure AD.

    ![Selección de un directorio de Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Haga clic en la pestaña **Configure**.

    ![Pestaña Configurar del directorio](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Desplácese a una sección titulada **Servicios de dominio**.

    ![Sección de configuración de los Servicios de dominio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Mueva la opción de conmutación **Habilitar Servicios de dominio para este directorio** a **Sí**. Puede observar que en la página aparecen algunas opciones más de configuración para Azure AD Domain Services.

    ![Habilitación de los Servicios de dominio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Al habilitar Azure AD Domain Services para el inquilino, Azure AD genera y almacena los valores de hash de credenciales de Kerberos y NTLM que son necesarios para autenticar a los usuarios.

7. Especifique el **Nombre de dominio DNS de Servicios de dominio**.

   - De forma predeterminada, se selecciona el nombre de dominio predeterminado del directorio (es decir, que termina con el sufijo de dominio **. onmicrosoft.com**).

   - La lista contiene todos los dominios que se han configurado para el directorio de Azure AD, incluidos también los dominios comprobados y sin comprobar que configura en la pestaña 'Dominios'.

   - Además, puede agregar también un nombre de dominio personalizado a esta lista con solo escribirlo. En este ejemplo, hemos escrito un nombre de dominio personalizado 'contoso100.com'.

     > [AZURE.WARNING] Asegúrese de que el prefijo de dominio del nombre de dominio que especifique (por ejemplo, "contoso100" en el nombre de dominio 'contoso100.com') tenga menos de 15 caracteres. No se puede crear un dominio de Servicios de dominio de Azure AD con un prefijo de dominio de más de 15 caracteres.

8. El siguiente paso consiste en seleccionar una red virtual en la que quiere que los Servicios de dominio de Azure AD estén disponibles. Seleccione la red virtual que ha creado en la lista desplegable **Conectar Servicios de dominio a esta red virtual**.

   - Asegúrese de que la red virtual que ha especificado pertenece a una región de Azure compatible con los Servicios de dominio de Azure AD.

   - Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure AD Domain Services.

   - Las redes virtuales que pertenecen a una región donde no se admiten Azure AD Domain Services no aparecerán en la lista desplegable.

   - De igual forma, las redes virtuales que se crearon mediante Azure Resource Manager no aparecerán en la lista desplegable. Esto es porque las redes virtuales basadas en Resource Manager no son compatibles de momento con Azure AD Domain Services.

9. Asegúrese de que el nombre de dominio DNS que ha elegido para el dominio administrado no existe ya en la red virtual. Específicamente, compruebe si:

   - Ya tiene un dominio con el mismo nombre de dominio DNS en la red virtual.

   - Si la red virtual que ha seleccionado tiene una conexión VPN con la red local y tiene un dominio con el mismo nombre de dominio DNS en la red local.

   - Si ya dispone de un servicio en la nube con ese nombre en la red virtual.

10. Para habilitar Azure AD Domain Services, haga clic en **Guardar** en el panel de tareas de la parte inferior de la página.

11. La página muestra un estado "Pendiente..." mientras Azure AD Domain Services se habilita para su directorio.

    ![Habilitación de los Servicios de dominio: estado pendiente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Los Servicios de dominio de Azure AD proporcionan una alta disponibilidad para el dominio administrado. La primera vez que habilite Azure AD Domain Services para su dominio, observará que las direcciones IP en las que están disponibles los servicios de dominio en la red virtual se muestran de una en una. La segunda dirección IP se muestra poco después, en cuanto el servicio habilita la alta disponibilidad para el dominio. Cuando se configura la alta disponibilidad y está activa para su dominio, debe ver dos direcciones IP en la sección **Servicios de dominio** de la pestaña **Configurar**.

12. Al cabo de unos 20 o 30 minutos, verá la primera dirección IP en la que Domain Services están disponibles en la red virtual, en el campo **Dirección IP** de la página **Configurar**.

    ![Servicios de dominio habilitados: primera dirección IP aprovisionada](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Cuando la alta disponibilidad está operativa para el dominio, puede ver dos direcciones IP en la página. Estas son las direcciones IP en las que Azure AD Domain Services estarán disponibles en la red virtual seleccionada. Anote estas direcciones IP para que pueda actualizar la configuración de DNS de la red virtual. Este paso permite a las máquinas virtuales de la red virtual conectarse al dominio de cara para realizar operaciones como unirse a un dominio.

    ![Servicios de dominio habilitados: ambas direcciones IP aprovisionadas](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] En función del tamaño del inquilino de Azure AD (número de usuarios, grupos, etc.), el contenido del inquilino tarda un rato en estar disponible en Azure AD Domain Services. Este proceso de sincronización se produce en segundo plano. En el caso de inquilinos grandes con decenas de miles de objetos, pueden tardar uno o dos días en estar disponibles todos los usuarios, pertenencias a grupos y credenciales en Azure AD Domain Services.

<br>

## Tarea 4: Actualización de la configuración DNS para la red virtual de Azure
La siguiente tarea de configuración consiste en [actualizar la configuración de DNS para la red virtual de Azure](active-directory-ds-getting-started-dns.md).

<!---HONumber=AcomDC_0928_2016-->