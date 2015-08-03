<properties
	pageTitle="Introducción a los Centros de notificaciones de Azure"
	description="Obtenga información acerca de cómo usar los Centros de notificaciones de Azure para las notificaciones de inserción."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article" 
	ms.date="05/27/2015"
	ms.author="wesmc"/>

# Introducción a los Centros de notificaciones

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Información general

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación iOS. En este tutorial puede crear una aplicación iOS vacía que recibe notificaciones de inserción mediante el servicio de notificaciones de inserción de Apple (APN). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones.

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones.

##Requisitos previos

Este tutorial cuenta con los siguientes requisitos previos:

+ [SDK de iOS para Servicios móviles]
+ [XCode 6][Install Xcode]
+ Un dispositivo compatible con iOS 8 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS

   >[AZURE.NOTE]Debido a los requisitos de configuración de notificaciones push, debe implementar y probar las notificaciones push en un dispositivo compatible con iOS (iPhone o iPad) y no en el simulador.

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones iOS.

> [AZURE.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Los Centros de notificaciones permiten notificaciones push de Apple](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Configuración de su Centro de notificaciones

Esta sección le guía en la creación y configuración de un nuevo centro de notificaciones con el certificado de inserción que se creó. Si desea usar un centro de notificaciones que ya ha creado, puede omitir los pasos del 2 al 5.


1. En Acceso a llaves, haga clic en el nuevo certificado de inserción que creó en la categoría **Certificados**. Haga clic en **Export** (Exportar), dé un nombre al archivo, seleccione el formato **.p12** y, a continuación, haga clic en **Save** (Guardar).

    ![][1]

	Anote el nombre de archivo y la ubicación del certificado exportado.

	>[AZURE.NOTE]Con este tutorial se crea un archivo QuickStart.p12. El nombre del archivo y la ubicación pueden ser diferentes.

2. Inicie sesión en el [Portal de administración de Azure] y haga clic en **+NUEVO** en la parte inferior de la pantalla.

3. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y, a continuación, en **Creación rápida**.

   	![][2]

4. Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear una nueva base de datos central de notificaciones**.

   	![][3]

5. Haga clic en el espacio de nombres que acaba de crear (normalmente ***nombre del centro de notificaciones*-ns**) para abrir su panel.

   	![][4]

6. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

   	![][5]

7. Haga clic en la pestaña **Configurar** en la parte superior y luego haga clic en el botón **Cargar** en la configuración de notificaciones de Apple para cargar la huella digital del certificado. A continuación, seleccione el certificado **.p12** que exportó anteriormente y la contraseña para el certificado. Asegúrese de seleccionar si desea usar el servicio de inserción **Production** (si desea enviar notificaciones de inserción a usuarios que adquirieron la aplicación desde la tienda) o **Sandbox** (durante el desarrollo).

   	![][6]

8. Haga clic en la pestaña **Panel** en la parte superior y luego haga clic en **Ver cadena de conexión**. Anote las dos cadenas de conexión.

   	![][7]

El centro de notificaciones está ahora configurado para funcionar con APNS y tiene las cadenas de conexión para registrar la aplicación y enviar notificaciones.

##Conexión de su aplicación al centro de notificaciones

1. En XCode, cree un nuevo proyecto iOS y seleccione la plantilla **Single View Application** (Aplicación de vista sencilla).

   	![][8]

2. Al configurar las opciones para su nuevo proyecto, asegúrese de usar el mismo **nombre de producto** e **identificador organizativo** que usó cuando estableció anteriormente el Id. de paquete en el portal de desarrollo de Apple.

	![][11]

3. En **Targets** (Destinos), haga clic en el nombre de proyecto, luego haga clic en la pestaña **Build Settings** (Compilar configuración) y expanda **Code Signing Identity** (Identidad de firma de código); a continuación, en **Debug** (Depurar), establezca la **identidad de firma de código**. Alterne los **niveles** entre **Basic** (Básico) a **All** (Todos) y establezca el **perfil de aprovisionamiento** en el perfil de aprovisionamiento que creó anteriormente.

	Si no ve el nuevo perfil de aprovisionamiento que creó en XCode, intente actualizar los perfiles de la identidad de firma haciendo clic en **XCode** en la barra de menús. A continuación, haga clic en **Preferences** (Preferencias), en la pestaña **Account** (Cuenta), en el botón **View Details** (Ver detalles), haga clic en la identidad de firma y luego en el botón Refresh (Actualizar) en la esquina inferior derecha.

   	![][9]

4. Descargue la **versión 1.2.4** del [SDK de Servicios móviles para iOS] y descomprima el archivo. En XCode, haga clic con el botón derecho en el proyecto y haga clic en la opción **Add Files to** (Agregar archivos a) para agregar la carpeta **WindowsAzureMessaging.framework** al proyecto de XCode. Seleccione **Copy items if needed** (Copiar elementos si es necesario) y luego haga clic en **Add** (Agregar).

   	![][10]

5. Abra el archivo AppDelegate.h y agregue la siguiente directiva de importación:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. En el archivo AppDelegate.m, agregue el siguiente código al método `didFinishLaunchingWithOptions` en función de su versión de iOS. Este código registra el identificador de dispositivo en APNS:

	Para iOS 8:

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	Para la versión de iOS antes de la 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. En el mismo archivo, agregue los métodos siguientes y reemplace los marcadores de posición de literal de cadena por su *nombre del concentrador* y el valor de *DefaultListenSharedAccessSignature* que anotó anteriormente. Este código proporciona el token del dispositivo al centro de notificaciones para que el centro de notificaciones pueda enviar notificaciones:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. En el mismo archivo, agregue el siguiente método para mostrar una **UIAlert** si la notificación se recibe mientras la aplicación está activa:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. Compile y ejecute la aplicación en el dispositivo para comprobar que no haya errores.

## Envío de notificaciones


Para probar la recepción de notificaciones en su aplicación, envíe notificaciones en el Portal de Azure usando la pestaña de depuración en el centro de notificaciones, tal como se muestra en la pantalla que aparece a continuación.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. En XCode, abra su Main.storyboard y agregue los siguientes componentes de interfaz de usuario de la biblioteca de objetos para permitir que el usuario envíe notificaciones push en la aplicación.

	- Una etiqueta sin texto de etiqueta. Se usará para notificar errores al enviar notificaciones. La propiedad **Lines** se debe establecer en **0** para que el redimensionamiento automático se restrinja a los márgenes derecho e izquierdo y a la parte superior de la vista.
	- Un campo de texto con el texto de **marcador de posición** establecido en **Enter Notification Message** (Escribir mensaje de notificación). Restrinja el campo justo debajo de la etiqueta, tal como se muestra a continuación. Establezca el Controlador de vista como delegado de salida.
	- Un botón denominado **Send Notification** (Enviar notificación) se restringió justo debajo del campo de texto y en el centro horizontal.

	La vista debe tener el aspecto siguiente:

	![][32]


2. Abra el archivo ViewController.h y agregue las siguientes instrucciones `#import` y `#define`. Sustituya el literal de cadena de marcador de posición por la cadena de conexión *DefaultFullSharedAccessSignature* real y el *nombre del centro*.


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. Agregue salidas para la etiqueta y el campo de texto conectados a la vista y actualice la `interface` definición para que admita `UITextFieldDelegate` y `NSXMLParserDelegate`. Agregue las tres declaraciones de propiedades que se muestran a continuación para ayudar a admitir la llamada a la API de REST y el análisis de la respuesta.

	El archivo ViewController.h debe tener el aspecto siguiente:

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging.
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. Abra ViewController.m y agregue el siguiente código para analizar su cadena de conexión *DefaultFullSharedAccessSignature*. Como se ha mencionado en la [Referencia de la API de REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), esta información analizada se usará para generar un token de SAS para el encabezado de la solicitud *Autorización*.

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. En ViewController.m, actualice el método `viewDidLoad` para analizar la cadena de conexión cuando se cargue la vista. Agregue también los métodos de utilidad que se muestran a continuación.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. En ViewController.m, agregue el código siguiente para generar el token de autorización de SAS que se proporcionará en el encabezado *Authorization* (Autorización) tal como se mencionó en la [Referencia de la API de REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. Presione **Ctrl y arrastre** desde el botón **Send Notification** (Enviar notificación) hasta ViewController.m para agregar una acción para el evento **tocar hacia abajo** que ejecuta la llamada a la API de REST mediante el código siguiente.

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the messages REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generated the token to be used in the authorization header.
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNS notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			//Authenticate the notification message POST request with the SaS token
			[request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

			//Add the notification message body
			[request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

			// Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
		        }
				if (data != NULL)
				{
		        	xmlParser = [[NSXMLParser alloc] initWithData:data];
		        	[xmlParser setDelegate:self];
		       		[xmlParser parse];
		    	}
		    }];
		    [dataTask resume];
		}


8. En ViewController.m, agregue el siguiente método delegado para admitir el cierre del teclado para el campo de texto. Presione **Ctrl y arrastre** desde el campo de texto al icono de Controlador de vista en el diseñador de la interfaz para establecer el controlador de vista como el delegado de salida.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. En ViewController.m, agregue los siguientes métodos delegados para admitir el análisis de la respuesta con `NSXMLParser`.

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. Compile el proyecto y compruebe que no haya errores.



Puede buscar todas las cargas posibles en la [guía de programación de notificaciones push y locales] de Apple.



##Prueba de la aplicación

Para probar las notificaciones push en iOS, debe implementar la aplicación en un dispositivo. No puede enviar notificaciones push de Apple con el simulador de iOS.

1. Ejecute la aplicación y compruebe que los registros se realizan correctamente, luego presione **OK** (Aceptar).

	![][33]

2. Toque en el campo de texto para escribir un mensaje de notificación. Luego, presione el botón **Send** (Enviar) en el teclado o el botón **Send Notification** (Enviar notificación) de la vista para enviar el mensaje de notificación.

	![][34]

3. La notificación se envía a todos los dispositivos registrados para recibir la notificación.

	![][35]

Si tiene algún problema o alguna recomendación que sirva para mejorar este tutorial para todos los lectores, déjenos un comentario en la siguiente sección de Disqus.


##Pasos siguientes

En este sencillo ejemplo, se difunden notificaciones a todos los dispositivos iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora]. Obtenga más información sobre el uso de Centros de notificaciones en la [Orientación sobre los Centros de notificaciones].



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[SDK de Servicios móviles para iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[SDK de iOS para Servicios móviles]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Uso de Centros de notificaciones para insertar notificaciones en los usuarios]: notification-hubs-aspnet-backend-ios-notify-users.md
[Uso de los Centros de notificaciones para enviar noticias de última hora]: notification-hubs-ios-send-breaking-news.md

[guía de programación de notificaciones push y locales]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
 

<!---HONumber=July15_HO4-->