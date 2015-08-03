

1. Abra **QSTodoListViewController.m** y agregue el siguiente código:


        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }


    > [AZURE.NOTE]Si usa un proveedor de identidades que no sea Facebook, cambie el valor pasado a **loginWithProvider**. Los valores compatibles son: _microsoftaccount_, _facebook_, _twitter_, _google_ o _windowsazureactivedirectory_.


2. Modifique `viewDidLoad` reemplazando `[self refresh]` al final por lo siguiente:

        [self loginAndGetData];

3. Presione **Ejecutar** para iniciar la aplicación y, a continuación, inicie sesión en el proveedor de identidades elegido. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

<!---HONumber=July15_HO4-->