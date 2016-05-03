## Solución de problemas de conectividad
Use la pestaña **Diagnósticos** del **Administrador de configuración de Data Management Gateway** para solucionar problemas de conexión.

1. Inicie el **Administrador de configuración de Data Management Gateway**. Puede ejecutar "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe" directamente, o bien buscar **Gateway** para encontrar un vínculo a la aplicación **Microsoft Data Management Gateway**, tal como se muestra en la imagen siguiente. 

	![Buscar puerta de enlace](./media/data-factory-troubleshoot-connectivity/search-gateway.png)
2. Cambie a la pestaña **Diagnósticos**.

	![Diagnóstico de puerta de enlace](./media/data-factory-troubleshoot-connectivity/data-factory-gateway-diagnostics.png) 
3. Seleccione el **tipo** de almacén de datos (el servicio vinculado). 
4. Especifique la **autenticación** y escriba las **credenciales**, o bien escriba la **cadena de conexión** para conectarse al almacén de datos. 
5. Haga clic en **Probar conexión** para probar la conexión con el almacén de datos. 

<!---HONumber=AcomDC_0420_2016-->