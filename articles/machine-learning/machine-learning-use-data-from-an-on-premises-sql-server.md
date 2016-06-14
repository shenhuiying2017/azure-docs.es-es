<properties
pageTitle="Uso de datos de una base de datos de SQL Server local en Aprendizaje automático | Azure"
description="Use datos de una base de datos de SQL Server local para llevar a cabo análisis avanzados con Aprendizaje automático de Azure."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="05/25/2016"
ms.author="garye;krishnan"/>

# Análisis avanzados con Aprendizaje automático de Azure con datos de una base de datos de SQL Server local


Con frecuencia, a las compañías que trabajan con datos locales les gustaría aprovechar la escala y la agilidad de la nube para su cargas de trabajo de aprendizaje de automático. Sin embargo, no desean interrumpir los flujos de trabajo y los procesos de negocio actuales por mover sus datos locales a la nube. Ahora, Aprendizaje automático de Azure admite la lectura de los datos de una base de datos de SQL Server local, seguida del entrenamiento y la puntuación de un modelo con estos datos. Ya no tendrá que copiar y sincronizar de forma manual los datos entre la nube y el servidor local. En su lugar, el módulo **Importar datos** de Estudio de aprendizaje automático de Azure ahora puede leer directamente la base de datos de SQL Server local para los trabajos de entrenamiento y puntuación.

En este artículo se proporciona información general sobre la entrada de datos de SQL Server locales en Aprendizaje automático de Azure. Se da por supuesto que conoce conceptos de Aprendizaje automático de Azure tales como áreas de trabajo, módulos, conjuntos de datos, experimentos, *etc.*

> [AZURE.NOTE] Esta característica no está disponible para las áreas de trabajo gratuitas. Para más información acerca de los niveles y los precios de Aprendizaje automático, consulte [Precios de Aprendizaje automático](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Instalación de Microsoft Data Management Gateway

Para acceder a una base de datos de SQL Server local en Aprendizaje automático de Azure, debe descargar e instalar Microsoft Data Management Gateway. Al configurar la conexión de puerta de enlace en Estudio de aprendizaje automático, tendrá la oportunidad de descargar e instalar la puerta de enlace con el cuadro de diálogo **Download and register data gateway** (Descargar y registrar puerta de enlace) que se describe a continuación.

También puede instalar Data Management Gateway antes si descarga y ejecuta el paquete de instalación MSI desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Elija la versión más reciente, de 32 bits o 64 bits según corresponda a su equipo. El archivo MSI también puede usarse para actualizar una instancia de Data Management Gateway existente a la versión más reciente, sin que se pierda la configuración existente.

La puerta de enlace cuenta con los siguientes requisitos previos:

- Las versiones de sistema operativo Windows compatibles son Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.
- La configuración recomendada para el equipo de la puerta de enlace es de al menos 2 GHz, 4 núcleos, 8 GB de RAM y un disco de 80 GB.
- Si el equipo host está en hibernación, la puerta de enlace no podrá responder a las solicitudes de datos. Por tanto, configure un plan de energía adecuado en el equipo antes de instalar la puerta de enlace. Durante la instalación de la puerta de enlace, se muestra un mensaje si el equipo está configurado para hibernar.
- Dado que la actividad de copia sucede con una frecuencia determinada, el uso de recursos (CPU, memoria) en el equipo también sigue el mismo patrón con horas pico y de inactividad. El uso de recursos también depende en gran medida de la cantidad de datos que se mueven. Cuando haya varios trabajos de copia en curso, observará que el uso de los recursos aumenta durante las horas pico. Aunque la configuración mínima de arriba es técnicamente suficiente, se recomienda una con más recursos en función de la carga específica para el movimiento de datos.

Debe considerar lo siguiente cuando configure y use una instancia de Data Management Gateway:

- Solo puede instalar una instancia de Data Management Gateway en un solo equipo.
- Puede usar una sola puerta de enlace para varios orígenes de datos locales.
- Puede conectar varias puertas de enlace en diferentes equipos al mismo origen de datos local.
- Una puerta de enlace se configura para una única área de trabajo al mismo tiempo. Las puertas de enlace no se pueden compartir entre áreas de trabajo en este momento.
- Puede configurar varias puertas de enlace para una sola área de trabajo. Por ejemplo, podría usar una puerta de enlace que esté conectada a los orígenes de datos de prueba durante el desarrollo, y una de producción cuando esté listo para ponerla en operación.
- La puerta de enlace no necesita encontrarse en el mismo equipo que el origen de datos pero, si está cerca de él, tarda menos tiempo en conectarse al origen de datos. Se recomienda que instale la puerta de enlace en un equipo diferente del que hospeda el origen de datos local para que la puerta de enlace y el origen de datos no compitan por los recursos.
- Si ya tiene una puerta de enlace instalada en el equipo que atiende los escenarios de Power BI o Data Factory de Azure, instale en otro equipo una independiente para Aprendizaje automático de Azure. 

    > [AZURE.NOTE] No puede ejecutar Data Management Gateway y Power BI Gateway en el mismo equipo.

- Debe usar la instancia de Data Management Gateway para Aprendizaje automático de Azure incluso si utiliza Azure ExpressRoute para otros datos. Debe tratar el origen de datos como si fuera local (que está tras un firewall) incluso si usa ExpressRoute, y utilizar Data Management Gateway para establecer la conectividad entre Aprendizaje automático y el origen de datos.

Puede encontrar información detallada sobre los requisitos previos de instalación, los pasos de instalación y sugerencias para solucionar problemas en las secciones del principio del artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

## <span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Entrada de datos de la base de datos de SQL Server local en Aprendizaje automático de Azure


En este tutorial, instalará una instancia de Data Management Gateway en un área de trabajo de Aprendizaje automático de Azure, la configurará y después leerá datos de una base de datos de SQL Server local.

> [AZURE.TIP] Antes de comenzar, deshabilite el bloqueador de elementos emergentes del explorador para `studio.azureml.net`. Si usa el explorador Google Chrome, descargue e instale uno de los varios complementos disponibles en Google Chrome WebStore entre las [extensiones de aplicación ClickOnce](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### Paso 1: Creación de una puerta de enlace

El primer paso consiste en crear y configurar la puerta de enlace para acceder a la base de datos SQL local.

1.  Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/Home/) y seleccione el área de trabajo donde desea trabajar.

2.  Haga clic en la hoja **SETTINGS** (Configuración) a la izquierda y en la pestaña **DATA GATEWAYS** (Puertas de enlace de datos) en la parte superior.

3.  Haga clic en **NEW DATA GATEWAY** (Nueva puerta de enlace de datos) en la parte inferior de la pantalla.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4.  En el cuadro de diálogo **New data gateway** (Nueva puerta de enlace de datos), escriba un valor en **Gateway Name** (Nombre de puerta de enlace) y, opcionalmente, en **Description** (Descripción). Haga clic en la flecha situada en la esquina inferior derecha para ir al siguiente paso de la configuración.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5.  En el diálogo Download and register data gateway (Descargar y registrar puerta de enlace), copie el valor de GATEWAY REGISTRATION KEY (Clave de registro de la puerta de enlace) en el Portapapeles.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6.  <span id="note-1" class="anchor"></span>Si aún no ha descargado ni instalado Microsoft Data Management Gateway, haga clic en **Download data management gateway** (Descargar Data Management Gateway). Esto lo lleva al Centro de descarga de Microsoft, donde puede seleccionar la versión de puerta de enlace que necesita, descargarla e instalarla. Puede encontrar información detallada sobre los requisitos previos de instalación, los pasos de instalación y sugerencias para solucionar problemas en las secciones del principio del artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7.  Una vez instalada la puerta de enlace, se abre el Administrador de configuración de Data Management Gateway y se muestra el cuadro de diálogo **Registrar puerta de enlace**. Pegue la **clave de registro de puerta de enlace** que copió en el Portapapeles y haga clic en **Registrar**.

8.  Si ya tiene una puerta de enlace instalada, ejecute el Administrador de configuración de Data Management Gateway, haga clic en **Cambiar la clave**, pegue la **clave de registro de puerta de enlace** que copió en el Portapapeles y haga clic en **Aceptar**.

9.  Cuando la instalación haya finalizado, se muestra el cuadro de diálogo **Registrar puerta de enlace** para el Administrador de configuración de Data Management Gateway. Pegue la clave de registro de puerta de enlace que copió en el Portapapeles y haga clic en **Registrar**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10.  La configuración de puerta de enlace está completa cuando se establecen los siguientes valores en la pestaña **Inicio** del Administrador de configuración de Microsoft Data Management Gateway:

    -   En **Nombre de puerta de enlace** y **Nombre de instancia** aparece el nombre de la puerta de enlace.

    -   **Registro** está establecido en **Registrado**.

    -   El **Estado** está establecido en **Iniciado**.

    -   La barra de estado de la parte inferior muestra **Conectado al servicio en la nube Data Management Gateway** junto con una marca de verificación verde.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

    También se actualiza Estudio de aprendizaje automático de Azure cuando el registro está completado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11.  En el cuadro de diálogo **Download and register data gateway** (Descargar y registrar puerta de enlace), haga clic en la marca de verificación para completar la instalación. En la página **Settings** (Configuración), se muestra el estado de la puerta de enlace como "Online" (En línea). En el panel derecho, encontrará el estado y otra información útil.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. En el Administrador de configuración de Microsoft Data Management Gateway, cambie a la pestaña **Certificado**. El certificado especificado en esta pestaña se usa para cifrar y descifrar las credenciales para el almacén de datos local que especifique en el portal. Este es el certificado predeterminado generado. Microsoft recomienda que lo cambie al suyo propio que esté en una copia de seguridad en el sistema de administración de certificados. Haga clic en **Cambiar** para usar su propio certificado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (opcional) Si desea habilitar el registro detallado para solucionar problemas con la puerta de enlace, en el Administrador de configuración de Microsoft Data Management Gateway, cambie a la pestaña **Diagnóstico** y active la casilla **Habilitar el registro detallado para la solución de problemas**. La información de registro se encuentra en el Visor de eventos de Windows, en el nodo **Registros de aplicaciones y servicios** -> **Data Management Gateway**. También puede usar la pestaña **Diagnóstico** para probar la conexión a un origen de datos local mediante la puerta de enlace.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Con esto, se completa el proceso de configuración de la puerta de enlace en Aprendizaje automático de Azure. Ya está listo para usar los datos locales.

Puede crear y configurar varias puertas de enlace en Estudio para cada área de trabajo. Por ejemplo, puede conectar una puerta de enlace a los orígenes de datos de prueba durante el desarrollo, y una puerta de enlace diferente para los orígenes de datos de producción. Aprendizaje automático de Azure ofrece la flexibilidad para configurar varias puertas de enlace en función de su entorno corporativo. Actualmente no se puede compartir una puerta de enlace entre áreas de trabajo y solamente se puede instalar una única puerta de enlace en un equipo. Para más información sobre la instalación de la puerta de enlace, consulte [Consideraciones para utilizar Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) en el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### Paso 2: Uso de la puerta de enlace para leer datos de un origen de datos local

Después de configurar la puerta de enlace, puede agregar un módulo **Importar datos** a un experimento que introduce los datos de la base de datos de SQL Server local.

1.  En Estudio de aprendizaje automático, seleccione la pestaña **EXPERIMENTS** (Experimentos), haga clic en la pestaña **+NEW** (+Nuevo) en la esquina inferior izquierda y seleccione **Blank Experiment** (Experimento en blanco). También puede seleccionar uno de los varios experimentos de ejemplo disponibles.

2.  Busque y arrastre el módulo **Importar datos** al lienzo del experimento.

3.  Haga clic en **Save as** (Guardar como) bajo el lienzo. Escriba "Tutorial de SQL Server local en Aprendizaje automático de Azure" como nombre del experimento, seleccione el área de trabajo y haga clic en la marca de verificación de **Aceptar**.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Haga clic en el módulo **Importar datos** para seleccionarlo y, en el panel **Properties** (Propiedades) a la derecha del lienzo, seleccione "Base de datos SQL local" en la lista desplegable **Data source** (Origen de datos).

5.  Seleccione en **Data gateway** (Puerta de enlace de datos) la puerta de enlace que instaló y registró. Puede configurar otra si selecciona "(add new Data Gateway…)" (Agregar nueva puerta de enlace de datos).

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Escriba el servidor SQL en **Database server name** (Nombre del servidor de base de datos) y un valor para **Database name** (Nombre de base de datos), junto con el texto para **Database query** (Consulta de base de datos) para la consulta de base de datos SQL que desea ejecutar.

7.  Haga clic en **Enter values** (Escribir valores) en **User name and password** (Nombre de usuario y contraseña) y escriba sus credenciales de base de datos. Puede usar la autenticación integrada de Windows o la autenticación de SQL Server según la configuración de SQL Server local.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    El mensaje "values required" (valores necesarios) cambiará a "values set" (valores establecidos) con una marca de verificación verde. Basta con que escriba las credenciales una vez, a menos que la información de la base de datos o la contraseña cambien. Aprendizaje automático de Azure usa el certificado que proporcionó al instalar la puerta de enlace para cifrar las credenciales en la nube. Azure nunca almacenará credenciales locales sin cifrado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Haga clic en **RUN** (Ejecutar) para ejecutar el experimento.

Una vez finalizado el experimento, puede visualizar los datos importados desde la base de datos si hace clic en el puerto de salida del módulo **Importar datos** y selecciona **Visualizar**.

Una vez que haya terminado de desarrollar el experimento, puede implementar el modelo y ponerlo en operación. Mediante el servicio de ejecución por lotes, se leerán los datos de la base de datos de SQL Server local configurada en el módulo **Importar datos** y se usarán para la puntuación. Aunque puede usar el servicio de solicitud-respuesta para puntuar datos locales, Microsoft recomienda usar el [complemento de Excel](machine-learning-excel-add-in-for-web-services.md) en su lugar. Actualmente, no se admite la escritura en una base de datos de SQL Server local mediante **Exportar datos**, ni en experimentos ni en servicios web publicados.

<!---HONumber=AcomDC_0601_2016-->