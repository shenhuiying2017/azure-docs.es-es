
El ejemplo anterior muestra un inicio de sesión estándar, que requiere que el cliente se ponga en contacto tanto con el proveedor de identidades como con el servicio móvil cada vez que se inicia la aplicación. Este método no escala y es ineficaz.

Un método mejor es almacenar en caché el token de autorización devuelto por los Servicios móviles de Azure e intentar usarlo antes de utilizar un inicio de sesión basado en el proveedor.

1. Para cifrar y almacenar tokens de autenticación en un cliente iOS, se recomienda usar iOS Keychain. Vamos a usar [SSKeychain](https://github.com/soffes/sskeychain) (un contenedor simple de iOS Keychain). Siga las instrucciones de la página de SSKeychain para agregarlo al proyecto. Compruebe que el valor **Habilitar módulos** está habilitado en la opción **Configuración de compilación** (sección **Apple LLVM - Idiomas - Módulos**) del proyecto.

2. Abra **QSTodoListViewController.m** y agregue el siguiente código:


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. En el método `loginAndGetData`, modifique el bloque de finalización de la llamada `loginWithProvider:controller:animated:completion:` mediante la adición de una llamada a `saveAuthInfo` justo antes de la línea `[self refresh]`. Con esta llamada, simplemente se almacenan el Id. de usuario y las propiedades del token:

				[self saveAuthInfo];

4. También se cargan el Id. de usuario y las propiedades del token cuando se inicia la aplicación. En el método `viewDidLoad` en **QSTodoListViewController.m**, agregue una llamada a loadAuthInfo inmediatamente después de que se haya inicializado `self.todoService`.

				[self loadAuthInfo];
<!--HONumber=54-->