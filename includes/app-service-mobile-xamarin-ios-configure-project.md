#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Configuración del proyecto de iOS en Xamarin Studio
1. En Xamarin.Studio, abra **Info.plist** y actualice el **identificador de agrupación** de trabajos con el identificador que ha creado antes con su nuevo identificador de la aplicación.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Desplácese hacia abajo hasta **Background Modes** (Modos en segundo plano). Active la casilla **Enable Background Modes** (Habilitar modos en segundo plano) y la casilla **Remote notifications** (Notificaciones remotas).

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Haga doble clic en el proyecto en el Panel de soluciones para abrir las **opciones de proyecto**.
4. En **Build** (Compilar), elija **iOS Bundle Signing** (Registro de agrupaciones de trabajos iOS) y seleccione la identidad y el perfil de aprovisionamiento correspondientes que acaba de configurar para este proyecto.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   De esta forma, se garantiza que el proyecto usa el nuevo perfil para la firma de código. Para ver la documentación oficial de aprovisionamiento de dispositivos Xamarin, consulte [Aprovisionamiento de dispositivos Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Configuración del proyecto de iOS en Visual Studio
1. En Visual Studio, haga clic con el botón derecho en el proyecto y después haga clic en **Propiedades**.
2. En las páginas de propiedades, haga clic en la pestaña **Aplicación de iOS** y actualice el **identificador** con el id. que creó anteriormente.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. En la pestaña **iOS Bundle Signing** (Registro de agrupación de trabajos iOS), seleccione la identidad y el perfil de aprovisionamiento que acaba de configurar para este proyecto.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    De esta forma, se garantiza que el proyecto usa el nuevo perfil para la firma de código. Para ver la documentación oficial de aprovisionamiento de dispositivos Xamarin, consulte [Aprovisionamiento de dispositivos Xamarin].
4. Haga doble clic en Info.plist para abrirlo y, a continuación, habilite **RemoteNotifications** en **Background Modes** (Modos en segundo plano).

[Aprovisionamiento de dispositivos Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


<!--HONumber=Dec16_HO2-->


