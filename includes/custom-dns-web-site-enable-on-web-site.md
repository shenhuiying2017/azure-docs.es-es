Después de que los registros del nombre de dominio se hayan propagado, deberá asociarlos al sitio web. Utilice los pasos siguientes para habilitar los nombres de dominio mediante el explorador web.

> [AZURE.NOTE] Puede que pase un tiempo antes de que los registros CNAME que se crearon en los pasos anteriores se propaguen por el sistema DNS. No se puede agregar el nombre de dominio a su sitio web de Azure hasta que CNAME se haya propagado. Si está usando un registro A, no puede agregar el nombre de dominio del registro A al sitio web de Azure hasta que se haya propagado el registro CNAME **awverify** que se creó en el paso anterior.
> 
> Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el CNAME está disponible.

1. En el explorador, abra el [Portal de administración de Azure](https://manage.windowsazure.com).

2. En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** en la parte inferior de la página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Use los cuadros de texto **NOMBRES DE DOMINIO** para escribir los nombres de dominio que debe asociar a este sitio web. 

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Haga clic en la marca de verificación en la esquina inferior derecha para guardar la configuración del nombre de dominio.

	Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **nombres de dominio** de la página **Configurar** del sitio web.

En este punto, debería poder escribir el nombre de dominio personalizado en su explorador y ver que le lleva sin problemas al sitio web de Azure. <!--HONumber=42-->
