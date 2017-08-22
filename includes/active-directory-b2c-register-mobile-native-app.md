[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registrar su aplicación móvil o nativa, use la configuración especificada en la tabla.

![Ejemplo de configuración de registro para la nueva aplicación móvil o nativa](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Configuración      | Valor de ejemplo  | Descripción                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name** | Aplicación B2C de Contoso | Escriba un **Nombre** para la aplicación que la describa a los consumidores. |
| **Cliente nativo** | Sí | Seleccione **Sí** para una aplicación móvil o nativa. |
| **URI de redireccionamiento personalizado** | `com.onmicrosoft.contoso.appname://redirect/path` | Escriba un URI de redirección con un esquema personalizado. Asegúrese de elegir un [buen identificador URI de redireccionamiento](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-native-application-redirect-uri) y no incluya caracteres especiales, como caracteres de subrayado. |

Haga clic en **Crear** para registrar la aplicación.

La aplicación recién registrada aparece en la lista de aplicaciones del inquilino B2C. Seleccione la aplicación móvil o nativa de la lista. Se muestra el panel de propiedades de la aplicación.

![Propiedades de la aplicación](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Tome nota del **Id. de cliente de aplicación** único. Use el identificador en el código de la aplicación.

Si la aplicación nativa llama a una API web protegida por Azure AD B2C, siga estos pasos:
   1. Cree un secreto de aplicación, para lo cual debe ir a la hoja **Claves** y hacer clic en el botón **Generar clave**. Anote el valor de la **Clave de la aplicación**. Use el valor como secreto de aplicación en el código de la aplicación.
   2. Haga clic en **Acceso de API**, en **Agregar** y seleccione la API web y los ámbitos (permisos).

> [!NOTE]
> Un **secreto de aplicación** es una credencial de seguridad importante y debe protegerse correctamente.
> 
