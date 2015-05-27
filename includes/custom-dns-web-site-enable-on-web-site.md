Después de que los registros del nombre de dominio se hayan propagado, es preciso asociarlos a la aplicación web. Utilice los pasos siguientes para habilitar los nombres de dominio mediante el explorador web.

> [AZURE.NOTE]Puede que pase un tiempo antes de que los registros CNAME que se crearon en los pasos anteriores se propaguen por el sistema DNS. El nombre de dominio de la aplicación web no se puede agregar hasta que se haya propagado el CNAME. Si usa un registro D, no puede agregar el nombre de dominio de dicho registro a la aplicación web que se haya propagado el registro **awverify** de CNAME creado en el paso anterior.
> 
> Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el CNAME está disponible.

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

2. En la pestaña **Aplicaciones web**, haga clic en el nombre de la aplicación web, seleccione **Configuración** y, a continuación, seleccione **Dominios personalizados y SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Utilice los cuadros de texto **NOMBRES DE DOMINIO** para escribir los nombres de dominio que se van a asociar a esta aplicación web.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Haga clic en la marca de verificación en la esquina inferior derecha para guardar la configuración del nombre de dominio.

	Al finalizar la configuración, el nombre de dominio personalizado aparecerá en la sección de **nombres de dominio** de la hoja **Dominios personalizados y SSL** de la aplicación web.

En este punto, debería poder escribir el nombre de dominio personalizado en el explorador y ver que le lleva sin problemas a la aplicación web.
<!--HONumber=54-->