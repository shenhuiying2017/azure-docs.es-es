<properties
	pageTitle="Uso de Blob Storage desde Xamarin | Microsoft Azure"
	description="La biblioteca de cliente de Azure Storage para Xamarin permite a los desarrolladores crear aplicaciones iOS, Android y de la Tienda Windows con sus interfaces de usuario nativas. Este tutorial muestra cómo usar Xamarin para crear una aplicación Android que usa Azure Blob Storage."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="micurd"/>

# Uso de Blob Storage desde Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Xamarin permite a los desarrolladores usar un código base C# compartida para crear aplicaciones de iOS, Android y de la Tienda Windows con sus interfaces de usuario nativas. En este tutorial se muestra cómo usar Azure Blob Storage con una aplicación de Xamarin. Si desea más información sobre Azure Storage, antes de entrar en el código, consulte [Introducción a Microsoft Azure Storage](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## Creación de una nueva aplicación Xamarin

En esta introducción, crearemos una aplicación con destino Android, iOS y Windows. Esta aplicación creará simplemente un contenedor y cargará un blob en este contenedor. Aunque en esta introducción vamos a usar Visual Studio en Windows, se pueden aplicar los mismos conocimientos al crear una aplicación mediante Xamarin Studio en Mac OS.

Para crear la aplicación, siga estos pasos:

1. Si aún no lo ha hecho, descargue e instale o [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abra Visual Studio y cree una aplicación vacía (compartida nativa): **Archivo > Nuevo > Proyecto > Multiplataforma > Blank App (Native Shared)** Blank App(Native Shared) (Aplicación vacía [compartida nativa]).
3. Haga clic con el botón derecho en la solución en el panel del Explorador de soluciones y seleccione **Administrar paquetes NuGet para la solución**. Busque **WindowsAzure.Storage** e instale la versión estable más reciente para todos los proyectos de su solución.
4. Compile y ejecute el proyecto.

Ahora debería tener una aplicación que le permita hacer clic en un botón e incrementar un contador.

> [AZURE.NOTE] La biblioteca de cliente de Azure Storage para Xamarin también funciona para aplicaciones de Xamarin.Forms.

## Creación de un contenedor y carga de un blob

A continuación, agregará código a la clase compartida `MyClass.cs` que crea un contenedor y carga un blob en este contenedor. `MyClass.cs` debería ser similar al siguiente:

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using System.Threading.Tasks;

	namespace XamarinApp
	{
		public class MyClass
		{
			public MyClass ()
			{
			}

		    public static async Task createContainerAndUpload()
		    {
		        // Retrieve storage account from connection string.
		        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

		        // Create the blob client.
		        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

		        // Retrieve reference to a previously created container.
		        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

				// Create the container if it doesn't already exist.
            	await container.CreateIfNotExistsAsync();

		        // Retrieve reference to a blob named "myblob".
		        CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

		        // Create the "myblob" blob with the text "Hello, world!"
		        await blockBlob.UploadTextAsync("Hello, world!");
		    }
		}
	}

Asegúrese de reemplazar "su\_nombre\_de\_cuenta\_aquí" y "su\_clave\_de\_cuenta\_aquí" por el nombre de cuenta y la clave de cuenta reales. Luego, puede usar esta clase compartida en su aplicación de iOS, Android y Windows Phone. Puede agregar simplemente `MyClass.createContainerAndUpload()` a cada proyecto. Por ejemplo:

### XamarinApp.Droid > MainActivity.cs

	using Android.App;
	using Android.Widget;
	using Android.OS;

	namespace XamarinApp.Droid
	{
		[Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
		public class MainActivity : Activity
		{
			int count = 1;

			protected override async void OnCreate (Bundle bundle)
			{
				base.OnCreate (bundle);

				// Set our view from the "main" layout resource
				SetContentView (Resource.Layout.Main);

				// Get our button from the layout resource,
				// and attach an event to it
				Button button = FindViewById<Button> (Resource.Id.myButton);

				button.Click += delegate {
					button.Text = string.Format ("{0} clicks!", count++);
				};

	      	  await MyClass.createContainerAndUpload();
			}
		}
	}

### XamarinApp.iOS > ViewController.cs

	using System;
	using UIKit;

	namespace XamarinApp.iOS
	{
		public partial class ViewController : UIViewController
		{
			int count = 1;

			public ViewController (IntPtr handle) : base (handle)
			{
			}

			public override async void ViewDidLoad ()
			{
				base.ViewDidLoad ();
				// Perform any additional setup after loading the view, typically from a nib.
				Button.AccessibilityIdentifier = "myButton";
				Button.TouchUpInside += delegate {
					var title = string.Format ("{0} clicks!", count++);
					Button.SetTitle (title, UIControlState.Normal);
				};

	            await MyClass.createContainerAndUpload();
	    	}

			public override void DidReceiveMemoryWarning ()
			{
				base.DidReceiveMemoryWarning ();
				// Release any cached data, images, etc that aren't in use.
			}
		}
	}

### XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

	using Windows.UI.Xaml.Controls;
	using Windows.UI.Xaml.Navigation;

	// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

	namespace XamarinApp.WinPhone
	{
	    /// <summary>
	    /// An empty page that can be used on its own or navigated to within a Frame.
	    /// </summary>
	    public sealed partial class MainPage : Page
	    {
	        int count = 1;

	        public MainPage()
	        {
	            this.InitializeComponent();

	            this.NavigationCacheMode = NavigationCacheMode.Required;
	        }

	        /// <summary>
	        /// Invoked when this page is about to be displayed in a Frame.
	        /// </summary>
	        /// <param name="e">Event data that describes how this page was reached.
	        /// This parameter is typically used to configure the page.</param>
	        protected override async void OnNavigatedTo(NavigationEventArgs e)
	        {
	            // TODO: Prepare page for display here.

	            // TODO: If your application contains multiple pages, ensure that you are
	            // handling the hardware Back button by registering for the
	            // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
	            // If you are using the NavigationHelper provided by some templates,
	            // this event is handled for you.
	            Button.Click += delegate {
	                var title = string.Format("{0} clicks!", count++);
	                Button.Content = title;
	            };

	            await MyClass.createContainerAndUpload();
	        }
	    }
	}


## Ejecución de la aplicación

Ahora puede ejecutar esta aplicación en un emulador Android o Windows Phone. También puede ejecutar esta aplicación en un emulador de iOS, pero necesitará un equipo Mac. Para obtener instrucciones específicas al respecto, lea la documentación sobre [cómo conectar Visual Studio con un equipo Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Después de ejecutar su aplicación, se creará el contenedor `mycontainer` en su cuenta de Storage. Contendrá el blob `myblob`, que tiene el texto `Hello, world!`. Para comprobar esto, use el [Explorador de Microsoft Azure Storage](http://storageexplorer.com/).

## Pasos siguientes

En esta introducción, aprendió a crear una aplicación multiplataforma en Xamarin que usa Azure Storage. Aunque se ha centrado específicamente en un escenario de Blob Storage, puede hacer muchas más cosas no solo con Blob Storage, sino también con Table, File y Queue Storage. Consulte los artículos siguientes para más información:
- [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md)
- [Introducción al Almacenamiento de tablas de Azure mediante .NET](storage-dotnet-how-to-use-tables.md)
- [Introducción al Almacenamiento en cola de Azure mediante .NET](storage-dotnet-how-to-use-queues.md)
- [Introducción a Almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

<!---HONumber=AcomDC_0907_2016-->