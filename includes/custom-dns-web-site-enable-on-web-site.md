Después de que los registros del nombre de dominio se hayan propagado, deberá asociarlos al sitio web. Utilice los pasos siguientes para habilitar los nombres de dominio mediante el explorador web.

> [WACOM.NOTE] Puede que pase un tiempo antes de que los registros CNAME que se crearon en los pasos anteriores se propaguen por el sistema DNS. No se puede agregar el nombre de dominio a su sitio web de Azure hasta que CNAME se haya propagado. Si está utilizando un registro D, no puede agregar el nombre de dominio del registro D al sitio web de Azure hasta que se hayan propagado los registros CNAME **awverify.www** o **www** que se crearon en el paso anterior.
>
> Puede utilizar un servicio como <http://www.digwebinterface.com/> para comprobar que el CNAME está disponible.

1.  En el explorador, abra el [Portal de administración de Azure](https://manage.windowsazure.com).

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** en la parte inferior de la página.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Utilice los cuadros de texto **NOMBRES DE DOMINIO** para escribir los nombres de dominio que debe asociar a este sitio web.

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Haga clic en la marca de verificación en la esquina inferior derecha para guardar la configuración del nombre de dominio.

    Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **nombres de dominio** de la página **Configure** del sitio web.

En este punto, debería poder escribir el nombre de dominio personalizado en su explorador y ver que le lleva sin problemas al sitio web de Azure.

