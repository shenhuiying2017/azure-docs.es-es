

1. Abra **QSTodoListViewController.m** y en el método **viewDidLoad**, quite la siguiente línea:

        [self refresh];

2.	Agregue el siguiente código justo después del método **viewDidLoad**:  

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

    > [AZURE.NOTE] Si usa un proveedor de identidades que no sea Facebook, cambie el valor pasado a **loginWithProvider**. Los valores admitidos son: _microsoftaccount_, _facebook_, _twitter_, _google_ o _windowsazureactivedirectory_.

3. Presione  **Ejecutar** para iniciar la aplicación y, a continuación, inicie sesión en el proveedor de identidades elegido. Una vez que haya iniciado sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones en ella.

<!--HONumber=49-->