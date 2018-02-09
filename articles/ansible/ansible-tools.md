---
title: Herramientas para usar Ansible con Azure
description: "Instalación y uso de herramientas individuales para Ansible con Azure"
ms.service: ansible
keywords: "ansible, azure, devops, herramientas, vs code, visual studio code, extensión"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>Herramientas para usar Ansible con Azure

Las siguientes herramientas hacen que el trabajo con Ansible y Azure sea más fácil y eficaz.

## <a name="visual-studio-code-extension-for-ansible"></a>Extensión de Visual Studio Code para Ansible

La [extensión de Visual Studio Code para Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) proporciona varias características para el uso de Ansible desde Visual Studio Code:

- Finalización automática de directivas, módulos y complementos de Ansible de la documentación de Ansible a medida que escribe.
- Visualización de fragmentos de código al presionar &lt;Ctrl>&lt;Espacio> mientras se crean guiones de procedimientos de Ansible.
- Resaltado de sintaxis que muestra el código de los guiones de procedimientos de Ansible en distintos colores y fuentes de acuerdo con la sintaxis YAML.
- Ejecución de guiones de procedimientos de Ansible desde la ventana de Terminal de Visual Studio Code.
    - (Solo Windows) Ansible se puede ejecutar desde un contenedor de Docker.
    - (Linux y macOS) Ansible se puede ejecutar desde un contenedor de Docker o desde una instalación local de Ansible. 
- Ejecución de guiones de procedimientos de Ansible desde Azure Cloud Shell.