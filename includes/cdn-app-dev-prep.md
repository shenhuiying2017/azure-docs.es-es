## Requisitos previos

Antes de escribir el código de administración de la red CDN, tenemos que realizar algunos preparativos para habilitar el código para que interactúe con Azure Resource Manager. Para ello, necesitará lo siguiente:

* Crear un grupo de recursos que contendrá el perfil de CDN que crearemos en este tutorial
* Configurar Azure Active Directory para que proporcione autenticación para la aplicación
* Aplicar permisos al grupo de recursos para que solo los usuarios autorizados de nuestro inquilino de Azure AD puedan interactuar con el perfil de CDN

### Creación del grupo de recursos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic sucesivamente en el botón **Nuevo** de la parte superior izquierda, en **Administración** y en **Grupo de recursos**.
	
	![Crear un grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Llame a su grupo de recursos *CdnConsoleTutorial*. Seleccione la suscripción y elija una ubicación cercana. Si lo desea, puede hacer clic en la casilla **Anclar al panel** para anclar el grupo de recursos al panel en el portal. Así resultará más fácil encontrarlo. Cuando termine las selecciones, haga clic en **Crear**.

	![Asignar un nombre al grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Una vez creado el grupo de recursos, si no lo ancló al panel, para buscarlo, haga clic en **Examinar** y en **Grupos de recursos**. Haga clic en el grupo de recursos para abrirlo. Tome nota del valor en **Id. de suscripción**. lo necesitará más adelante.

	![Asignar un nombre al grupo de recursos](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### Creación de la aplicación de Azure AD y aplicación de los permisos

Existen dos enfoques para la autenticación de la aplicación con Azure Active Directory: usuarios individuales o una entidad de servicio. Una entidad de servicio se parece a una cuenta de servicio en Windows. En lugar de conceder a usuarios concretos permisos para interactuar con los perfiles de CDN, concedemos los permisos a la entidad de servicio. Las entidades de servicio se suelen utilizar para procesos automatizados no interactivos. Aunque en este tutorial escribimos una aplicación de consola interactiva, nos centraremos en el enfoque de entidad de servicio.

Para crear una entidad de servicio, se siguen varios pasos, incluida la creación de una aplicación de Azure Active Directory. Para ello, vamos a [completar este tutorial](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Asegúrese de seguir los pasos descritos en el [tutorial vinculado](../articles/resource-group-create-service-principal-portal.md). Es *muy importante* que lo complete exactamente como se describe. No se olvide de tomar nota del **identificador de inquilino**, el **nombre de dominio del inquilino** (por lo general, un dominio *.onmicrosoft.com*, a menos que haya especificado uno personalizado), el **identificador de cliente** y la **clave de autenticación de cliente**, ya que los necesitaremos más adelante. Tenga cuidado de proteger el **identificador de cliente** y la **clave de autenticación de cliente**, ya que cualquiera puede usar estas credenciales para ejecutar operaciones como la entidad de servicio.
> 	
> Cuando llegue al paso denominado [Configuración de aplicación multiinquilino](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), seleccione **No**.
> 
> Cuando llegue al paso [Asignación de aplicación a un rol](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), use el grupo de recursos que creamos antes, *CdnConsoleTutorial*, pero en lugar del rol **Lector**, asigne el rol **CDN Profile Contributor** (Colaborador de perfil de CDN). Después de asignar la aplicación al rol **CDN Profile Contributor** (Colaborador de perfil de CDN) en el grupo de recursos, vuelva a este tutorial.

Una vez creada la entidad de servicio y asignado el rol **CDN Profile Contributor** (Colaborador de perfil de CDN), la hoja **Usuarios** del grupo de recursos debería parecerse a la siguiente.

![Hoja Usuarios](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### Autenticación interactiva de usuarios

Si, en lugar de una entidad de servicio, prefiere la autenticación interactiva de usuario individual, el proceso se parece mucho al usado con una entidad de servicio. De hecho, debe seguir el mismo procedimiento, con algunos cambios menores.

> [AZURE.IMPORTANT] Siga estos pasos únicamente si opta por usar la autenticación de usuario individual en lugar de una entidad de servicio.

1. Al crear la aplicación, en lugar de **Aplicación web**, elija **Aplicación nativa**.
	
	![Aplicación nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
	
2. En la página siguiente, se le pedirá un **URI de redirección**. No se validará el URI, pero debe recordarlo. Lo necesitará más adelante.

3. No hay necesidad de crear una **clave de autenticación de cliente**.

4. En lugar de asignar una entidad de servicio al rol **CDN Profile Contributor** (Colaborador de perfil de CDN), vamos a asignar usuarios individuales o grupos. En este ejemplo, puede ver que hemos asignado *Usuario de demostración de CDN* al rol **CDN Profile Contributor** (Colaborador de perfil de CDN).
	
	![Acceso de usuario individual](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

<!---HONumber=AcomDC_0706_2016-->