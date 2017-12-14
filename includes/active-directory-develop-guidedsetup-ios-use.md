## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utilice la biblioteca de autenticación de Microsoft (MSAL) para obtener un token para la API Graph de Microsoft.

Abra `ViewController.swift` y reemplace el código por:

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current logged in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive signin.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign-in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Más información
#### <a name="getting-a-user-token-interactively"></a>Obtención de un token de usuario interactivamente
La llamada al método `acquireToken` genera una ventana en el explorador que pide al usuario que inicie sesión. Las aplicaciones suelen requerir que un usuario inicie sesión interactivamente la primera vez que requieren acceso a un recurso protegido, o cuando se produce un error en una operación silenciosa para adquirir un token (por ejemplo, si la contraseña de usuario caducó).

#### <a name="getting-a-user-token-silently"></a>Obtención de un token de usuario en silencio
El método `acquireTokenSilent` controla la renovación y las adquisiciones de tokens sin la interacción del usuario. Después de ejecutar `acquireToken` por primera vez, `acquireTokenSilent` es el método usado habitualmente para obtener los tokens empleados para acceder a recursos protegidos en las llamadas posteriores, ya que las llamadas para solicitar o renovar los tokens se realizan en modo silencioso.

Es posible que `acquireTokenSilent` genere eventualmente un error; por ejemplo, si el usuario ha cerrado la sesión o ha cambiado su contraseña en otro dispositivo. Si MSAL detecta que el problema puede solucionarse requiriendo una acción interactiva, desencadena una excepción `MSALErrorCode.interactionRequired`. La aplicación puede abordar esta excepción de dos maneras:

1.  Realizar una llamada a `acquireToken` inmediatamente, lo que ocasiona que el usuario tenga que iniciar sesión. Este patrón se da normalmente en aplicaciones en línea en las que no hay ningún contenido sin conexión en la aplicación disponible para el usuario. La aplicación de ejemplo generada en esta instalación guiada utiliza este patrón: puede verla en acción la primera vez que se ejecute la aplicación. Dado que ningún usuario usó la aplicación, `applicationContext.users().first` contendrá un valor null y se iniciará una excepción ` MSALErrorCode.interactionRequired `. El código del ejemplo entonces trata la excepción llamando a `acquireToken`, lo que provoca que se pida al usuario que inicie sesión.

2.  Las aplicaciones también pueden hacer una indicación visual al usuario de que se requiere un inicio de sesión interactivo, de manera que el usuario pueda seleccionar el momento adecuado para iniciar sesión, o la aplicación puede reintentar `acquireTokenSilent` en un momento posterior. Esto se utiliza normalmente cuando el usuario puede utilizar otras funciones de la aplicación sin que se le interrumpa: por ejemplo, hay contenido sin conexión disponible en la aplicación. En este caso, el usuario puede decidir el momento en que desea iniciar sesión para tener acceso al recurso protegido, o actualizar la información no actualizada; o puede decidir que la aplicación vuelva a intentar `acquireTokenSilent` cuando se restaure la red después de estar no disponible temporalmente.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Llamada a la API Graph de Microsoft con el token que acaba de obtener

Agregue el siguiente método nuevo a `ViewController.swift`. El método se utiliza para realizar una solicitud `GET` a la API de Microsoft Graph con un *encabezado de autorización HTTP*:

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Llamada de REST con una API protegida

En esta aplicación de ejemplo, el método `getContentWithToken()` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token y, a continuación, devolver el contenido al autor de la llamada. Este método agrega el token adquirido al *encabezado de autorización HTTP*. En este ejemplo, el recurso es el punto de conexión *me* de la API Graph de Microsoft, que muestra información de perfil del usuario.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Configuración del cierre de sesión

Agregue el método siguiente a `ViewController.swift` para cerrar la sesión del usuario:

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Más información sobre el cierre de sesión

El método `signoutButton` anterior quita al usuario de la caché de usuario MSAL: esto indicará a MSAL de manera eficaz que olvide al usuario actual de forma que una solicitud futura para adquirir un token solo se completará correctamente si se realiza para ser interactiva.

Aunque la aplicación en este ejemplo es compatible con un solo usuario, MSAL admite escenarios en los que es posible iniciar sesión en varias cuentas al mismo tiempo: un ejemplo es una aplicación de correo electrónico donde un usuario tiene varias cuentas.
<!--end-collapse-->

## <a name="register-the-callback"></a>Registro de la devolución de llamada

Una vez que el usuario se autentica, el explorador lo redirige a la aplicación. Siga estos pasos para registrar esta devolución de llamada:

1.  Abra `AppDelegate.swift` e importe MSAL:

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Agregue el método siguiente a su clase <code>AppDelegate</code> para administrar las devoluciones de llamada:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```

