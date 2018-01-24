| Recurso | Gratuito | Compartido (Vista previa) | Básica | Estándar | Premium (vista previa)</th> |
| --- | --- | --- | --- | --- | --- |
| [Aplicaciones web, móviles o de API](https://azure.microsoft.com/services/app-service/) por [plan de App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |
| [Logic Apps](https://azure.microsoft.com/services/app-service/logic/) por [plan de App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 por núcleo |20 por núcleo |
| [plan de App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 por región |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |
| Tipo de instancia de proceso |Compartido |Compartido |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |
| [Escalado horizontal](../articles/app-service/web-sites-scale.md) (instancias máximas) |1 compartido |1 compartido |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |20 dedicados (50 en ASE)<sup>3.4</sup> |
| Almacenamiento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| Tiempo de CPU (5 min)<sup>6</sup> |3 minutos |3 minutos |Sin límite, se paga según las [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> estándar. |Sin límite, se paga según las tarifas estándar |Sin límite, se paga según las tarifas estándar |
| Tiempo de CPU (día)<sup>6</sup> |60 minutos |240 minutos |Sin límite, se paga según las [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> estándar. |Sin límite, se paga según las tarifas estándar |Sin límite, se paga según las tarifas estándar |
| Memoria (1 hora) |1.024 MB por plan de App Service |1.024 MB por aplicación |N/D |N/D |N/D |
| Ancho de banda |165 MB |Ilimitado; se aplican [tasas por transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitado; se aplican tasas por transferencia de datos. |Ilimitado; se aplican tasas por transferencia de datos. |Ilimitado; se aplican tasas por transferencia de datos. |
| Arquitectura de la aplicación |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Web Sockets por instancia<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |
| Conexiones de depurador [simultáneas](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) por aplicación |1 |1 |1 |5 |5 |
| [subdominio de azurewebsites.net con FTP/S y SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Compatibilidad con dominio](../articles/app-service/app-service-web-tutorial-custom-domain.md) personalizado | |X |X |X |X |
| Compatibilidad con dominio [Compatibilidad con SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Conexiones SSL SNI ilimitadas |Se incluyen conexiones SNI SSL ilimitadas y 1 conexión SSL de IP |Se incluyen conexiones SNI SSL ilimitadas y 1 conexión SSL de IP |
| Equilibrador de carga integrado | |X |X |X |X |
| [Siempre activado](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Copias de seguridad programadas](../articles/app-service/web-sites-backup.md) | | | | Copias de seguridad programadas cada 2 horas, con un máximo de 12 copias de seguridad al día (manuales y programadas). | Copias de seguridad programadas cada hora, con un máximo de 50 copias de seguridad al día (manuales y programadas). |
| [Escala automática](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Compatibilidad con Azure Scheduler](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |
| [Supervisión de extremos](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Ranuras de ensayo](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20  |
| Dominios personalizados por aplicación</a> | |500 |500 |500 |500 |
| Contrato de nivel de servicio | |<p> |99,9 % |99,95%<sup>10</sup> |99,95 %<sup>9</sup> |

<sup>1</sup>Las aplicaciones y las cuotas de almacenamiento son por plan de App Service, a menos que se indique lo contrario.  
<sup>2</sup>El número real de aplicaciones que puedes hospedar en estas máquinas depende de la actividad de las aplicaciones, el tamaño de las instancias de máquina y el correspondiente uso de los recursos.  
<sup>3</sup>Las instancias dedicadas pueden tener diferentes tamaños. Consulte [Precios de App Service](https://azure.microsoft.com/pricing/details/app-service/) para obtener más detalles.  
<sup>4</sup>El nivel Premium permite hasta 50 instancias de proceso (en función de la disponibilidad) y 500 GB de espacio en disco cuando se usa en entornos de App Service, y 20 instancias de proceso y 250 GB de almacenamiento, en caso contrario.  
<sup>5</sup>El límite de almacenamiento es el tamaño total del contenido en todas las aplicaciones en el mismo plan de App Service. Hay más opciones de almacenamiento en [App Service Environment](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage).  
<sup>6</sup>Estos recursos están limitados por los recursos físicos en las instancias dedicadas (el tamaño de la instancia y el número de instancias).  
<sup>7</sup>Si se escala una aplicación en el nivel Básico a dos instancias, dispone de 350 conexiones simultáneas para cada una de las dos.  
<sup>8</sup>Ejecute scripts o archivos ejecutables personalizados bajo demanda, según una programación o de manera continua como tarea en segundo plano dentro de su instancia de App Service. Siempre disponible se requiere para la ejecución continua de Trabajos web. Se requiere Azure Scheduler de nivel Gratis o Estándar para Trabajos web programados. No hay ningún límite predefinido en el número de WebJobs que puede ejecutar en una instancia de App Service, pero sí hay límites prácticos que dependen de lo que el código de la aplicación esté intentando hacer.   
<sup>9</sup>Acuerdo de nivel de servicio (SLA) del 99,95 % que se proporciona para realizar implementaciones que usan varias instancias con Azure Traffic Manager configurado para la conmutación por error.  

