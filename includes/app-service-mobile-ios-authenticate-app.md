**Objective-C**:

1. En su Mac, abra _QSTodoListViewController.m_ en Xcode y agregue el siguiente método. Cambie _google_ a _microsoftaccount_, _twitter_, _facebook_ o _windowsazureactivedirectory_ si no usa Google como su proveedor de identidades. Si utiliza Facebook, [necesitará incluir dominios de Facebook en la lista blanca en su aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Reemplace `[self refresh]` en `viewDidLoad` en _QSTodoListViewController.m_ por lo siguiente:

            [self loginAndGetData];

3. Presione _Ejecutar_ para iniciar la aplicación y, a continuación, inicie sesión. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

**Swift**:

1. En su Mac, abra _ToDoTableViewController.swift_ en Xcode y agregue el siguiente método. Cambie _google_ a _microsoftaccount_, _twitter_, _facebook_ o _windowsazureactivedirectory_ si no usa Google como su proveedor de identidades. Si utiliza Facebook, [necesitará incluir dominios de Facebook en la lista blanca en su aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData()
            {
                let client = self.table!.client
                if client.currentUser != nil {
                    return
                }
                    
                client.loginWithProvider("google", controller: self, animated: true, completion: { (user, error) -> Void in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                })
            }

2. Quite las líneas `self.refreshControl?.beginRefreshing()` y `self.onRefresh(self.refreshControl)` al final de `viewDidLoad()` en _ToDoTableViewController.swift_. Agregue una llamada a `loginAndGetData()` en su lugar:

            loginAndGetData()

3. Presione _Ejecutar_ para iniciar la aplicación y, a continuación, inicie sesión. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

<!---HONumber=AcomDC_0107_2016-->