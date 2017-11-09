## <a name="provision-the-solution"></a>Aprovisionamiento de la solución

Si no ha aprovisionado aún la solución preconfigurada de supervisión remota en su cuenta:

1. Inicie sesión en [azureiotsuite.com][lnk-azureiotsuite] con sus credenciales de la cuenta de Azure y haga clic en **+** para crear una nueva solución.
2. Haga clic en **Seleccionar** en el icono **Supervisión remota**.
3. Escriba un valor en **Nombre de la solución** para la solución preconfigurada de supervisión remota.
4. Seleccione la **región** y la **suscripción** que desea usar para aprovisionar la solución.
5. Haga clic en **Crear solución** para comenzar el proceso de aprovisionamiento. Este proceso normalmente tarda varios minutos en ejecutarse.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Espere a que se complete el proceso de aprovisionamiento
1. Haga clic en el icono de la solución con el estado **Aprovisionando**.
2. Observe los **estados de aprovisionamiento** , ya que los servicios de Azure se implementan en su suscripción de Azure.
3. Una vez que se complete el aprovisionamiento, el estado cambia a **Listo**.
4. Haga clic en el icono y verá los detalles de la solución en el panel derecho.

> [!NOTE]
> Si surgen problemas al implementar la solución preconfigurada, consulte [Permisos en el sitio azureiotsuite.com][lnk-permissions] y [Preguntas más frecuentes][lnk-faq]. Si los problemas persisten, cree un vale de servicio en el [portal][lnk-portal].
> 
> 

¿Hay detalles que esperaría ver que no aparezcan para su solución? Puede sugerirnos nuevas características en [User Voice](https://feedback.azure.com/forums/321918-azure-iot)(La voz del usuario).

[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-faq]: ../articles/iot-suite/iot-suite-v1-faq.md