

1. Abra el archivo de proyecto QSTodoListViewController.m y, en el método **viewDidLoad**, quite el siguiente código que vuelve a cargar los datos en la tabla:

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

    <div class="dev-callout"><b>Nota:</b>
	<p>Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método <strong>loginWithProvider</strong> anterior por uno de los siguientes: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> o <em>windowsazureactivedirectory</em>.</p>
    </div>
		
3. Presione el botón **Run** (Ejecutar) para compilar el proyecto, inicie la aplicación en el emulador de iPhone e inicie sesión con su proveedor de identidades elegido.

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.
