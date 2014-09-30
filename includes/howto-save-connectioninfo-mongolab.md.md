Aunque es posible pegar un URI de MongoLAB en el código, recomendamos configurarlo en el entorno para obtener una mayor facilidad en la administración. De esta forma, si el URI cambia, puede actualizarlo a través del Portal de Azure sin dirigirse al código.

1.  En el Portal de Azure, seleccione **Sitios web**.
2.  Haga clic en el nombre del sitio web en la lista de sitios web.  
    ![WebSiteEntry][]
    Se muestra el panel del sitio web.

3.  Haga clic en **Configurar** en la barra de menú.  
    ![WebSiteDashboardConfig][]

4.  Desplácese a la sección Cadenas de conexión.  
    ![WebSiteConnectionStrings][]

5.  Para **Name**, especifique MONGOLAB\_URI.
6.  Para **Value**, pegue la cadena de conexión que se obtuvo en la sección anterior.
7.  Seleccione **Custom** en la lista desplegable **Type** (en lugar del valor predeterminado **SQLAzure**).
8.  Haga clic en **Guardar** en la barra de herramientas.  
    ![SaveWebSite][]

**Nota:** Azure agrega el prefijo **CUSTOMCONNSTR\_** a esta variable, que es el código de las referencias anteriores **CUSTOMCONNSTR\_MONGOLAB\_URI.**

  [WebSiteEntry]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
  [WebSiteDashboardConfig]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
  [WebSiteConnectionStrings]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
  [SaveWebSite]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
