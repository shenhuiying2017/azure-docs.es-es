<properties
	pageTitle="P+F de Servicios en la nube | Microsoft Azure"
	description="Preguntas más frecuentes acerca de los Servicios en la nube."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="adegeo"/>

# P+F de Servicios en la nube
En este artículo se responden algunas preguntas frecuentes sobre Servicios en la nube de Microsoft Azure. También puede visitar [Preguntas más frecuentes de soporte técnico de Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para información general sobre los precios y el soporte técnico de Azure. También puede consultar la [página Tamaños de los servicios en la nube](cloud-services-sizes-specs.md) para obtener información de tamaño.

## Certificados

### No se puede quitar el certificado expirado

Azure impide quitar un certificado mientras está en uso. Debe eliminar la implementación que utiliza el certificado o actualizar la implementación con un certificado diferente o renovado.

### Eliminar un certificado expirado

Siempre que el certificado no esté en uso, podrá usar el cmdlet de PowerShell [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) para quitar un certificado.

### Tengo certificados expirados denominados Administración de servicios de Microsoft Azure para Extensiones

Estos certificados se crean siempre que se agrega una extensión al servicio en la nube, como la extensión de Escritorio remoto. Estos certificados solo se utilizan para cifrar y descifrar la configuración privada de la extensión. No importa si estos certificados expiran. La fecha de expiración no se comprueba.

### Siguen apareciendo certificados que he eliminado

Estos siguen reapareciendo muy probablemente a causa de una herramienta que esté utilizando, como Visual Studio. Cada vez que vuelva a conectar con una herramienta que esté utilizando un certificado, este se volverá a cargar en Azure.

### Mis certificados siguen desapareciendo

Cuando se recicla la instancia de máquina virtual, se pierden todos los cambios locales. Use una [tarea de inicio](cloud-services-startup-tasks.md) para instalar certificados en la máquina virtual cada vez que se inicie el rol.

### ¿Dónde debo instalar mi certificado?

**My** Certificado de aplicación con clave privada (*.pfx, *.p12).

**CA** Todos los certificados intermedios van a este almacén (entidades de certificación de directivas y secundarias).

**ROOT** El almacén de entidades de certificación raíz, por lo que la mayoría de certificados de entidades de certificación raíz van aquí.

## Solución de problemas

### No se puede reservar una dirección IP en un servicio en la nube con varias direcciones IP virtuales

En primer lugar, asegúrese de que la instancia de máquina virtual para la que está intentando reservar la dirección IP está activada. En segundo lugar, asegúrese de que utiliza direcciones IP reservadas para las implementaciones de ensayo y de producción. **No** cambie la configuración mientras se está actualizando la implementación.

<!---HONumber=AcomDC_0824_2016-->