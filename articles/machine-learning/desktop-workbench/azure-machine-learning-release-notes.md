---
title: Novedades de Azure Machine Learning
description: En este documento se detallan las actualizaciones de Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: e30943426ad68171e1464f828a9c8672b06c975a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Novedades de Azure Machine Learning

En este artículo aprenda sobre las nuevas versiones de [Azure Machine Learning Services](../service/overview-what-is-azure-ml.md). 

## <a name="2018-03-sprint-4"></a>2018-03 (Sprint 4)
**Número de versión**: 0.1.1801.24353  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([busque su versión](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Esta es la quinta actualización de Azure Machine Learning Workbench. Muchas de las actualizaciones siguientes son consecuencia directa de esos comentarios. ¡No deje de enviarlos!

**Nuevas características y cambios destacables**

- Compatibilidad para ejecutar los scripts en máquinas virtuales de Ubuntu remotas de forma nativa en su propio entorno, además la ejecución basada en docker remoto.
- La nueva experiencia de entorno en la aplicación Workbench le permite crear destinos de proceso y ejecutar configuraciones además de la experiencia basada en la CLI.
![Pestaña Entornos](media/azure-machine-learning-release-notes/environment-page.png)
- Informes de historial de ejecución personalizables ![Imagen de los nuevos informes de historial de ejecución](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Actualizaciones detalladas**

A continuación figura una lista de actualizaciones detalladas de cada área de componente de Azure Machine Learning en esta versión de sprint.

### <a name="workbench-ui"></a>Interfaz de usuario de Workbench
- Informes de historial de ejecución personalizables
  - Configuración de gráficos mejorada para los informes de historial de ejecución
    - Se pueden cambiar los puntos de entrada utilizados.
    - Se pueden agregar y modificar filtros de nivel superior. ![Agregar filtros](media/azure-machine-learning-release-notes/add-filters.jpg).
    - Se pueden agregar o modificar gráficos y estadísticas (y reorganizarlos mediante arrastrar y colocar).
    ![Creación de nuevos gráficos](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD para informes de historial de ejecución
  - Se han movido todas las configuraciones de vistas de listas de historial de ejecución existentes a los informes del lado servidor, que actúan como canalizaciones en las ejecuciones desde los puntos de entrada seleccionados.

- Pestaña Entornos
  - Agregue fácilmente nuevos archivos de destino de proceso y de archivos de configuración de ejecución en el proyecto ![Nuevo destino de proceso](media/azure-machine-learning-release-notes/add-new-environments.png).
  - Administre y actualice los archivos de configuración con la experiencia del usuario simple, basada en formularios
  - Nuevo botón para preparar los entornos para ejecución

- Mejoras de rendimiento en la lista de archivos de la barra lateral

### <a name="data-preparation"></a>Preparación de los datos 
- Azure Machine Learning Workbench ahora le permite buscar una columna mediante el nombre de una columna conocida.


### <a name="experimentation"></a>Experimentación
- Azure Machine Learning Workbench admite ahora la ejecución de los scripts de forma nativa en su propio entorno de python o pyspark. Para esta funcionalidad, el usuario crea y administra su propio entorno en la máquina virtual remota y utiliza Azure Machine Learning Workbench para ejecutar sus scripts en ese destino. Consulte [Configuración del servicio Experimentación de Azure Machine Learning](experimentation-service-configuration.md). 

### <a name="model-management"></a>Administración de modelos
- Compatibilidad con la personalización de los contenedores implementados: permite personalizar la imagen del contenedor al permitir la instalación de bibliotecas externas mediante apt get, por ejemplo. Ya no se limita a las bibliotecas en las que se puede instalar PIP. Consulte la [documentación](model-management-custom-container.md) para más información.
  - Utilice la marca `--docker-file myDockerStepsFilename` y el nombre de archivo con los comandos de creación de manifiesto, imagen o servicio.
  - Tenga en cuenta que la imagen base es Ubuntu y no se puede modificar.
  - Comando de ejemplo: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>2018-01 (Sprint 3) 
**Número de versión**: 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([busque su versión](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

A continuación, encontrará las actualizaciones y mejoras de este sprint. Muchas de las actualizaciones descritas aquí son consecuencia directa de los comentarios de los usuarios. 


A continuación figura una lista de actualizaciones detalladas de cada área de componente de Azure Machine Learning en esta versión de sprint.

- Las actualizaciones de la pila de autenticación fuerzan el inicio de sesión y la selección de cuenta al inicio.

### <a name="workbench"></a>Workbench
- Capacidad para instalar o desinstalar la aplicación mediante la opción Agregar o quitar programas.
- Las actualizaciones de la pila de autenticación fuerzan el inicio de sesión y la selección de cuenta al inicio.
- Experiencia mejorada de inicio de sesión único (SSO) en Windows.
- Los usuarios que pertenecen a varios inquilinos con credenciales diferentes ahora podrán iniciar sesión en Workbench.

### <a name="ui"></a>UI
- Correcciones de errores y mejoras generales

### <a name="notebooks"></a>Cuadernos
- Correcciones de errores y mejoras generales

### <a name="data-preparation"></a>Preparación de los datos 
- Se han mejorado las sugerencias automáticas durante la realización de transformaciones By Example.
- Algoritmo mejorado para el inspector de frecuencia de patrones.
- Capacidad para enviar datos y comentarios de ejemplo durante la realización de transformaciones By Example. ![Imagen del vínculo par enviar comentarios sobre la transformación de la columna derivada](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Mejoras de Spark en tiempo de ejecución
- Scala ha reemplazado a Pyspark
- Se corrigió la incapacidad para cerrar datos no aplicables del inspector de series temporales. 
- Se corrigió el tiempo de suspensión de la ejecución de preparación de datos para HDI.

### <a name="model-management-cli-updates"></a>Actualizaciones de la CLI de Administración de modelos 
  - La propiedad de la suscripción ya no es necesaria para el aprovisionamiento de recursos. El acceso del colaborador al grupo de recursos será suficiente para configurar el entorno de implementación.
  - Se habilitó la configuración del entorno local para suscripciones gratuitas. 

## <a name="2017-12-sprint-2-qfe"></a>2017-12 (Sprint 2 QFE) 
**Número de versión**: 0.1.1711.15323  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([busque su versión](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Se trata de la versión QFE (ingeniería de corrección rápida), una versión secundaria. Aborda varios problemas de telemetría y ayuda al equipo del producto a comprender mejor cómo se utiliza el producto. Este conocimiento se puede utilizar en futuros esfuerzos para mejorar la experiencia del producto. 

Además, hay dos actualizaciones importantes:

- Se ha corregido un error de preparación de datos que ha impedido que el inspector de series de tiempo se muestre en los paquetes de preparación de datos.
- En la herramienta de línea de comandos, ya no es necesario ser propietario de una suscripción de Azure para aprovisionar clústeres de ACS de proceso de Machine Learning. 

## <a name="2017-12-sprint-2"></a>2017-12 (Sprint 2)
**Número de versión**: 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([busque su versión](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Esta es la tercera actualización de Azure Machine Learning. Esta actualización incluye mejoras en la aplicación de workbench, la interfaz de línea de comandos (CLI) y los servicios back-end. Le agradecemos enormemente sus comentarios, tanto positivos como negativos. Muchas de las actualizaciones siguientes son consecuencia directa de esos comentarios. 

**Nuevas características destacables**
- [Compatibilidad con SQL Server y Azure SQL Database como un origen de datos](data-prep-appendix2-supported-data-sources.md#types) 
- [Aprendizaje profundo sobre Spark con compatibilidad con GPU mediante MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Todos los contenedores de AML son compatibles con dispositivos de Azure IoT Edge cuando se implementan (no se requieren pasos adicionales)](http://aka.ms/aml-iot-edge-blog)
- Lista de modelos disponibles y vistas de detalle disponibles en Azure Portal
- Acceso a los destinos de proceso mediante la autenticación basada en claves SSH además del acceso basado en nombre de usuario y contraseña. 
- Nuevo inspector de frecuencia de patrones en la experiencia de preparación de datos. 

**Actualizaciones detalladas** A continuación figura una lista de la actualizaciones detalladas de cada área de componente de Azure Machine Learning en este sprint.

### <a name="installer"></a>Instalador
- El instalador puede actualizarse automáticamente de modo que las correcciones de errores y las nuevas características pueden admitirse sin que el usuario tenga que volver a instalarlas.

### <a name="workbench-authentication"></a>Autenticación de Workbench
- Varias correcciones para el sistema de autenticación. Háganos saber si sigue experimentando problemas de inicio de sesión.
- Cambios en la IU que facilitan la búsqueda de la configuración del administrador del proxy.

### <a name="workbench"></a>Workbench
- La vista de archivo de solo lectura ahora tiene fondo azul claro.
- El botón Editar se ha movido a la derecha para que sea más reconocible.
- Los formatos de archivo "dsource", "dprep" y "ipynb" ahora se pueden representar en formato de texto sin formato.
- Ahora, Workbench cuenta con una nueva experiencia de edición que guía a los usuarios hacia el uso de IDE externos para editar scripts y el uso de Workbench solo para editar los tipos de archivos que tienen una experiencia de edición avanzada (por ejemplo, blocs de notas, orígenes de datos y paquetes de preparación de datos).
- La carga de la lista de áreas de trabajo y proyectos a los que el usuario tiene acceso es significativamente más rápida ahora.

### <a name="data-preparation"></a>Preparación de los datos 
- Un inspector de frecuencia de patrones para ver los patrones de cadena en una columna. También puede filtrar los datos mediante estos patrones. Esto muestra una vista similar al inspector de recuentos de valores. La diferencia es que la frecuencia de patrones muestra los recuentos de los patrones únicos de los datos, en lugar de los recuentos de datos únicos. También puede filtrar o no todas las filas que se ajusten a un determinado patrón.

![Imagen del inspector de frecuencia de patrones con en el número de producto](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Mejoras de rendimiento al recomendar casos extremos para revisar en la transformación 'derivar columna por ejemplo'.

- [Compatibilidad con SQL Server y Azure SQL Database como un origen de datos](data-prep-appendix2-supported-data-sources.md#types) 

![Imagen de creación de un nuevo origen de datos de SQL Server](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Vista "rápida" de recuentos de filas y columnas habilitada.

![Imagen rápida del número de columnas de fila](media/azure-machine-learning-release-notes/row-col-count.png)

- La preparación de datos está habilitada en todos los contextos de proceso.
- Los orígenes de datos que utilizan una base de datos SQL Server están habilitados en todos los contextos de proceso
- Las columnas de cuadrícula de preparación de datos se pueden filtrar por tipo de datos.
- Problema corregido con la conversión de varias columnas a fecha.
- Se ha corregido el problema por el que el usuario podía seleccionar la columna de salida como un origen en Derivación de columnas por ejemplos si el usuario cambiaba el nombre de la columna de salida en el modo avanzado.

### <a name="job-execution"></a>Ejecución de trabajos
Ahora puede crear un destino de proceso de tipo de clúster o docker remoto y tener acceso a él mediante la autenticación basada en claves SSH siguiendo estos pasos:
- Adjunte un destino de proceso mediante el siguiente comando de la CLI.

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>La opción -k (o --use-azureml-ssh-key) del comando especifica generar y utilizar la clave SSH.

- Azure ML Workbench generará una clave pública y la transmitirá en la consola. Inicie sesión en el destino de proceso usando el mismo nombre de usuario y anexe el archivo ~/.ssh/authorized_keys con esta clave pública.

- Puede preparar este destino de proceso y usarlo para la ejecución y el área de trabajo de Azure ML Workbench usará esta clave para la autenticación.  

Para más información sobre la creación de destinos de proceso, consulte [Configuración del servicio Experimentación de Azure Machine Learning](experimentation-service-configuration.md).

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools para AI
- Compatibilidad agregada para [Visual Studio Tools para AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interfaz de línea de comandos (CLI)
- El comando `az ml datasource create` agregado permite la creación de un archivo de origen de datos desde la línea de comandos.

### <a name="model-management-and-operationalization"></a>Operacionalización y administración de modelos
- [Todos los contenedores de AML son compatibles con dispositivos de Azure IoT Edge cuando se operacionalizan (no se requieren pasos adicionales)](http://aka.ms/aml-iot-edge-blog) 
- Mejoras de mensajes de error en la CLI o16n
- Correcciones de errores en UX del portal de administración de modelos  
- Uso coherente de mayúsculas y minúsculas de letras para los atributos de administración de modelos en la página de detalles
- Tiempo de espera de llamadas de puntuación en tiempo real establecido en 60 segundos
- Lista de modelos disponibles y vistas de detalle disponibles en Azure Portal

![detalles del modelo en el portal](media/azure-machine-learning-release-notes/model-list.jpg)

![introducción al modelo en el portal](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Aprendizaje profundo sobre Spark con [compatibilidad con GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Compatibilidad con plantillas de Resource Manager para la implementación sencilla de recursos
- Compatibilidad con el ecosistema de SparklyR
- [Integración de AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Proyectos de ejemplo
- Ejemplos de [Iris](https://github.com/Azure/MachineLearningSamples-Iris) y [MMLSpark](https://github.com/Azure/mmlspark) actualizado con la nueva versión del SDK de Azure ML

### <a name="breaking-changes"></a>Cambios drásticos
- Se ha promovido el modificador `--type` de `az ml computetarget attach` a un subcomando. 

    - `az ml computetarget attach --type remotedocker` ahora es `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` ahora es `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>2017-11 (Sprint 1) 
**Número de versión**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([busque su versión](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

En esta versión, hemos realizado mejoras de seguridad, estabilidad y mantenimiento en la aplicación Workbench, en la CLI y en el nivel de servicios back-end. Le agradecemos enormemente sus comentarios tanto positivos como negativos. Muchas de las actualizaciones descritas aquí son consecuencia directa de esos comentarios. ¡No deje de enviarlos!

### <a name="notable-new-features"></a>Nuevas características destacables
- Ahora, Azure Machine Learning está disponible en dos nuevas regiones de Azure: **Europa Occidental** y el **Sudeste Asiático**. Ambas se unen a las regiones ya incluidas de **Este de EE. UU. 2**, **Oeste del centro de EE. UU.** y **Este de Australia**, lo que eleva a cinco el número total de regiones de implementación.
- Hemos habilitado el resaltado de sintaxis de código de Python en la aplicación Workbench para que el código fuente de Python sea más fácil de leer y editar. 
- Ahora, puede iniciar su IDE favorito directamente desde un archivo, en lugar de desde el proyecto global.  Si abre un archivo en Workbench y, después, hace clic en "Editar", se abre el IDE (actualmente se admiten VS Code y PyCharm) con el archivo y proyecto actuales.  También puede hacer clic en la flecha situada junto al botón Editar para modificar el archivo en el editor de texto de Workbench.  Los archivos serán de solo lectura hasta que haga clic en Editar, lo que evita que se realicen cambios por error.
- Ahora, la conocida biblioteca de trazado `matplotlib` versión 2.1.0 se incluye con la aplicación Workbench.
- Hemos actualizado la versión de .NET Core a 2.0 para el motor de preparación de datos. Por lo tanto, ya no es necesario instalar OpenSSL mediante Homebrew al instalar una aplicación en Mac OS. Esto allana el terreno igualmente para más características de preparación de datos muy interesantes que llegarán en un futuro próximo. 
- Hemos habilitado una página principal de la aplicación específica de la versión; así, tendrá acceso a más notas de la versión relevantes y a avisos de actualización según cuál sea su versión actual de la aplicación.
- Ahora, si el nombre de usuario local contiene un espacio, la aplicación se puede instalar sin ningún tipo de problema. 

### <a name="detailed-updates"></a>Actualizaciones detalladas
Esta es una lista de actualizaciones detalladas de cada área de componente de Azure Machine Learning en este sprint.

#### <a name="installer"></a>Instalador
- Ahora, el instalador de la aplicación limpia el directorio de instalación que se creó con una versión anterior.
- Se ha corregido un error que hacía que el instalador se quedara bloqueado al llegar a 100 % en macOS High Sierra.
- Ahora, hay un vínculo directo al directorio del instalador para que el usuario pueda revisar los registros del instalador en caso de que haya un error en la instalación.
- Ahora, la instalación no da problemas a aquellos usuarios cuyo nombre de usuario contiene un espacio.

#### <a name="workbench-authentication"></a>Autenticación de Workbench
- Se ha incluido compatibilidad con la autenticación en el administrador de proxy.
- Ahora, los usuarios que están detrás de un firewall pueden iniciar sesión sin problemas. 
- La aplicación ya no se bloquea si un usuario tiene cuentas de experimentación en varias regiones de Azure y una de esas regiones deja de estar disponible.
- Cuando la autenticación no se ha completado y el cuadro de diálogo de autenticación sigue estando visible, la aplicación ya no intenta cargar el área de trabajo desde la memoria caché local.

#### <a name="workbench-app"></a>Aplicación Workbench
- El resaltado de sintaxis de código de Python está habilitado en el editor de texto.
- El botón Editar del editor de texto permite modificar el archivo en un IDE (se admiten VS Code y PyCharm) o en el editor de texto integrado.
- El editor de texto está en modo de solo lectura de forma predeterminada. 
- Ahora, el botón Guardar se muestra deshabilitado después de que el archivo actual se guarde, lo que hace que el proceso sea más limpio.
- Workbench guarda _todos_ los archivos sin guardar cuando se inicia una ejecución.
- Workbench recuerda la última área de trabajo usada en el equipo local, por lo que la abre automáticamente.
- Ahora, solo es posible ejecutar una única instancia de Workbench. Antes se podían iniciar varias instancias, lo que causaba problemas cuando se trabajaba en el mismo proyecto.
- La opción del menú Archivo "Open Project..." (Abrir proyecto) ahora se llama "Add Existing Folder as Project..." (Agregar carpeta existente como proyecto). 
- Ahora, el paso de una pestaña a otra es mucho más rápido.
- Se han agregado vínculos de ayuda al cuadro de diálogo de configuración de IDE.
- Ahora, el formulario de comentarios recuerda la dirección de correo electrónico que se especificó la última vez.
- Ahora, el área de texto del formulario de puntos positivos y negativos es más grande, por lo que nos puede enviar más comentarios. 
- El texto de ayuda del conmutador `--owner` en `az ml workspace create` se ha corregido.
- Hemos agregado un cuadro de diálogo "Acerca de" para que los usuarios puedan ver y copiar fácilmente el número de versión de la aplicación.
- Se ha agregado un elemento de menú "Sugerir una característica" al menú Ayuda.
- Ahora, el nombre de la cuenta de Experimentación aparece en la barra de título de la aplicación, antes de "Azure Machine Learning Workbench".
- Ahora, se muestra una página principal de la aplicación específica de la versión en función de la versión de la aplicación detectada.

#### <a name="data-preparation"></a>Preparación de los datos 
- Los sitios web externos ya no se pueden cargar desde el inspector de mapa para evitar posibles problemas de seguridad.
- Ahora, los inspectores de histograma y de recuento de valores disponen de una opción para mostrar el gráfico a escala logarítmica.
- Ahora, cuando hay un cálculo en curso, la barra de calidad de datos muestra un color diferente para indicar el estado "en proceso de cálculo".
- Ahora, las métricas de columna muestran estadísticas de las columnas de valores categóricos.
- El último carácter de los nombres de origen de datos ya no se trunca.
- Ahora, el paquete de preparación de datos permanece abierto cuando se cambia de pestaña, con lo cual el rendimiento mejora notablemente.
- Ahora, al alternar entre la vista de datos y la vista de métricas en un origen de datos, el orden de las columnas no se altera.
- Abrir un archivo `.dprep` o `.dsource` no válido ya no hace que Workbench se bloquee.
- Ahora, el paquete de preparación de datos puede usar rutas de acceso relativas para la salida de la transformación de _escritura en CSV_.
- Ahora, la transformación de _conservación de columna_ permite a los usuarios agregar más columnas al editar.
- Ahora, el menú _Replace this_ (Reemplazar este) abre realmente el cuadro de diálogo _Reemplazar valor_.
- Ahora, la transformación para _reemplazar valor_ funciona según lo previsto en lugar de producir un error.
- Ahora, el paquete de preparación de datos usa rutas de acceso absolutas al hacer referencia a archivos de datos fuera de la carpeta de proyecto, lo que permite ejecutar el paquete en un contexto local con una ruta de acceso absoluta al archivo de datos.
- Ahora, la opción _Full File_ (Archivo completo) se puede usar como estrategia de muestreo cuando se usa un blob de Azure como origen de datos.
- Ahora, el código de Python generado (a partir del paquete de preparación de datos) incluye CR y LF, lo que hace que sea fácil de usar en Windows.
- Ahora, el menú desplegable _Elegir métricas_ oculta las propiedades cuando se cambia a la vista de datos.
- Ahora, Workbench puede procesar archivos con formato parquet aun cuando esté usando tiempo de ejecución de Python. Antes, solo se podía usar Spark al procesar archivos parquet. 
- Filtrar valores en una columna con el tipo de datos _date_ ya no hace que el motor de preparación de datos se bloquee.
- Ahora, la vista de métricas respeta las actualizaciones de estrategia de muestreo.
- Ahora, los trabajos de muestreo remotos funcionan como deben.

#### <a name="job-execution"></a>Ejecución de trabajos
- Ahora, se incluye un argumento en el registro de historial de ejecuciones.
- Ahora, los trabajos iniciados en la CLI se muestran automáticamente en el panel de trabajo de historial de ejecuciones.
- Ahora, el panel de trabajos muestra los trabajos creados por usuarios invitados agregados al inquilino de Azure AD.
- Las acciones Cancelar y Eliminar del panel de trabajo son más estables.
- Ahora, al hacer clic en el botón Ejecutar, se activa un mensaje de error si los archivos de configuración tienen un formato incorrecto.
- Terminar la aplicación ya no interfiere con los trabajos iniciados en la CLI.
- Ahora, los trabajos iniciados en la CLI siguen generando salida estándar incluso después de transcurrida una hora de ejecución.
- Se muestran mensajes de error más adecuados cuando se produce un error en una ejecución del paquete preparación de datos en Python/PySpark.
- Ahora, `az ml experiment clean` limpia las imágenes de Docker también en las máquinas virtuales remotas.
- Ahora, `az ml experiment clean` funciona correctamente con destinos locales de macOS.
- Los mensajes de error que aparecen en las ejecuciones de Docker locales o remotas se borran y son más fáciles de leer.
- Se muestran mensajes de error más adecuados si el nombre del nodo principal del clúster de HDInsight no está formateado correctamente cuando se adjunta como un destino de ejecución.
- Se muestran mensajes de error más adecuados cuando no se encuentra el secreto en el servicio de credenciales. 
- La biblioteca de MMLSpark se ha actualizado para admitir Apache Spark 2.2.
- Ahora, MMLSpark incluye una transformación de codificación del asunto (codificación de malla) en documentos médicos.
- Ahora, la versión 2.1.0 de `matplotlib` se incluye en Workbench de forma integrada.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- Ahora, las búsquedas de nombres de bloc de notas funcionan correctamente en la vista de blocs de notas.
- Ahora, los blocs de notas se pueden eliminar en la vista de blocs de notas.
- Se ha agregado una nueva fase `%upload_artifact` para cargar los archivos creados en el entorno de ejecución de Notebook en el almacén de datos del historial de ejecuciones.
- Ahora, los errores de kernel aparecen en el estado de trabajo de Notebook para una depuración más sencilla.
- Ahora, el servidor de Jupyter se cierra correctamente cuando el usuario cierra sesión en la aplicación.

#### <a name="azure-portal"></a>Azure Portal
- Ahora, se pueden crear cuentas de Experimentación y cuentas de Administración de modelos en dos nuevas regiones de Azure: Europa Occidental y Sudeste Asiático.
- Ahora, el plan de DevTest de la cuenta de Administración de modelos solo está disponible cuando es el primero que se crea en la suscripción. 
- El vínculo de ayuda en Azure Portal se ha actualizado para apuntar a la página de documentación correcta.
- El campo de descripción se ha quitado de la página de detalles de la imagen de Docker, ya que no procede.
- Se han agregado detalles, como la configuración de escala automática y AppInsights, a la página de detalles del servicio web.
- Ahora, la página Administración de modelos se presenta aunque las cookies de terceros estén deshabilitadas en el explorador. 

#### <a name="operationalization"></a>Operacionalización
- Los servicios web que tengan "score" en el nombre ya no generan errores.
- Ahora, los usuarios pueden crear un entorno de implementación solo con acceso de colaborador a un grupo de recursos de Azure o a la suscripción. Ya no es necesario tener acceso de propietario en toda la suscripción.
- Ahora, la CLI de operacionalización incluye la finalización automática de pestañas en Linux.
- Ahora, el servicio de creación de imágenes permite crear imágenes para servicios y dispositivos de IoT de Azure.

#### <a name="sample-projects"></a>Proyectos de ejemplo
- Proyecto de ejemplo de [_clasificación de Iris_](./tutorial-classifying-iris-part-1.md):
    - `iris_pyspark.py` cambia su nombre a `iris_spark.py`.
    - `iris_score.py` cambia su nombre a `score_iris.py`.
    - `iris.dprep` y `iris.dsource` se han actualizado para reflejar las últimas actualizaciones de motor de preparación de datos.
    - El bloc de notas `iris.ipynb` se ha modificado y ahora funciona en el clúster de HDInsight.
    - El historial de ejecuciones está activado en la celda del bloc de notas `iris.ipynb`.
- Se ha corregido el paso de control del valor de error del proyecto de ejemplo [_Preparación avanzada de datos con Bike Share Data_](./tutorial-bikeshare-dataprep.md).
- El formato de `docker.runconfig` del proyecto de ejemplo [_MMLSpark on Adult Census Data_](https://github.com/Azure/MachineLearningSamples-mmlspark) (MMLSpark en datos de censo de adultos) se ha actualizado de JSON a YAML.
- El formato de `docker.runconfig` del proyecto de ejemplo [_Ajuste distribuido de hiperparámetros_](./scenario-distributed-tuning-of-hyperparameters.md) se ha actualizado de JSON a YAML.
- Nuevo proyecto de ejemplo: [_Clasificación de imágenes con CNTK_](./scenario-image-classification-using-cntk.md).


## <a name="2017-10-sprint-0"></a>2017-10 (Sprint 0) 
**Número de versión**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([busque su versión](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Esta es la primera actualización de Azure Machine Learning Workbench después de la versión preliminar pública inicial en la conferencia de Microsoft Ignite 2017. Las actualizaciones principales en esta versión son correcciones para mejorar la confiabilidad y la estabilidad.  Algunos de los problemas críticos que se han resuelto son:

### <a name="new-features"></a>Nuevas características
- macOS High Sierra ahora es compatible

### <a name="bug-fixes"></a>Corrección de errores
#### <a name="workbench-experience"></a>Experiencia de área de trabajo
- Arrastrar y colocar un archivo en el área de trabajo hace que esta se bloquee.
- La ventana de terminal en VS Code configurado como un IDE para el área de trabajo no reconoce los comandos _az ml_.

#### <a name="workbench-authentication"></a>Autenticación del área de trabajo
Hemos realizado varias actualizaciones para mejorar diversos problemas de inicio de sesión y autenticación notificados.
- La ventana de autenticación sigue apareciendo, especialmente cuando la conexión a Internet no es estable.
- Problemas de mejora de la confiabilidad sobre la expiración del token de autenticación.
- En algunos casos, la ventana de autenticación aparece dos veces.
- La ventana principal de Workbench sigue mostrando el mensaje "autenticando" cuando el proceso de autenticación ha finalizado y el cuadro de diálogo emergente ya se ha descartado.
- Si no hay ninguna conexión a Internet, el cuadro de diálogo de autenticación aparece con una pantalla en blanco.

#### <a name="data-preparation"></a>Preparación de los datos 
- Cuando se filtra un valor específico, los errores y los valores que faltan también se filtran.
- Al cambiar una estrategia de muestreo, se eliminan las operaciones de combinación existentes posteriores.
- Al reemplazarse la transformación Valor que falta, no se tiene en consideración la opción NaN.
- La inferencia de tipos de fecha produce una excepción cuando encuentra un valor null.

#### <a name="job-execution"></a>Ejecución de trabajos
- No hay ningún mensaje de error claro cuando se produce un error en la ejecución del trabajo para cargar la carpeta del proyecto debido a que superó el límite de tamaño.
- Si el script de Python del usuario cambia el directorio de trabajo, no se realiza el seguimiento de los archivos escritos en las carpetas de resultados. 
- Si la suscripción activa de Azure es diferente de aquella a la que pertenece el proyecto actual, el envío de trabajos da como resultado un error 403.
- Cuando Docker no está presente, no se devuelve ningún mensaje de error claro si el usuario intenta utilizar Docker como destino de ejecución.
- El archivo .runconfig no se guarda automáticamente cuando el usuario hace clic en el botón _Ejecutar_.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- El servidor de Notebook no se puede iniciar si el usuario utiliza ciertos tipos de inicio de sesión.
- Los mensajes de error del servidor de Notebook no se exponen en registros visibles para el usuario.

#### <a name="azure-portal"></a>Azure Portal
- Si se selecciona el tema oscuro de Azure Portal, la hoja Administración de modelos aparece como un cuadro negro.

#### <a name="operationalization"></a>Operacionalización
- Reutilizar un manifiesto para actualizar un servicio web ocasiona una nueva imagen de Docker compilada con un nombre aleatorio.
- No se pueden recuperar los registros de servicio Web del clúster de Kubernetes.
- Se imprime un mensaje de error que induce a error cuando el usuario intenta crear una cuenta de Administración de modelos o una cuenta de proceso de Machine Learning y se producen problemas con los permisos.

## <a name="next-steps"></a>Pasos siguientes

Consulte la introducción de [Azure Machine Learning](../service/overview-what-is-azure-ml.md).