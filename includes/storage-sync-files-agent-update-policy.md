El agente de Azure File Sync se actualiza periódicamente con el fin de agregar nueva funcionalidad y solucionar los problemas. Se recomienda configurar Microsoft Update para obtener todas las actualizaciones del agente de Azure File Sync a medida que están disponibles.

#### <a name="major-vs-minor-agent-versions"></a>Versiones de agente principales y secundarias
* Con frecuencia, las versiones de agente principales contienen nuevas características y en la primera parte del número de versión tienen un número creciente. Por ejemplo: *2.\*.\**
* Las versiones de agente secundarias se llaman también "revisiones" y se lanzan con más frecuencia que las principales. Suelen contener correcciones de errores y mejoras más pequeñas, pero no características nuevas. Por ejemplo: *\*.3.\**

#### <a name="upgrade-paths"></a>Rutas de actualización
Existen tres métodos aprobados y probados de instalar las actualizaciones del agente de Azure File Sync. Estas rutas de actualización funcionan tanto con las versiones principales como las secundarias.
1. **(Preferida) Configurar Microsoft Update para descargar e instalar automáticamente las actualizaciones del agente.**  
    Se recomienda realizar siempre todas las actualizaciones de Azure File Sync para asegurarse de que tiene acceso a las correcciones más recientes del agente de servidor. Microsoft Update realiza este proceso íntegramente al descargar e instalar automáticamente estas actualizaciones.
2. **Aplicar revisiones a un agente de Azure File Sync existente mediante un archivo de revisiones de Microsoft Update o un archivo ejecutable .msp. La actualización más reciente de Azure File Sync se puede descargar del [Catálogo de Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Al ejecutar un archivo ejecutable .msp, se actualiza la instalación de Azure File Sync con el mismo método usado automáticamente por Microsoft Update en la ruta de actualización anterior. Cuando se aplica una revisión de Microsoft Update, se realiza una actualización local de una instalación de Azure File Sync.
3. **Descargar el instalador del agente de Azure File Sync más reciente del [Centro de descarga de Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). La descarga del instalador es un paquete de Microsoft Installer o un archivo ejecutable .msi.**  
    Para actualizar una instalación existente del agente de Azure File Sync, desinstale la versión antigua e instale a continuación la versión más reciente del instalador descargado. El registro del servidor, los grupos de sincronización y cualquier otra configuración se mantienen durante la instalación de Azure File Sync.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantías de ciclo de vida y administración de cambios del agente
Azure File Sync es un servicio en la nube que permite la introducción continua de nuevas características y funcionalidades. Esto significa que una versión específica del agente de Azure File Sync solo se puede admitir por tiempo limitado. Para facilitar la implementación, las siguientes reglas garantizan que dispone de tiempo suficiente y le notifican que adapte las actualizaciones del agente en el proceso de administración de cambios:

- Las versiones principales del agente se admiten durante seis meses como mínimo desde la fecha de lanzamiento inicial.
- Se garantiza que, al menos durante tres meses, el soporte técnico de las versiones principales del agente se solapa. 
- Se emiten advertencias para los servidores registrados mediante un agente "expirará pronto" al menos tres meses antes de la expiración. Puede comprobar si un servidor registrado usa una versión anterior del agente en la sección de servidores registrados de un servicio de sincronización de almacenamiento.
- La duración de una versión secundaria del agente está enlazada a la versión principal asociada. Por ejemplo, cuando se lance la versión 3.0 del agente, se establecerá que las versiones 2.\* expiren todas juntas.

> [!Note]
> Al instalarse una versión del agente con un aviso de expiración se mostrará una advertencia, pero la instalación se realizará. Al intentar instalar una versión expirada del agente, o conectarse a ella, no se admite y se bloqueará.