---
title: "Unión de una máquina virtual con Windows Server a Azure Active Directory Domain Services | Microsoft Docs"
description: "Unión de una máquina virtual con Windows Server a un dominio administrado mediante plantillas de Azure Resource Manager."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: b90198696b7bdb9277fd2f2b8e8e727af42c5cfa
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Unión de una máquina virtual con Windows Server a un dominio administrado mediante una plantilla de Resource Manager
En este artículo se muestra cómo unir una máquina virtual con Windows Server a un dominio administrado con Azure AD Domain Services mediante plantillas de Resource Manager.

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:
1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](active-directory-ds-getting-started.md).
4. Asegúrese de que ha configurado las direcciones IP del dominio administrado como servidores DNS de la red virtual. Para más información, consulte [cómo actualizar la configuración de DNS para la red virtual de Azure](active-directory-ds-getting-started-dns.md)
5. Complete los pasos necesarios para [sincronizar contraseñas para el dominio administrado de Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Instalación y configuración de las herramientas necesarias
Puede usar cualquiera de las siguientes opciones para realizar los pasos descritos en este documento:
* **Azure PowerShell**: [instalación y configuración](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Interfaz de línea de comandos multiplataforma de Azure**: [instalación y configuración](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opción 1: aprovisionamiento de una nueva máquina virtual con Windows Server y su unión a un dominio administrado
**Nombre de la plantilla de inicio rápido**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Para implementar una máquina virtual con Windows Server y su unión a un dominio administrado, realice los siguientes pasos:
1. Vaya a la [plantilla de inicio rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Haga clic en **Implementar en Azure**.
3. En la página **Implementación personalizada**, proporcione la información necesaria para aprovisionar la máquina virtual.
4. Seleccione la **suscripción de Azure** en la que se aprovisionará la máquina virtual. Elija la misma suscripción de Azure en la que ha habilitado Azure AD Domain Services.
5. Elija un **grupo de recursos** existente o cree uno.
6. Elija una **ubicación** en la que se implementará la nueva máquina virtual.
7. En el campo de **nombre de red virtual existente**, especifique la red virtual en la que ha implementado el dominio administrado de Azure AD Domain Services.
8. En el campo de **nombre de subred existente**, especifique la subred de la red virtual donde desea implementar esta máquina virtual. No seleccione la subred de puerta de enlace de la red virtual. Además, no seleccione la subred dedicada en la que se implementa el dominio administrado.
9. En el campo de **prefijo de etiqueta DNS**, especifique el nombre de host de la máquina virtual en proceso de aprovisionamiento. Por ejemplo, "contoso-win".
10. Seleccione el **Tamaño de VM** de la máquina virtual.
11. En el campo de **dominio al que desea unirse**, especifique el nombre de dominio del dominio administrado.
12. En el campo de **nombre de usuario de dominio**, especifique el nombre de cuenta de usuario del dominio administrado que debe usarse para unir la máquina virtual al dominio administrado.
13. En el campo de **contraseña de dominio**, especifique la contraseña de la cuenta de usuario de dominio a la que hace referencia el parámetro "domainUsername".
14. Opcional: puede especificar una **ruta de acceso de la unidad organizativa** en una unidad organizativa personalizada, en la que se va a agregar la máquina virtual. Si no especifica un valor para este parámetro, la máquina virtual se agrega a la unidad organizativa **Equipos de DC de AAD** del dominio administrado.
15. En el campo de **nombre de usuario de administrador de máquina virtual**, especifique un nombre de cuenta de administrador local para la máquina virtual.
16. En el campo de **contraseña de administrador de máquina virtual**, especifique una contraseña de administrador local para la máquina virtual. Proporcione una contraseña de administrador local segura para la máquina virtual a fin de ofrecer protección frente a ataques de fuerza bruta a contraseñas.
17. Haga clic en **Acepto los términos y condiciones indicados anteriormente**.
18. Haga clic en **Comprar** para aprovisionar la máquina virtual.

> [!WARNING]
> **Controle las contraseñas con precaución.**
> El archivo de parámetros de plantilla contiene contraseñas de cuentas de dominio, así como contraseñas de administrador local de la máquina virtual. Asegúrese de no dejar este archivo por ahí en recursos compartidos de archivos o en otras ubicaciones compartidas. Recomendamos que se deshaga de este archivo una vez que haya terminado de implementar las máquinas virtuales.
>

Tras completarse correctamente la implementación, su máquina virtual Windows recién aprovisionada se une al dominio administrado.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opción 2: unión de una máquina virtual con Windows Server existente a un dominio administrado
**Plantilla de inicio rápido**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Para unir una máquina virtual con Windows Server existente a un dominio administrado, realice los siguientes pasos:
1. Vaya a la [plantilla de inicio rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Haga clic en **Implementar en Azure**.
3. En la página **Implementación personalizada**, proporcione la información necesaria para aprovisionar la máquina virtual.
4. Seleccione la **suscripción de Azure** en la que se aprovisionará la máquina virtual. Elija la misma suscripción de Azure en la que ha habilitado Azure AD Domain Services.
5. Elija un **grupo de recursos** existente o cree uno.
6. Elija una **ubicación** en la que se implementará la nueva máquina virtual.
7. En el campo de **lista de máquinas virtuales**, especifique los nombres de las máquinas virtuales existentes que se van a unir al dominio administrado. Use una coma para separar nombres de máquina virtual individuales. Por ejemplo, **contoso-web, contoso-api**.
8. En el campo de **nombre de usuario de unión al dominio**, especifique el nombre de cuenta de usuario del dominio administrado que debe usarse para unir la máquina virtual al dominio administrado.
9. En el campo de **contraseña de usuario de unión al dominio**, especifique la contraseña de la cuenta de usuario de dominio a la que hace referencia el parámetro "domainUsername".
10. En el campo de **dominio FQDN**, especifique el nombre de dominio DNS del dominio administrado.
11. Opcional: puede especificar una **ruta de acceso de la unidad organizativa** en una unidad organizativa personalizada, en la que se va a agregar la máquina virtual. Si no especifica un valor para este parámetro, la máquina virtual se agrega a la unidad organizativa **Equipos de DC de AAD** del dominio administrado.
12. Haga clic en **Acepto los términos y condiciones indicados anteriormente**.
13. Haga clic en **Comprar** para aprovisionar la máquina virtual.

> [!WARNING]
> **Controle las contraseñas con precaución.**
> El archivo de parámetros de plantilla contiene contraseñas de cuentas de dominio, así como contraseñas de administrador local de la máquina virtual. Asegúrese de no dejar este archivo por ahí en recursos compartidos de archivos o en otras ubicaciones compartidas. Recomendamos que se deshaga de este archivo una vez que haya terminado de implementar las máquinas virtuales.
>

Tras completarse correctamente la implementación, las máquinas virtuales Windows especificadas se unen al dominio administrado.


## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Plantilla de inicio rápido de Azure: Domain join a new VM (Unión a un dominio de una nueva máquina virtual)](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Plantilla de inicio rápido de Azure: Domain join existing VMs (Unión a un dominio de máquinas virtuales existentes)](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
