

Tras probar la aplicación de la Tienda Windows con el servicio móvil local, el paso final de este tutorial consiste en publicar el servicio móvil en Azure y ejecutar la aplicación en el servicio en directo.

1.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto de servicio móvil y, a continuación, haga clic en **Publicar**.

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)

    De este modo, se muestra el cuadro de diálogo para publicación en Web.

2.  Haga clic en **Import**, haga clic en **Examinar**, desplácese hasta la ubicación en la que anteriormente guardó el archivo de perfil de publicación, seleccione dicho archivo y haga clic en **OK**.

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-import-profile.png)

    De este modo, se carga la información que Visual Studio necesita para publicar el servicio móvil en Azure.

    **Nota de seguridad**

    Después de importar el perfil de publicación, podría ser conveniente eliminar el archivo descargado dado que contiene información que otros usuarios podrían emplear para tener acceso a sus servicios.

3.  Haga clic en **Validate connection** para comprobar que la publicación esté bien configurada y, a continuación, en **Publicar**.

    ![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

    Una vez realizada la publicación, volverá a ver la página de confirmación de que el servicio móvil está en funcionamiento, aunque esta vez en Azure.


