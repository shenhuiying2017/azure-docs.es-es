---
title: "Notas de la versión de Azure Machine Learning Workbench sprint 1 de noviembre de 2017"
description: "En este documento se detallan las actualizaciones de la versión de sprint 1 de Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="sprint-1---november-2017"></a>Sprint 1: noviembre de 2017 

**Número de la versión: 0.1.1710.31013**

Esta es la segunda actualización de Azure Machine Learning Workbench. Seguimos realizando mejoras de seguridad, estabilidad y mantenimiento en la aplicación Workbench, en la CLI y en el nivel de servicios back-end. Le agradecemos enormemente sus comentarios tanto positivos como negativos. Muchas de las actualizaciones descritas aquí son consecuencia directa de esos comentarios. ¡No deje de enviarlos!

## <a name="notable-new-features"></a>Nuevas características destacables
- Ahora, Azure Machine Learning está disponible en dos nuevas regiones de Azure: **Europa Occidental** y el **Sudeste Asiático**. Ambas se unen a las regiones ya incluidas de **Este de EE. UU. 2**, **Oeste del centro de EE. UU.** y **Este de Australia**, lo que eleva a cinco el número total de regiones de implementación.
- Hemos habilitado el resaltado de sintaxis de código de Python en la aplicación Workbench para que el código fuente de Python sea más fácil de leer y editar. 
- Ahora, puede iniciar su IDE favorito directamente desde un archivo, en lugar de desde el proyecto global.  Si abre un archivo en Workbench y, después, hace clic en "Editar", se abre el IDE (actualmente se admiten VS Code y PyCharm) con el archivo y proyecto actuales.  También puede hacer clic en la flecha situada junto al botón Editar para modificar el archivo en el editor de texto de Workbench.  Los archivos serán de solo lectura hasta que haga clic en Editar, lo que evita que se realicen cambios por error.
- Ahora, la conocida biblioteca de trazado `matplotlib` versión 2.1.0 se incluye con la aplicación Workbench.
- Hemos actualizado la versión de .NET Core a 2.0 para el motor de preparación de datos. Por lo tanto, ya no es necesario instalar OpenSSL mediante Homebrew al instalar una aplicación en Mac OS. Esto allana el terreno igualmente para más características de preparación de datos muy interesantes que llegarán en un futuro próximo. 
- Hemos habilitado una página principal de la aplicación específica de la versión; así, tendrá acceso a más notas de la versión relevantes y a avisos de actualización según cuál sea su versión actual de la aplicación.
- Ahora, si el nombre de usuario local contiene un espacio, la aplicación se puede instalar sin ningún tipo de problema. 

## <a name="detailed-updates"></a>Actualizaciones detalladas
Esta es una lista de actualizaciones detalladas de cada área de componente de Azure Machine Learning en este sprint.

### <a name="installer"></a>Instalador
- Ahora, el instalador de la aplicación limpia el directorio de instalación que se creó con una versión anterior.
- Se ha corregido un error que hacía que el instalador se quedara bloqueado al llegar a 100 % en macOS High Sierra.
- Ahora, hay un vínculo directo al directorio del instalador para que el usuario pueda revisar los registros del instalador en caso de que haya un error en la instalación.
- Ahora, la instalación no da problemas a aquellos usuarios cuyo nombre de usuario contiene un espacio.

### <a name="workbench-authentication"></a>Autenticación de Workbench
- Se ha incluido compatibilidad con la autenticación en el administrador de proxy.
- Ahora, los usuarios que están detrás de un firewall pueden iniciar sesión sin problemas. 
- La aplicación ya no se bloquea si un usuario tiene cuentas de experimentación en varias regiones de Azure y una de esas regiones deja de estar disponible.
- Cuando la autenticación no se ha completado y el cuadro de diálogo de autenticación sigue estando visible, la aplicación ya no intenta cargar el área de trabajo desde la memoria caché local.

### <a name="workbench-app"></a>Aplicación Workbench
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

### <a name="data-preparation"></a>Preparación de los datos 
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

### <a name="job-execution"></a>Ejecución de trabajos
- Ahora, se incluye un argumento en el registro de historial de ejecuciones.
- Ahora, los trabajos iniciados en la CLI se muestran automáticamente en el panel de trabajo de historial de ejecuciones.
- Ahora, el panel de trabajo muestra los trabajos creados por usuarios invitados agregados al inquilino de AAD.
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

### <a name="jupyter-notebook"></a>Jupyter Notebook
- Ahora, las búsquedas de nombres de bloc de notas funcionan correctamente en la vista de blocs de notas.
- Ahora, los blocs de notas se pueden eliminar en la vista de blocs de notas.
- Se ha agregado una nueva fase `%upload_artifact` para cargar los archivos creados en el entorno de ejecución de Notebook en el almacén de datos del historial de ejecuciones.
- Ahora, los errores de kernel aparecen en el estado de trabajo de Notebook para una depuración más sencilla.
- Ahora, el servidor de Jupyter se cierra correctamente cuando el usuario cierra sesión en la aplicación.

### <a name="azure-portal"></a>Azure Portal
- Ahora, se pueden crear cuentas de Experimentación y cuentas de Administración de modelos en dos nuevas regiones de Azure: Europa Occidental y Sudeste Asiático.
- Ahora, el plan de DevTest de la cuenta de Administración de modelos solo está disponible cuando es el primero que se crea en la suscripción. 
- El vínculo de ayuda en Azure Portal se ha actualizado para apuntar a la página de documentación correcta.
- El campo de descripción se ha quitado de la página de detalles de la imagen de Docker, ya que no procede.
- Se han agregado detalles, como la configuración de escala automática y AppInsights, a la página de detalles del servicio web.
- Ahora, la página de Administración de modelos se presenta aun cuando las cookies de terceros estén deshabilitadas en el explorador. 

### <a name="operationalization"></a>Operacionalización
- Los servicios web que tengan "score" en el nombre ya no generan errores.
- Ahora, los usuarios pueden crear un entorno de implementación solo con acceso de colaborador a un grupo de recursos de Azure o a la suscripción. Ya no es necesario tener acceso de propietario en toda la suscripción.
- Ahora, la CLI de operacionalización incluye la finalización automática de pestañas en Linux.
- Ahora, el servicio de creación de imágenes permite crear imágenes para servicios y dispositivos de IoT de Azure.

### <a name="sample-projects"></a>Proyectos de ejemplo
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
