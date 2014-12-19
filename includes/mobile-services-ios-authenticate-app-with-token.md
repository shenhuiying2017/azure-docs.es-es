
En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio móvil cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por los servicios móviles e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores. 


>[WACOM.NOTE] Puede almacenar en caché el token emitido por los servicios móviles con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.

1. La manera recomendada para cifrar y almacenar los tokens de autenticación en un cliente iOS es mediante Keychain. Para ello, cree una clase KeychainWrapper, copiando [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) y [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) del [ejemplo LensRocket](https://github.com/WindowsAzure-Samples/iOS-LensRocket). Este objeto KeychainWrapper se usa como el objeto KeychainWrapper definido en la documentación de Apple no tiene en cuenta el recuento de referencias automáticas (ARC).


2. Abra el archivo de proyecto **QSTodoListViewController.m** y agregue el siguiente código:

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. Al final del método **viewDidAppear** en **QSTodoListViewController.m**, agregue la llamada a saveAuthInfo. Con esta llamada, simplemente se almacena el identificador de usuario y las propiedades del token.  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. Ahora que hemos visto cómo el token y el identificador de usuario se puede almacenar en caché, miremos cómo se puede cargar esto al iniciarse la aplicación. En el método **viewDidLoad** en **QSTodoListViewController.m**, agregue la llamada a loadAuthInfo después de que se haya iniciado **self.todoService**. 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. Si la aplicación realiza una solicitud al Servicio móvil que debería realizarse correctamente porque el usuario ya se ha autenticado, y recibe una respuesta 401 (error de autorización), significa que el token de usuario que está pasando ha expirado. En el controlador de finalización para cada método existente que interactúa con el Servicio móvil, se podría comprobar una respuesta 401 o se podría gestionar el proceso desde un solo lugar: el handleRequest de MSFilter.  Para ver cómo gestionar este escenario, consulte [este artículo del blog](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx).

