---
title: "Preguntas más frecuentes sobre roles de Azure Cloud Services | Microsoft Docs"
description: "Preguntas más frecuentes sobre Azure Cloud Services. Responde a algunas preguntas comunes sobre certificados, roles web y roles de trabajo."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 7287cb1709b7c863cd046edfb995e23455398ec2
ms.lasthandoff: 03/25/2017


---
# <a name="cloud-services-faq"></a>P+F de Servicios en la nube
En este artículo se responden algunas preguntas frecuentes sobre Servicios en la nube de Microsoft Azure. También puede visitar [Preguntas más frecuentes de soporte técnico de Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para información general sobre los precios y el soporte técnico de Azure. También puede consultar la [página Tamaños de los servicios en la nube](cloud-services-sizes-specs.md) para obtener información de tamaño.

## <a name="certificates"></a>Certificados
### <a name="where-should-i-install-my-certificate"></a>¿Dónde debo instalar mi certificado?
* **My**  
  Certificado de aplicación con clave privada (\*.pfx, \*.p12).
* **CA**  
  Todos los certificados intermedios van a este almacén (entidades de certificación de directivas y secundarias).
* **ROOT**  
  El almacén de entidades de certificación raíz, por lo que la mayoría de certificados de entidades de certificación raíz van aquí.

### <a name="i-cant-remove-expired-certificate"></a>No se puede quitar el certificado expirado
Azure impide quitar un certificado mientras está en uso. Debe eliminar la implementación que utiliza el certificado o actualizar la implementación con un certificado diferente o renovado.

### <a name="delete-an-expired-certificate"></a>Eliminar un certificado expirado
Siempre que el certificado no esté en uso, podrá usar el cmdlet de PowerShell [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) para quitar un certificado.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Tengo certificados expirados denominados Administración de servicios de Microsoft Azure para Extensiones
Estos certificados se crean siempre que se agrega una extensión al servicio en la nube, como la extensión de Escritorio remoto. Estos certificados solo se utilizan para cifrar y descifrar la configuración privada de la extensión. No importa si estos certificados expiran. La fecha de expiración no se comprueba.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Siguen apareciendo certificados que he eliminado
Estos siguen reapareciendo muy probablemente a causa de una herramienta que esté utilizando, como Visual Studio. Cada vez que vuelva a conectar con una herramienta que esté utilizando un certificado, este se volverá a cargar en Azure.

### <a name="my-certificates-keep-disappearing"></a>Mis certificados siguen desapareciendo
Cuando se recicla la instancia de máquina virtual, se pierden todos los cambios locales. Use una [tarea de inicio](cloud-services-startup-tasks.md) para instalar certificados en la máquina virtual cada vez que se inicie el rol.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>No encuentro mis certificados de administración en el portal
Los [certificados de administración](../azure-api-management-certs.md) solo están disponibles en el Portal de Azure clásico. La versión actual de Azure Portal no utiliza certificados de administración. 

### <a name="how-can-i-disable-a-management-certificate"></a>¿Cómo puedo deshabilitar un certificado de administración?
[certificados de administración](../azure-api-management-certs.md) . Se eliminan en el Portal de Azure clásico cuando ya no quiera utilizarlos más.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>¿Cómo puedo crear un certificado SSL para una dirección IP específica?
Siga las instrucciones del [tutorial de creación de un tutorial](cloud-services-certs-create.md). Utilice la dirección IP como el nombre DNS.

## <a name="security"></a>Seguridad
### <a name="disable-ssl-30"></a>Deshabilitar SSL 3.0
Para deshabilitar SSL 3.0 y usar la seguridad TLS, cree una tarea de inicio que se documente en esta entrada de blog: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

### <a name="add-nosniff-to-your-website"></a>Agregue **nosniff** a su sitio web
Para evitar que los clientes curioseen en los tipos MIME, agregue un ajuste a su archivo *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

También puede agregarlo como un ajuste en IIS. Use el comando siguiente con el artículo [common startup tasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) (tareas comunes de inicio).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="customize-iis-for-a-web-role"></a>Personalización de IIS para un rol web
Use el script de inicio de IIS del artículo [common startup tasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) (tareas comunes de inicio).

## <a name="scaling"></a>Escalado
### <a name="i-cannot-scale-beyond-x-instances"></a>No puedo escalar más allá de X instancias
La suscripción de Azure tiene un límite en el número de núcleos que se pueden usar. El escalado no funcionará si ha usado todos los núcleos disponibles. Por ejemplo, si tiene un límite de 100 núcleos, esto significa que podría tener 100 instancias de máquina virtual de tamaño A1 para su servicio en la nube o 50 instancias de máquina virtual de tamaño A2.

## <a name="networking"></a>Redes
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>No se puede reservar una dirección IP en un servicio en la nube con varias direcciones IP virtuales
En primer lugar, asegúrese de que la instancia de máquina virtual para la que está intentando reservar la dirección IP está activada. En segundo lugar, asegúrese de que utiliza direcciones IP reservadas para las implementaciones de ensayo y de producción. **No** cambie la configuración mientras se está actualizando la implementación.

## <a name="remote-desktop"></a>Escritorio remoto
### <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>¿Cómo se usa el escritorio remoto con un NSG?
Agregue una regla al NSG que reenvía al puerto **20000**.

