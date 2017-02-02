## <a name="highly-available-solutions-with-azure-traffic-manager"></a>Soluciones de alta disponibilidad con Administrador de tráfico de Azure
Conviene averiguar si los requisitos de alta disponibilidad de su carga de trabajo se pueden cumplir solamente con el Administrador de tráfico de Azure, o bien necesita combinar el Administrador de tráfico con otros procesos u otras soluciones de DNS. Según sus necesidades, puede:

* **Usar solo Traffic Manager**. Si un 99,99 % de tiempo de actividad es suficiente para la carga de trabajo, puede usar el Administrador de tráfico únicamente. En caso de que se produzca un error en el servicio del Administrador de tráfico, los usuarios no podrán tener acceso a la carga de trabajo hasta que dicho servicio se restablezca.
* **Usar otra solución de administración de tráfico, junto con Azure Traffic Manager**. En caso de que se produzca un error en el servicio de Administrador de tráfico, puede cambiar el registro CNAME de forma que apunte a otro servicio de administración de tráfico. Se podrá seguir teniendo acceso a la carga de trabajo, distribuido a todas las ubicaciones donde la carga de trabajo se hospede. Se trata de la solución más costosa, pero puede ser necesaria en aquellas cargas de trabajo que necesiten un contrato de nivel de servicio superior.



<!--HONumber=Jan17_HO3-->


