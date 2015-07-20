
## <a id="register"></a>Registro de aplicaciones para notificaciones de inserción

* [Registre un identificador de aplicación para una aplicación](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). Active la casilla opcional **Notificaciones de inserción** en **Servicios de aplicaciones** al registrar la aplicación.

> [AZURE.NOTE]Cree un identificador de aplicación explícito (no un identificador de aplicación comodín) y en **Id. de agrupación**, use el **Id. de agrupación** exacto que se encuentra en el proyecto de inicio rápido de Xcode. También es necesario que active la opción **Notificaciones de inserción** en **Servicios de aplicaciones** al registrar el identificador de la aplicación. Las notificaciones de inserción no funcionarán si no activa esta casilla.

* A continuación, [habilite las notificaciones de inserción para la aplicación](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6). Puede crear un certificado SSL de "Desarrollo" o "Distribución" (no olvide seleccionar la opción correspondiente ("Espacio aislado" o "Producción") más adelante, en el portal de servicios móviles).

* Después, [compruebe que el identificador de aplicación habilita las notificaciones de inserción](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Por último, [actualice los perfiles de aprovisionamiento en el proyecto de inicio rápido de Xcode](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) y, después, [compruebe que el perfil de aprovisionamiento se creó o se volvió a generar para habilitar las notificaciones de inserción](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).

<!---HONumber=July15_HO2-->