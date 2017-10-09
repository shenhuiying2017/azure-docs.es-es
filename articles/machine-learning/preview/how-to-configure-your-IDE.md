---
title: "¿Cómo configurar Azure Machine Learning Workbench para que funcione con un IDE?  | Microsoft Docs"
description: "Guía para configurar Azure Machine Learning Workbench para que funcione con el IDE."
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5fa9926e2230b285a2598e5d43048d6591bd1b03
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Cómo configurar Azure Machine Learning Workbench para que funcione con un IDE 

Azure Machine Learning Workbench puede configurarse para que funcione con los IDE (entorno de desarrollo integrado) de Python más populares. Permite una experiencia fluida de desarrollo de la ciencia de datos que se mueve entre la preparación de datos, la creación de código, el seguimiento de ejecuciones y la operacionalización. Actualmente, los IDE compatibles son:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Configurar workbench
1. Haga clic en el menú **Archivo** en la esquina superior izquierda de la aplicación. 
2. Seleccione la opción **Configure Project IDE** (Configurar IDE del proyecto) desde el menú flotante. 
3. Escriba `VS Code` o `PyCharm` en el campo **Nombre** (el nombre es arbitrario).
4. Escriba la ubicación del ejecutable del IDE (junto con el nombre y la extensión del archivo ejecutable) en **Ruta de acceso de ejecución**.

### <a name="default-install-path-for-visual-studio-code"></a>Ruta de instalación predeterminada de Visual Studio Code  

* Windows 32 bits - `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64 bits - `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS: seleccione la ruta de acceso de .app, como `/Applications/Visual Studio Code.app`, y la aplicación anexará el resto de la ruta de acceso por usted. Por defecto, la ruta de acceso completa al archivo ejecutable es `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Si ejecutó el comando `Shell Command: Install 'code' command in PATH` en VS Code, también puede hacer referencia a la secuencia de comandos de VS Code en `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Ruta de instalación predeterminada de PyCharm 

* Windows 32 bits - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64 bits - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS: seleccione la ruta de acceso de .app, como “/Applications/PyCharm CE.app”, y la aplicación anexará el resto de la ruta de acceso por usted. Por defecto, la ruta de acceso completa al archivo ejecutable es `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. También puede encontrar PyCharm en la carpeta Bin, `/usr/local/bin/charm`.

## <a name="open-project-in-ide"></a>Abrir el proyecto en el IDE 
Una vez completada la configuración, puede abrir un proyecto de Azure Machine Learning Project si abre el menú **Archivo** y selecciona **Abrir proyecto (<IDE_Name>)**


## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Configurar el terminal integrado en Visual Studio Code

### <a name="windows"></a>Windows 
Se ha reemplazado el shell predeterminado para que sea cmd en lugar de PowerShell. Al hacer clic en **Abrir proyecto (<IDE_Name>)**, verá un aviso: 

_¿Permite que el shell `C:\windows\System32\cmd.exe` (definido como una configuración del área de trabajo) se inicie en el terminal?_

Responda `yes` para permitir que la configuración del shell funcione sin problemas con la interfaz de la línea de comandos de Azure ML Workbench.

### <a name="mac"></a>Mac
Para ejecutar un comando `az` con el terminal integrado de Visual Studio Code en Mac, deberá establecer manualmente `PATH` para que sea el mismo valor que `PATH` en el archivo `.vscode/settings.json` del proyecto.

