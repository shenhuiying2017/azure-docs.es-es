<properties 
	pageTitle="Introducción a la autenticación personalizada | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo autenticar usuarios con un nombre de usuario y una contraseña." 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="mahender"/>

# Introducción a la autenticación personalizada

En este tema se muestra cómo autenticar usuarios en el backend .NET de Servicios móviles de Azure emitiendo su propio token de autenticación para Servicios móviles. En este tutorial, agregará autenticación al proyecto de inicio rápido mediante un nombre de usuario y una contraseña personalizados para la aplicación.

>[AZURE.NOTE] En este tutorial se muestra un método avanzado para autenticar sus servicios móviles con credenciales personalizadas. En muchas aplicaciones, sin embargo, será más adecuado usar los proveedores de identidades sociales integrados para permitir a los usuarios que inicien sesión a través de Facebook, Twitter, Google, una cuenta Microsoft y Azure Active Directory. Si esta es la primera vez que usa autenticación en Servicios móviles, consulte el tutorial [Introducción a los usuarios].

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Configuración de la tabla de cuentas]
2. [Creación del extremo de registro]
3. [Creación del proveedor de inicio de sesión]
4. [Creación del extremo de inicio de sesión]
5. [Configuración del servicio móvil para exigir autenticación]
6. [Prueba del flujo de inicio de sesión con un cliente de prueba]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles]. 

>[AZURE.NOTE] El objetivo de este tutorial es mostrar cómo se emite un token de autenticación para Servicios móviles. Esto no debe interpretarse como una guía de seguridad. A la hora de desarrollar una aplicación, debe ser consciente de las implicaciones de seguridad que conlleva el almacenamiento de contraseñas y debe disponer de una estrategia para controlar ataques por fuerza bruta.


## <a name="table-setup"></a>Configuración de la tabla de cuentas

Puesto que autenticación personalizada y no depende de otro proveedor de identidades, deberá almacenar la información de inicio de sesión de los usuarios. En esta sección, creará una tabla para las cuentas y configurará mecanismos de seguridad básicos. La tabla de cuentas contendrá los nombres de usuario y las contraseñas con sal y algoritmos hash. También puede incluir otros datos de los usuarios si es necesario.

1. En la carpeta `DataObjects` del proyecto de back-end, cree una nueva entidad llamada `Account`:

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    Esto constituirá una fila en nuestra tabla y contendrá el nombre de usuario, la sal de ese usuario y la contraseña almacenada de forma segura.

2. En la carpeta `Models`, encontrará una clase `DbContext` con el nombre de su servicio móvil. En el resto de este tutorial, usará `todoContext` como ejemplo y deberá actualizar los fragmentos de código en consecuencia. Abra el contexto e incluya el código siguiente para agregar la tabla de cuentas al modelo de datos:

        public DbSet<Account> Accounts { get; set; }

3. A continuación, va a configurar las funciones de seguridad para trabajar con estos datos. Necesitará un medio para generar una nueva sal larga, la capacidad para aplicar un algoritmo hash a una contraseña con sal y un modo seguro de comparar dos algoritmos hash. Cree una clase denominada `CustomLoginProviderUtils` y agréguele los siguientes métodos:


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }


## <a name="register-endpoint"></a>Creación del extremo de registro

Llegado este punto, dispone de todo lo necesario para comenzar a crear cuentas de usuario. En esta sección, configurará un extremo de registro para administrar nuevas solicitudes de registro. Aquí es donde impondrá nuevas directivas de nombre de usuario y contraseña, y se asegurará de que el nombre de usuario no esté ya en uso. Después almacenará la información de usuario de forma segura en la base de datos.

1. Cree un objeto que represente un intento de registro entrante:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Si desea recopilar otros datos en el momento del registro, puede incluirla aquí.

1. En el proyecto de backend de Servicios móviles, agregue un nuevo controlador personalizado denominado CustomRegistrationController y pegue el siguiente código en él:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomRegistrationController : ApiController
        {
            public ApiServices Services { get; set; }

            // POST api/CustomRegistration
            public HttpResponseMessage Post(RegistrationRequest registrationRequest)
            {
                if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
                }
                else if (registrationRequest.password.Length < 8)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
                }
	
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
                if (account != null)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
                }
                else
                {
                    byte[] salt = CustomLoginProviderUtils.generateSalt();
                    Account newAccount = new Account
                    {
                        Id = Guid.NewGuid().ToString(),
                        Username = registrationRequest.username,
                        Salt = salt,
                        SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
                    };
                    context.Accounts.Add(newAccount);
                    context.SaveChanges();
                    return this.Request.CreateResponse(HttpStatusCode.Created);
                }
            }
        }   

    Asegúrese de que permite que todo el tráfico llegue a este extremo. Para ello, incluya el siguiente código en el controlador:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>Creación del proveedor de inicio de sesión

Una de las construcciones básicas de la canalización de autenticación de los Servicios móviles es el `LoginProvider`. En esta sección, creará su propio `CustomLoginProvider`. No estará conectado a la canalización como los proveedores integrados, pero proporcionará una funcionalidad muy práctica.

1. Cree una nueva clase, `CustomLoginProvider`, que se derive de `LoginProvider`:

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       `LoginProvider` tiene otros tres métodos abstractos que implementará más adelante.

2. Cree una clase denominada `CustomLoginProviderCredentials`. Esta clase representa información acerca del usuario y estará disponible en el back-end a través de `ServiceUser.getIdentitiesAsync()`. Si va a agregar notificaciones personalizadas, asegúrese de que se capturan en este objeto.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. Agregue la siguiente implementación del método abstracto `ConfigureMiddleware` a `CustomLoginProvider`. Este método no está operativo aquí porque `CustomLoginProvider` no se integra con la canalización de autenticación.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. Agregue la siguiente implementación del método abstracto `ParseCredentials` a `CustomLoginProvider`. Este método permitirá al backend deserializar información de usuario de un token de autenticación entrante.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. Agregue la siguiente implementación del método abstracto `CreateCredentials` a `CustomLoginProvider`. Este método convierte un elemento `ClaimsIdentity` en un objeto `ProviderCredentials` que se usa en la fase de emisión del token de autenticación. De nuevo, puede capturar aquí otras notificaciones adicionales.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

## <a name="login-endpoint"></a>Creación del extremo de inicio de sesión

A continuación, creará un extremo para que los usuarios inicien sesión. El nombre de usuario y la contraseña que reciba se comprobarán con la base de datos. En primer lugar, se aplica la sal del usuario, después se aplica un algoritmo hash a la contraseña y se comprueba si el valor entrante coincide con el de la base de datos. Si coincide, puede crear un objeto `ClaimsIdentity` y pasarlo al `CustomLoginProvider`. La aplicación de cliente recibirá entonces un identificador de usuario y un token de autenticación para obtener acceso al servicio móvil.

1. En el proyecto de backend de Servicios móviles, cree un objeto para representar un intento de inicio de sesión entrante:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

1. Agregue un nuevo controlador personalizado denominado `CustomLoginController` y pegue el siguiente código en él:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    Asegúrese de que permite que todo el tráfico llegue a este extremo. Para ello, incluya el siguiente código en el controlador:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.NOTE] El `CustomLoginController` que utilice en producción debe contener también una estrategia de detección de ataques por fuerza bruta. De lo contrario, la solución de inicio de sesión puede ser vulnerable a ataques.

## <a name="require-authentication"></a>Configuración del servicio móvil para exigir autenticación

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a>Prueba del flujo de inicio de sesión con un cliente de prueba

En la aplicación de cliente, deberá desarrollar una pantalla de inicio de sesión personalizada que tome nombres de usuario y contraseñas y los envíe como carga JSON a los extremos de registro e inicio de sesión. Para completar este tutorial, usará únicamente el cliente de prueba integrado para el extremo .NET de Servicios móviles.

>[AZURE.NOTE] Los SDK de Servicios móviles se comunicarán con el servicio mediante HTTPS. Si planea obtener acceso a este extremo con una llamada REST directa, debe asegurarse de utilizar HTTPS para llamar al servicio móvil, ya que las contraseñas se envían como texto no cifrado.

1. En Visual Studio, inicie una nueva instancia de depuración del proyecto de back-end de Servicios Móviles. Para ello, haga clic con el botón derecho en el proyecto y seleccione **Depurar->Iniciar nueva instancia**

    ![][0]

2. Haga clic en **Probar**

    ![][1]

3. Seleccione el extremo de registro. Puede ver alguna documentación básica para la API. Haga clic en **Probar esto**.

    ![][2]

4. En el cuerpo, reemplace las cadenas de ejemplo por un nombre de usuario y una contraseña que cumplan los criterios que especificó anteriormente. Después, haga clic en **Enviar**. La respuesta debe ser **201/Creado**.

    ![][3]

5. Repita este proceso para el extremo de inicio de sesión. Después de enviar el nombre de usuario y la contraseña que registró antes, debe recibir el identificador de usuario y un token de autenticación.

    ![][4]


<!-- Anchors. -->
[Configuración de la tabla de cuentas]: #table-setup
[Creación del extremo de registro]: #register-endpoint
[Creación del proveedor de inicio de sesión]: #login-provider
[Creación del extremo de inicio de sesión]: #login-endpoint
[Configuración del servicio móvil para exigir autenticación]: #require-authentication
[Prueba del flujo de inicio de sesión con un cliente de prueba]: #test-login


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Introducción a los usuarios]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started

<!--HONumber=42-->
