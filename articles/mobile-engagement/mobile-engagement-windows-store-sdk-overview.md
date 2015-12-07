<properties 
	pageTitle="Información general del SDK Windows Universal" 
	description="Introducción al SDK de Windows Universal para Azure Mobile Engagement" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Introducción al SDK de Windows Universal para Azure Mobile Engagement

Comience aquí para obtener los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de Windows Universal. Si primero quiere probarlo, asegúrese de completar nuestro [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

Haga clic para ver el [contenido del SDK](mobile-engagement-windows-store-sdk-content.md)

##Procedimientos de integración

1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Implementación del plan de etiquetas: [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)

##Notas de la versión

###3\.2.0 (20/11/2015)

-   Se ha agregado compatibilidad para aplicaciones de la Plataforma universal de Windows de Windows 10.
-   Se ha agregado una función de uso compartido de canales de inserción para solucionar los conflictos de canales (ahora es compatible con los Centros de notificaciones de Azure).
-   Bloqueo fijo al solicitar el identificador del dispositivo justo después de la inicialización.
-   Mejoras de los registros de consola.
-   Bloqueo solucionado durante el análisis de algunas excepciones no controladas.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-windows-store-release-notes.md)

##Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK, consulte la sección completa [Procedimientos de actualización](mobile-engagement-windows-store-upgrade-procedure.md). Por ejemplo, si migra desde 0.10.1 a 0.11.0, primero debe seguir el procedimiento "de 0.9.0 a 0.10.1" y luego el procedimiento "de 0.10.1 a 0.11.0".

###De 3.1.0 a 3.2.0

#### Recursos
Este paso se refiere solo a los recursos personalizados. Si ha personalizado los recursos proporcionados por el SDK (html, imágenes, superposición), tendrá que hacer una copia de seguridad de estos antes de actualizar y volver a aplicar la personalización en los recursos actualizados.

#### integración de vista web.
En esta versión se introdujeron algunas mejoras para disponer de compatibilidad con distintos factores de formulario de dispositivos. Asegúrese de que la integración de las vistas web coincida con lo siguiente:

En la página XAML ():

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Y en el archivo .cs asociado:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

### Actualizar de versiones anteriores

Consulte [Procedimientos de actualización](mobile-engagement-windows-store-upgrade-procedure/)

<!---HONumber=AcomDC_1125_2015-->