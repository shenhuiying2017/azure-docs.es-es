---
title: Mi primer runbook de Python en Azure Automation | Microsoft Docs
description: "Tutorial que le guiará a través de la creación, prueba y publicación de un runbook de Python."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: eslesar
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 26a1681e662814fbe513bec1b8d2bc040885d632
ms.contentlocale: es-es
ms.lasthandoff: 09/13/2017

---

# <a name="my-first-python-runbook"></a>Mi primer runbook de Python

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Este tutorial le guiará por la creación de un [runbook de Python](automation-runbook-types.md#python-runbooks) en Azure Automation. Comenzaremos con un runbook simple que probaremos y publicaremos, y explicaremos cómo hacer un seguimiento del estado del trabajo del runbook. A continuación, modificaremos el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. Para finalizar, agregaremos parámetros de runbook para que el runbook sea más robusto.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Cuenta de Automatización](automation-offering-get-started.md) para contener el Runbook y autenticarse en recursos de Azure.  Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Una máquina virtual de Azure. Detendremos e iniciaremos esta máquina, por lo que no debería ser una máquina virtual de producción.

## <a name="create-a-new-runbook"></a>Crear un runbook

Empezamos creando un runbook simple cuya salida sea el texto *Hello World*.

1. En el Portal de Azure, abra su cuenta de Automatización.
   La página de la cuenta de Automatización proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de ellos son los módulos que se incluyen automáticamente en una cuenta nueva de Automatización. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).
1. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
   ![RunbooksControl](media/automation-first-runbook-textual-python/runbooks-control-tiles.png)
1. Para crear un runbook, haga clic en el botón **Agregar un runbook** y en **Crear un runbook nuevo**.
1. Asigne al runbook el nombre *MyFirstRunbook-Python*.
1. En este caso, vamos a crear un [runbook de Python](automation-runbook-types.md#python-runbooks), por tanto, seleccione **Python 2** en **Tipo de runbook**.
1. Haga clic en **Crear** para crear el runbook y abra el editor de texto.

## <a name="add-code-to-the-runbook"></a>Agregar código al runbook

Ahora, agregue un comando simple para imprimir el texto "Hello World":

```python
print("Hello World!")
```

Haga clic en **Guardar** para guardar el runbook.

## <a name="test-the-runbook"></a>Probar el runbook

Antes de que publiquemos el runbook para que esté disponible en producción, queremos probarlo para asegurarnos de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1. Haga clic en **Panel Prueba** para abrir el panel de prueba.
   ![Test Pane](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
1. Se crea un [trabajo de runbook](automation-runbook-execution.md) y se muestra su estado.
   El estado del trabajo se inicia como *En cola*, lo que indica que está esperando que haya disponible un trabajo de runbook en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.
1. Cuando se complete el trabajo del runbook, se mostrará su resultado. En nuestro caso, deberíamos ver *Hello World*.
1. Cierre el panel Prueba para volver al lienzo.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar el runbook

El runbook que acabamos de crear aún está en modo de borrador. Tenemos que publicarlo antes de que podamos ejecutarlo en producción.
Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador.
En nuestro caso, no tenemos una versión publicada aún porque acabamos de crear el runbook.

1. Haga clic en **Publicar** para publicar el runbook y en **Sí** cuando se le solicite.
   ![Botón Publicar](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Si se desplaza ahora a la izquierda para ver el runbook en el panel **Runbooks**, mostrará el **Estado de creación** **Publicado**.
1. Desplácese de nuevo a la derecha para ver el panel de **MyFirstRunbook-Python**.
   Las opciones en la parte superior nos permiten iniciar el runbook, verlo, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.
1. Deseamos iniciar el runbook, así que hacemos clic en **Iniciar** y, después, en **Aceptar** cuando se abre la hoja Iniciar Runbook.
1. Se abre un panel de trabajo para el trabajo de runbook que acabamos de crear. Podemos cerrar este panel, pero en este caso lo dejaremos abierto para que podamos ver el progreso del trabajo.
1. El estado del trabajo se muestra en **Resumen del trabajo** y coincide con los estados que vimos cuando probamos el runbook.
1. Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. Se abre el panel Salida y podemos ver *Hello World*.
1. Cierre el panel Salida.
1. Haga clic en **Todos los registros** para abrir el panel Transmisiones para el trabajo de Runbook. Solo deberíamos ver *Hello World* en el flujo de salida, pero se pueden mostrar otras transmisiones de un trabajo de Runbook como Detallado y Error si el Runbook escribe en ellas.
1. Cierre el panel Transmisiones y el panel Trabajos para volver al panel de MyFirstRunbook-Python.
1. Haga clic en **Trabajos** para abrir el panel Trabajos de este runbook. Enumera todos los trabajos creados por este runbook. Solo deberíamos ver un trabajo en la lista ya que solo ejecutamos el trabajo una vez.
1. Puede hacer clic en esta tarea para abrir el mismo panel Trabajo que vimos cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## <a name="add-authentication-to-manage-azure-resources"></a>Agregar autenticación para administrar recursos de Azure

Hemos probado y publicado nuestro runbook, pero hasta ahora no hace nada útil. Queremos que administre recursos de Azure.
Para administrar recursos de Azure, el script debe autenticarse por medio de las credenciales de su [cuenta de Automation](automation-offering-get-started.md).

> [!NOTE]
> La cuenta de Automation se debe haber creado con la característica de entidad de servicio para que haya un certificado RunAs.
> Si la cuenta de Automation no se ha creado con la entidad de servicio, se puede autenticar recurriendo al método descrito en [Autenticación con las bibliotecas de administración de Azure para Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Abra el editor de texto haciendo clic en **Editar** en el panel MyFirstRunbook-Python.
1. Agregue el siguiente código para autenticarse en Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Agregar código para crear un cliente de Compute de Python e iniciar la máquina virtual

Para trabajar con máquinas virtuales de Azure, cree una instancia del [cliente de Azure Compute para Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.compute.computemanagementclient?view=azure-python).

Use el cliente de Compute para iniciar la máquina virtual. Agregue el siguiente código al runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Donde _MyResourceGroup_ es el nombre del grupo de recursos que contiene la máquina virtual y _TestVM_, el nombre de la máquina virtual que se quiere iniciar. 

Pruebe y ejecute el runbook de nuevo para constatar que inicia la máquina virtual.

## <a name="use-input-parameters"></a>Usar parámetros de entrada

Actualmente, el runbook usa valores codificados de forma rígida en los nombres del grupo de recursos y de la máquina virtual.
Ahora vamos a agregar código para obtener estos valores de parámetros de entrada.

Usaremos la variable `sys.argv` para obtener los valores de parámetro.
Agregue el siguiente código al runbook, inmediatamente después de las demás instrucciones `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Con esta operación se importa el módulo `sys` y se crean dos variables para contener los nombres del grupo de recursos y de la máquina virtual.
Observe que el elemento de la lista de argumentos, `sys.argv[0]`, es el nombre del script y no lo especifica el usuario.

Ahora, podemos modificar las dos últimas líneas del runbook para usar los valores de parámetro de entrada en lugar de los valores codificados de forma rígida:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Cuando se inicia un runbook de Python (ya sea en la hoja **Prueba** o como un runbook publicado), se pueden especificar los valores de los parámetros en la hoja **Iniciar runbook**, dentro de **Parámetros**.

![Cuadro de valor de parámetro](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Cuando empiece a escribir un valor en el primer cuadro, aparecerá un segundo cuadro (y así sucesivamente) para que pueda especificar varios valores de parámetro si así lo precisa.

Los valores estarán disponibles para el script como la matriz `sys.argv` como en el código que se acaba de agregar.

Escriba el nombre del grupo de recursos como valor del primer parámetro y el nombre de la máquina virtual que se va a iniciar como valor del segundo parámetro.

![Escribir valores de parámetro](media/automation-first-runbook-textual-python/runbook-python-params.png)

Haga clic en **Aceptar** para iniciar el runbook. El runbook se ejecuta e inicia la máquina virtual que se ha especificado.

## <a name="next-steps"></a>Pasos siguientes

* Para empezar a trabajar con Runbooks de PowerShell, consulte [Mi primer Runbook de PowerShell](automation-first-runbook-textual-powershell.md)
* Para empezar a trabajar con cuadernos gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
* Para obtener más información sobre los tipos de Runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Automatización de Azure](automation-runbook-types.md)
* Para más información sobre cómo desarrollar para Azure con Python, vea [Azure para desarrolladores de Python](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).

