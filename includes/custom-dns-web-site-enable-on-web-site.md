Después de que los registros del nombre de dominio se hayan propagado, es preciso asociarlos a la aplicación web. Utilice los pasos siguientes para habilitar los nombres de dominio mediante el explorador web.

> [!NOTE]
> Puede que pase un tiempo antes de que los registros TXT que se crearon en los pasos anteriores se propaguen por el sistema DNS. El nombre de dominio de la aplicación web no se puede agregar hasta que se haya propagado el registro TXT. Si usa un registro D, no puede agregar el nombre de dominio de dicho registro a la aplicación web hasta que se haya propagado el registro TXT creado en el paso anterior.
> 
> Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el registro TXT está disponible.
> 
> 

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).
2. En la pestaña **Web Apps**, haga clic en el nombre de la aplicación web y seleccione **Dominios personalizados**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. En la hoja **Dominios personalizados**, haga clic en **Agregar nombre de host**.
4. Utilice los cuadros de texto **Nombre de host** para escribir los nombres de dominio que se van a asociar a esta aplicación web.
   
    ![](./media/custom-dns-web-site/add-custom-domain.png)
5. Haga clic en **Validar**.
6. Al hacer clic en **Validar** , Azure iniciará el flujo de trabajo Verificación de dominio. Este flujo de trabajo comprobará la propiedad del dominio y la disponibilidad del nombre de host, e informará del éxito o del error de forma detallada y con instrucciones para solucionar el error.    

En este punto, debería poder escribir el nombre de dominio personalizado en el explorador y ver que le lleva sin problemas a la aplicación web.

