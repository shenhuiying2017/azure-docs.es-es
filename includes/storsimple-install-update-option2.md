<!--author=SharS last changed: 9/17/15-->

#### Para instalar la Actualización 1.2 desde el Portal de administración de Azure

1. En el Portal de administración, vaya a la página **Dispositivos** y seleccione un dispositivo.
 
2. Vaya a **Dispositivos** > **Configurar**.

3. En **Interfaces de red**, compruebe primero que tiene al menos una interfaz de red que está habilitada para iSCSI. A continuación busque la interfaz de red (que no sea DATA 0) que tiene asignada una puerta de enlace.

4. Deshabilite la interfaz de red que tenga asignada una puerta de enlace y guarde la configuración modificada. Observe que se conserva la configuración de la interfaz de red, por lo que, cuando se vuelva a habilitar esta interfaz más adelante, el portal se revertirá a la configuración original.

7. Ya puede [usar el Portal de administración para instalar la Actualización 1.2](#use-the-management-portal-to-install-update-1). Siga las instrucciones a partir del paso 3 de este procedimiento. Después de haber instalado todas las actualizaciones, puede volver a habilitar la interfaz de red que deshabilitó.

<!---HONumber=Oct15_HO3-->