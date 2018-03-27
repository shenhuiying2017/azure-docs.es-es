---
title: "Notas de la versión de Azure Machine Learning Workbench sprint 0 octubre de 2017"
description: "Este documento detalla las actualizaciones para la versión de sprint 0 de Azure Machine Learning"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>Sprint 0: octubre de 2017 

**Número de la versión: 0.1.1710.04013**

Esta es la primera actualización de Azure Machine Learning Workbench después de la versión preliminar pública inicial en la conferencia de Microsoft Ignite 2017. Las actualizaciones principales en esta versión son correcciones para mejorar la confiabilidad y la estabilidad.  Algunos de los problemas críticos que se han resuelto son:

## <a name="new-features"></a>Nuevas características
- macOS High Sierra ahora es compatible

## <a name="bug-fixes"></a>Corrección de errores
### <a name="workbench-experience"></a>Experiencia de área de trabajo
- Arrastrar y colocar un archivo en el área de trabajo hace que esta se bloquee.
- La ventana de terminal en VS Code configurado como un IDE para el área de trabajo no reconoce los comandos _az ml_.

### <a name="workbench-authentication"></a>Autenticación del área de trabajo
Hemos realizado varias actualizaciones para mejorar diversos problemas de inicio de sesión y autenticación notificados.
- La ventana de autenticación sigue apareciendo, especialmente cuando la conexión a Internet no es estable.
- Problemas de mejora de la confiabilidad sobre la expiración del token de autenticación.
- En algunos casos, la ventana de autenticación aparece dos veces.
- La ventana principal del área de trabajo aún muestra un mensaje "autenticando" cuando en realidad el proceso de autenticación ha finalizado y el cuadro de diálogo emergente ya se ha descartado.
- Si no hay ninguna conexión a Internet, el cuadro de diálogo de autenticación aparece con una pantalla en blanco.

### <a name="data-preparation"></a>Preparación de los datos 
- Cuando se filtra un valor específico, los errores y los valores que faltan también se filtran.
- Al cambiar una estrategia de muestreo, se eliminan las operaciones de combinación existentes posteriores.
- Al reemplazarse la transformación Valor que falta, no se tiene en consideración la opción NaN.
- La inferencia de tipos de fecha produce una excepción cuando encuentra un valor null.

### <a name="job-execution"></a>Ejecución de trabajos
- No hay ningún mensaje de error claro cuando se produce un error en la ejecución del trabajo para cargar la carpeta del proyecto debido a que superó el límite de tamaño.
- Si el script de Python del usuario cambia el directorio de trabajo, no se realiza el seguimiento de los archivos escritos en las carpetas de resultados. 
- Si la suscripción activa de Azure es diferente de aquella a la que pertenece el proyecto actual, el envío de trabajos da como resultado un error 403.
- Cuando Docker no está presente, no se devuelve ningún mensaje de error claro si el usuario intenta utilizar Docker como destino de ejecución.
- El archivo .runconfig no se guarda automáticamente cuando el usuario hace clic en el botón _Ejecutar_.

### <a name="jupyter-notebook"></a>Jupyter Notebook
- El servidor de Notebook no se puede iniciar si el usuario utiliza ciertos tipos de inicio de sesión.
- Los mensajes de error del servidor de Notebook no se exponen en registros visibles para el usuario.

### <a name="azure-portal"></a>Azure Portal
- Si se selecciona el tema oscuro de Azure Portal, la hoja Administración de modelos aparece como un cuadro negro.

### <a name="operationalization"></a>Operacionalización
- Reutilizar un manifiesto para actualizar un servicio web ocasiona una nueva imagen de Docker compilada con un nombre aleatorio.
- No se pueden recuperar los registros de servicio Web del clúster de Kubernetes.
- Se imprime un mensaje de error que induce a error cuando el usuario intenta crear una cuenta de Administración de modelos o una cuenta de proceso de Machine Learning y se producen problemas con los permisos.
