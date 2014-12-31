<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="Inyección de datos personalizados en máquinas virtuales de Azure" description="Este tema describe cómo inyectar datos personalizados en una máquina virtual de Azure cuando la instancia se crea y cómo localizar dichos datos en Windows o Linux." metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="timlt" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/1/2014" ms.author="rasquill" />



#Inyección de datos personalizados en una máquina virtual de Azure 

La inyección de un script o de otros datos en una máquina virtual de Azure cuando se está aprovisionando es un escenario muy común, independientemente de si el sistema operativo es Microsoft Windows o una distribución de Linux. En este tema se describe cómo:

- Inyectar datos en una máquina virtual de Azure cuando se está aprovisionando.

- Recuperarla para Windows y Linux, y 

- Usar herramientas especiales disponibles en algunos sistemas para detectar y administrar datos personalizados automáticamente.

> [WACOM.NOTE] Este tema se amplía en esta [entrada de blog sobre Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) para hablar de esta característica, y se actualizará a medida que aparezcan más funcionalidades.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
En este tema:

+ [Inyección de datos personalizados en una máquina virtual de Azure](#injectingCustomData)

+ [Uso de datos personalizados en la máquina virtual](#usingCustomData)

+ [Pasos siguientes](#nextsteps)


## <a id="injectingCustomData"></a>Inyección de datos personalizados en una máquina virtual de Azure

Esta característica solamente se admite actualmente en la [interfaz de la línea de comandos entre plataformas de Microsoft Azure](https://github.com/Azure/azure-sdk-tools-xplat). Aunque puede usar cualquiera de las opciones para el comando "azure vm create", el siguiente enfoque muestra una perspectiva muy básica. 

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## <a id="usingCustomData"></a>Uso de datos personalizados en la máquina virtual
 
+ Si la máquina virtual de Azure es una máquina virtual de Windows, entonces el archivo de datos personalizados se guarda en "%SYSTEMDRIVE%\AzureData\CustomData.bin" y aunque tuviera una codificación base64 para transferirse desde el equipo local a la nueva máquina virtual, se descodifica automáticamente y se puede abrir o usar inmediatamente. 

   > [WACOM.NOTE] Si el archivo existe se sobrescribe. La seguridad en el directorio se establece en **Sistema: Control total** y **Administradores: Control total**.

+ Si la máquina virtual de Azure es una máquina virtual Linux, entonces el archivo de datos personalizados se ubicará en los dos lugares siguientes pero los datos tendrán la codificación base64, por lo que necesita descodificarlos primero.

    + En "/var/lib/waagent/ovf-env.xml"
    + En "/var/lib/waagent/CustomData" 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a id="nextsteps"></a>Pasos siguientes: Uso de cloud-init

Si la máquina virtual de Azure es una imagen de Ubuntu, entonces puede usar cloud-init para iniciar un script para usar los datos personalizados automáticamente (o bien, si el archivo de datos personalizados es un script, ejecútelo). Para obtener más información, consulte la [documentación de cloud-init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Referencia de la API de REST de administración del servicio Agregar rol](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfaz de la línea de comandos entre plataformas de Microsoft Azure](https://github.com/Azure/azure-sdk-tools-xplat)


<!--HONumber=35_1-->
