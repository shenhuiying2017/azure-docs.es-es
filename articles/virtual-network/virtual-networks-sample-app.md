---
title: "Aplicación de ejemplo de Azure para su uso con DMZ | Microsoft Docs"
description: "Implemente esta sencilla aplicación web después de crear una red perimetral para probar los escenarios de flujo de tráfico."
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 60340ab7-b82b-40e0-bd87-83e41fe4519c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 8506238e41c5d9dac8d76d729d4919b30a0528b9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="sample-application-for-use-with-dmzs"></a>Aplicación de ejemplo para su uso con DMZ
[Volver a la página de procedimientos recomendados de límites de seguridad][HOME]

Estos scripts de PowerShell se pueden ejecutar localmente en los servidores IIS01 y AppVM01 para instalar y configurar una aplicación web sencilla que muestra una página html del servidor front-end IIS01 con el contenido del servidor back-end AppVM01.

Esta aplicación proporciona un entorno de prueba sencillo para muchos de los ejemplos de la red perimetral y muestra el modo en que los cambios en las reglas de puntos de conexión, grupos de seguridad de red, UDR y firewall pueden afectar a los flujos de tráfico.

## <a name="firewall-rule-to-allow-icmp"></a>Regla de firewall para habilitar ICMP
Esta instrucción simple de PowerShell se puede ejecutar en cualquier máquina virtual de Windows para permitir el tráfico ICMP (Ping). Esta actualización de firewall hace posible un proceso de prueba y solución de problemas más sencillo al permitir al protocolo ping que pase a través del Firewall de windows (en la mayoría de las distribuciones de Linux, la característica ICMP está activada de forma predeterminada).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Si usa los siguientes scripts, esta adición de regla de firewall es la primera instrucción.

## <a name="iis01---web-application-installation-script"></a>IIS01: script de instalación de aplicación web
Este script le permitirá hacer lo siguiente:

1. Abrir IMCPv4 (Ping) en el Firewall de Windows del servidor local para facilitar el proceso de pruebas.
2. Instalar IIS y .Net Framework v4.5
3. Crear una página web ASP.NET y un archivo Web.config
4. Cambiar el grupo de aplicaciones predeterminado para facilitar el acceso a archivos
5. Establecer el usuario anónimo para la cuenta de administrador y la contraseña

Este script de PowerShell debe ejecutarse localmente mientras el RDP tiene lugar en IIS01.

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Clasic Pipeline to remote file access will work easier
    Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01: script instalación de servidor de archivos
Este script configura el back-end para esta aplicación simple. Este script le permitirá hacer lo siguiente:

1. Abrir IMCPv4 (Ping) en el firewall para facilitar el proceso de pruebas.
2. Crear un directorio para el sitio web
3. Crear un archivo de texto al que se acceda de forma remota por la página web
4. Establecer permisos en los directorios y el archivo en Anónimo para permitir el acceso
5. Desactivar la seguridad mejorada de Internet Explorer para permitir una exploración más fácil desde este servidor 

> [!IMPORTANT]
> **Procedimiento recomendado**: no desactive nunca la seguridad mejorada de Internet Explorer en un servidor de producción; además, no es aconsejable explorar la Web desde un servidor de producción. Asimismo, la apertura de recursos compartidos de archivos para el acceso anónimo no es la mejor opción, pero aquí se realiza para simplificar el proceso.
> 
> 

Este script de PowerShell debe ejecutarse localmente mientras RDP tiene lugar en AppVM01. Es necesario que PowerShell se ejecute como administrador para garantizar la correcta ejecución.

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01: script de instalación del servidor DNS
No hay ningún script incluido en esta aplicación de ejemplo para configurar el servidor DNS. Si las pruebas de las reglas de firewall, el grupo de seguridad de red o UDR deben incluir el tráfico DNS, el servidor DNS01 debe configurarse manualmente. El archivo xml de configuración de red y la plantilla de Resource Manager para ambos ejemplos incluye DNS01 como el servidor DNS principal y el servidor DNS público hospedado por el nivel 3 como el servidor DNS de copia de seguridad. El servidor DNS de nivel 3 podría ser el servidor DNS real utilizado para el tráfico no local, y si DNS01 no está configurado, no se produciría ningún DNS de red local.

## <a name="next-steps"></a>Pasos siguientes
* Ejecutar el script IIS01 en un servidor IIS
* Ejecutar el script de servidor de archivos en AppVM01
* Ir a la dirección IP pública en IIS01 para validar la compilación

<!--Link References-->
[HOME]: ../best-practices-network-security.md
