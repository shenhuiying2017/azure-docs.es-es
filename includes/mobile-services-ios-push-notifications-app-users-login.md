
A continuación, debe cambiar la manera en que se registran las notificaciones de inserción para asegurarse de que el usuario se autentique antes de que se intente el registro. 

1. En **QSAppDelegate.m**, quite por completo la implementación de **didFinishLaunchingWithOptions**:

		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
		(NSDictionary *)launchOptions
		{
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		    return YES;
		}

2. Abra el archivo de proyecto **QSTodoListViewController.m** y, en el método **viewDidLoad**, agregue el código quitado anteriormente para agregar lo siguiente:

	
		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		        [self refresh];
		    }];
		
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		}
<!--HONumber=42-->
