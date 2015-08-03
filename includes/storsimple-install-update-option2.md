
#### Para instalar la Actualización 1 desde el Portal de administración de Azure

1. En el Portal de administración, vaya a la página **Dispositivos** y seleccione un dispositivo.
 
2. Vaya a **Dispositivos** > **Configurar**.

3. En **Interfaces de red**, busque la interfaz de red que tiene asignada una puerta de enlace. Se trata de una interfaz de red que no sea DATA 0.

4. Borre la configuración de la puerta de enlace. Tenga en cuenta que dado que se requiere la configuración de puerta de enlace en una interfaz de red habilitada para la nube, deberá deshabilitar el acceso a la nube para que esta interfaz pueda borrar la configuración.

5. Repita el paso 4 para cualquier otra interfaz de red que tenga una puerta de enlace asignada (excluido DATO 0).

6. Guarde la configuración modificada.

7. Ya puede [usar el Portal de administración para instalar la actualización 1](#use-the-management-portal-to-install-update-1).

<!---HONumber=July15_HO4-->