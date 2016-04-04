<!--author=alkohli last changed: 03/17/16-->

## Solución de errores en actualización

**¿Qué ocurre si ve una notificación que indica que se ha producido un error en las comprobaciones previas a la actualización?**

Si se produce un error en la comprobación previa, asegúrese de que ha examinado la barra de notificación detallada en la parte inferior de la página. Esto proporciona una guía para saber en qué comprobación previa se ha producido el error. La siguiente ilustración muestra una instancia en la que aparece dicha notificación. En este caso, ni la comprobación de mantenimiento del controlador ni la comprobación de mantenimiento de los componentes de hardware han resultado satisfactorias. En la sección **Estado del hardware**, puede ver que los componentes de **Controller 0** y **Controller 1** necesitan atención.

  ![Error de comprobación previa](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

Es preciso asegurarse de que ambos controladores funcionan correctamente y están en línea. También necesitará asegurarse de que en la página de mantenimiento se muestra que todos los componentes de hardware del dispositivo StorSimple están en buen estado. A continuación, puede intentar instalar las actualizaciones. Si no puede corregir los problemas de los componentes de hardware, deberá ponerse en contacto con el servicio de soporte técnico de Microsoft para los pasos siguientes.

**¿Qué sucede si recibe el mensaje de error "Las actualizaciones no se pudieron instalar" y la recomendación es hacer referencia a la guía de solución de problemas de actualización para determinar la causa del error?**

Una causa probable podría ser que no tiene conectividad con los servidores de Microsoft Update. Esta es una comprobación manual que se debe realizar. Si se pierde la conexión con servidor de actualizaciones, se producirá un error en el trabajo de actualización. Para comprobar la conectividad, ejecute el siguiente cmdlet desde la interfaz de Windows PowerShell del dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Ejecute el cmdlet en ambos controladores.

Si ha comprobado que existe conectividad y sigue apareciendo este problema, póngase en contacto con servicio de soporte técnico de Microsoft para los pasos siguientes.

<!---HONumber=AcomDC_0323_2016-->