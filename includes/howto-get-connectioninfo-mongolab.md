Cuando realiza el aprovisionamiento en una base de datos MongoLab, MongoLab transmite un URI de conexión a Azure en el formato de cadena de conexión estándar de MongoDB. Este valor se usa para iniciar una conexión de MongoDB mediante su elección del controlador de MongoDB. Para obtener más información sobre las cadenas de conexión, consulte
[Connections][1] en mongodb.org.

**El URI contiene el nombre del usuario de la base de datos y la contraseña. Dele el mismo tratamiento que a la información confidencial y no lo comparta.**

Realice los siguientes pasos para recuperar el URI en el Portal de Azure:

1.  Seleccione **Add-ons**.  
     ![AddonsButton](./media/howto-get-connectioninfo-mongolab/button-addons.png)
2.  Busque el servicio de MongoLab en su lista de complementos.  
     ![MongolabEntry](./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png)
3.  Haga clic en el nombre del complemento para ir a la página de
    complementos.
4.  Haga clic en **Connection Info**.
	![ConnectionInfoButton](./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png)
    
    Se mostrará el URI de MongoLab:
	![ConnectionInfoScreen](./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png)
5.  Haga clic en el botón del portapapeles a la derecha del valor
    MONGOLAB\_URI para copiar el valor completo al portapapeles.



[1]: http://www.mongodb.org/display/DOCS/Connections