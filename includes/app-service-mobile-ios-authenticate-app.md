**Objective-C**: 

1. En su Mac, abra *QSTodoListViewController.m* en Xcode y agregue el siguiente método. Cambie *google* a *microsoftaccount*, *twitter*, *facebook* o *windowsazureactivedirectory* si no usa Google como su proveedor de identidades. Si utiliza Facebook, [necesitará incluir dominios de Facebook en la lista blanca en su aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
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
2. Reemplace `[self refresh]` en `viewDidLoad` en *QSTodoListViewController.m* por lo siguiente:
   
            [self loginAndGetData];
3. Presione *Ejecutar* para iniciar la aplicación y, después, inicie sesión. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

**Swift**:

1. En su Mac, abra *ToDoTableViewController.swift* en Xcode y agregue el siguiente método. Cambie *google* a *microsoftaccount*, *twitter*, *facebook* o *windowsazureactivedirectory* si no usa Google como su proveedor de identidades. Si utiliza Facebook, [necesitará incluir dominios de Facebook en la lista blanca en su aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
            func loginAndGetData() {
   
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
   
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }
2. Quite las líneas `self.refreshControl?.beginRefreshing()` y `self.onRefresh(self.refreshControl)` al final de `viewDidLoad()` en *ToDoTableViewController.swift*. Agregue una llamada a `loginAndGetData()` en su lugar:
   
            loginAndGetData()
3. Presione *Ejecutar* para iniciar la aplicación y, después, inicie sesión. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.



<!--HONumber=Nov16_HO3-->


