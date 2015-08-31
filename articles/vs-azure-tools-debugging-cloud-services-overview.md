<properties 
   pageTitle="Depuración de servicios en la nube"
   description="Depuración de servicios en la nube"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.date="08/12/2015"
   ms.author="patshea" />

# Depuración de servicios en la nube

Puede utilizar diferentes enfoques para depurar una aplicación de Azure utilizando las Herramientas de Azure para Microsoft Visual Studio y el SDK de Azure:

- Puede depurar una aplicación de Azure desde Visual Studio al desarrollarla, como lo haría con cualquier aplicación de Visual C# o Visual Basic. Para obtener más información, vea [Depurar un servicio en la nube o una máquina virtual en Visual Studio](http://go.microsoft.com/fwlink/p/?LinkID=623018).

- Puede usar Diagnósticos de Azure para registrar información detallada del código que se ejecuta en los roles, bien sea en el entorno de desarrollo o en Azure. Para obtener más información, vea [Recopilar datos de registro mediante Diagnósticos de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Si usa Visual Studio Enterprise para escribir roles destinados a .NET Framework 4 o .NET Framework 4.5, puede habilitar IntelliTrace en el momento de implementar un servicio en la nube de Azure desde Visual Studio. IntelliTrace proporciona un registro que se puede usar con Visual Studio para depurar la aplicación como si se estuviera ejecutando en Azure. Para obtener más información, vea [Depurar con IntelliTrace y Visual Studio un servicio en la nube](http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Puede habilitar la depuración remota en los servicios en la nube cuando implementa el servicio en la nube desde Visual Studio. Si elige habilitar la depuración remota para una implementación, los servicios de depuración remota se instalan en las máquinas virtuales que ejecutan cada instancia de rol. Estos servicios, como msvsmon.exe, no afectan al rendimiento ni al resultado en cuando a costos adicionales. Para obtener más información, vea [Depurar un servicio en la nube o una máquina virtual en Visual Studio](http://go.microsoft.com/fwlink/p/?LinkID=623018).

<!---HONumber=August15_HO8-->