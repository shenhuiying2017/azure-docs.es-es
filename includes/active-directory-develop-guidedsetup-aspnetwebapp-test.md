## <a name="test-your-code"></a>Prueba del código

Para probar la aplicación en Visual Studio, pulse **F5** para ejecutar el proyecto. El explorador se abre en la ubicación http://<span></span>localhost:{port}, donde verá el botón **Iniciar sesión con Microsoft**. Seleccione el botón para iniciar el proceso de inicio de sesión.

Cuando esté listo para realizar la prueba, use una cuenta profesional o educativa de Microsoft Azure Active Directory (Azure AD) o una cuenta de Microsoft personal (<span>live.</span>com, <span>outlook.</span>com) para iniciar sesión.

![Iniciar sesión con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Iniciar sesión en la cuenta de Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visualización de los resultados de la aplicación
Después de iniciar sesión, se redirige al usuario a la página principal del sitio web. Esta página principal es la dirección URL HTTPS especificada en la información de registro de la aplicación en el Portal de registro de aplicaciones de Microsoft. Asimismo, la página principal incluye un mensaje de bienvenida "Hello \<User>," un vínculo para cerrar la sesión y un vínculo para ver las notificaciones de usuario. El vínculo para las notificaciones de usuario conduce al controlador **Authorize** que creó anteriormente.

### <a name="browse-to-see-the-users-claims"></a>Ir a las notificaciones de usuario
Para ver las notificaciones de usuario, seleccione el vínculo para ir a la vista del controlador que solo está disponible para los usuarios autenticados.

#### <a name="view-the-claims-results"></a>Ver los resultados de las notificaciones
Una vez en la vista del controlador, verá una tabla que contiene las propiedades básicas del usuario:

|Propiedad |Valor |DESCRIPCIÓN |
|---|---|---|
|**Name** |Nombre completo del usuario | Nombre y apellidos del usuario.
|**Nombre de usuario** |usuario<span>@domain.com</span> | Nombre de usuario que se usa para identificar al usuario.
|**Asunto** |Asunto |Cadena que identifica al usuario de forma exclusiva en la web.|
|**Id. de inquilino** |Guid | **Guid** que representa de forma única la organización de Azure AD del usuario.|

Además, podrá ver una tabla con todas las notificaciones que se encuentran en la solicitud de autenticación. Para obtener más información, consulte la [lista de notificaciones que se encuentran en un token de identificación de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Probar el acceso a un método que tiene un atributo Authorize (opcional)
Para probar el acceso al controlador **Authorize** de las notificaciones de usuario como usuario anónimo, siga estos pasos:
1. Seleccione el vínculo para cerrar la sesión del usuario y complete el proceso de cierre de sesión.
2. Ya en el explorador, escriba http://<span></span>localhost:{port}/authenticated para obtener acceso al controlador que está protegido con el atributo **Authorize**.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados después de obtener acceso a un controlador protegido
Se le pedirá que se autentique para poder usar la vista del controlador protegido.

## <a name="additional-information"></a>Información adicional

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteger todo el sitio web
Para proteger todo el sitio web, vaya al archivo **Global.asax**, agregue el atributo **AuthorizeAttribute** al filtro **GlobalFilters** en el método **Application_Start**:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>Restringir el acceso de inicio de sesión a la aplicación
Las cuentas personales como outlook.com, live.com y similares pueden iniciar sesión en la aplicación de forma predeterminada. Asimismo, las cuentas profesionales o educativas de las organizaciones que están integradas con Azure AD también pueden iniciar sesión de forma predeterminada.

Existen varias opciones para restringir el acceso de los usuarios al proceso de inicio de sesión.

#### <a name="restrict-access-to-a-single-organization"></a>Restringir el acceso a una sola organización
Puede restringir el acceso al inicio de sesión de la aplicación a aquellas cuentas de usuario que solo formen parte de una sola organización de Azure AD:
1. En el archivo **web.config**, cambie el valor del parámetro **Tenant**. Cambie el valor de **Common** al nombre de inquilino de la organización como, por ejemplo, **contoso.onmicrosoft.com**.
2. A continuación, en la clase **OWIN Startup**, establezca el argumento **ValidateIssuer** en **true**.

#### <a name="restrict-access-to-a-list-of-organizations"></a>Restringir el acceso a una lista de organizaciones
Puede restringir el acceso de inicio de sesión únicamente a aquellas cuentas de usuario que formen parte de una organización de Azure AD que se encuentre en una lista de organizaciones permitidas:
1. En el archivo **web.config**, en la clase **OWIN Startup**, establezca el argumento **ValidateIssuer** en **true**.
2. Establezca el valor del parámetro **ValidIssuers** en la lista de organizaciones permitidas.

#### <a name="use-a-custom-method-to-validate-issuers"></a>Usar un método personalizado para validar emisores
Puede implementar un método personalizado para validar los emisores con el parámetro **IssuerValidator**. Para obtener más información sobre cómo usar este parámetro, puede encontrar más detalles en la [clase TokenValidationParameters](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) en MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
