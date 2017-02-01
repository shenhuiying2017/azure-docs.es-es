---
title: Azure Marketplace para asociados de Azure Government | Microsoft Docs
description: "Este artículo ofrece una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para Azure Government."
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>Azure Marketplace para Azure Government
Si es un asociado de Azure Government interesado en las ofertas de publicación para Azure Marketplace, busque los detalles de este artículo.

Las plantillas de la solución y las imágenes de máquina virtual de BYOL se admiten actualmente en Azure Government Marketplace. Como práctica recomendada, las plantillas de solución deben revisarse antes de publicar en Azure Government para asegurarse de que funcionará en la nube de Azure Government y la nube pública de Azure. Si solo va a publicar una imagen de máquina virtual, puede seguir con los pasos de publicación siguientes.

## <a name="pre-publishing-validation-for-solution-templates"></a>Validación de la publicación previa para plantillas de soluciones

Antes de publicar la plantilla de solución en Azure Government, recomendamos comprobar un par de áreas de prácticas recomendadas frecuentes para garantizar la plantilla funcionarán en la nube pública de Azure y Azure Government.

1.  Compruebe que los puntos de conexión no estén codificados de forma rígida en la plantilla de solución de la nube pública de Azure. No serán válidos para los demás entornos de Azure. En su lugar, modifique la plantilla de solución para solicitar una llamada API para extraer el punto final válido:  

  Ejemplo:

  Identificador URI de VHD incorrecto (codificado de forma rígida) "uri": "[concat('https://', variables('storageAccountName'), '.blob.core.windows.net/',  '/osdisk.vhd')]",

  Identificador URI de VHD correcto (con referencia)

  "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'osdisk.vhd')]",

  La sintaxis corregida garantizará que la plantilla funcionará en cualquier nube (Gov, Public Azure, AzureStack, China, etc.)

2.  Compruebe que los recursos usados en la plantilla de solución están disponibles la nube independiente en la que está realizando la publicación.
RP en Azure y versión de API

    Identificar la disponibilidad de Azure Government con el Explorador de recursos en el portal:

  1.    Iniciar sesión en el Portal de Azure Government
  2.    Inicie Resource Explorer siguiendo los pasos que aparecen aquí https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services#supported-regions

  Extensiones usadas con más frecuencia; Disponible en Fairfax  

  | Publicador/tipo | Versiones disponibles en Fairfax |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1; 1.1; 1.2.2.0; 1.3.0.2; 1.4.1.0; 1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2; 1.3; 1.4; 1.7; 1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | No disponible |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005; 2.1.9005; 2.2.9005; 2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> Si coloca ubicaciones para una lista de valores permitidos, tendrá que actualizarla periódicamente cuando se agreguen nuevas regiones.  


## <a name="publishing"></a>Publicación
> [!NOTE]
> Si no es un asociado de Azure Certified Marketplace existente, complete los pasos que muestran cómo [publicar y administrar una oferta](../marketplace-publishing/marketplace-publishing-getting-started.md) antes de continuar.
>
>

### <a name="step-1"></a>Paso 1
Inicie sesión en [Publicación de Azure](https://publish.windowsazure.com).

### <a name="step-2"></a>Paso 2
Haga clic en la oferta que quiera publicar.

### <a name="step-3"></a>Paso 3
Haga clic en **SKU** y seleccione el cuadro **Nube de administración pública de Azure**.

> [!NOTE]
> Solo se admiten las SKU de traiga su propia licencia (BYOL).  Esta opción no está disponible para las SKU de pago por uso.
>
>

![Página de la oferta donde se encuentran las opciones de SKU y la nube de Azure Government](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>Paso 4
Haga clic en el vínculo **+ Add Certification** (+ Agregar certificación) para agregar vínculos a las certificaciones para su oferta.

![Página de oferta con el vínculo de Agregar certificación](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>Paso 5
Para probar la imagen en el portal de publicación, solicite una [cuenta de prueba](https://azuregov.microsoft.com/trial/azuregovtrial) para Microsoft Azure Government.

Se comprueba su elegibilidad como asociado que trabaja para entidades federales, estatales, locales o tribales de EE. UU. y se le enviará la confirmación por correo electrónico.  La cuenta de prueba estará disponible en entre tres y cinco días laborables.

### <a name="step-6"></a>Paso 6
Haga clic en **Publicar** y en **Push to Staging** (Pasar a ensayo).

![Publicar e insertar en opciones de almacenamiento provisional](./media/government-manage-marketplace-partner-3.png)

Se le pedirá que especifique una suscripción en la lista blanca que tiene acceso a la oferta almacenada provisionalmente. Escriba el identificador de suscripción de su cuenta de prueba de Azure Government recién adquirida.

![Preguntar dónde especificar los identificadores de suscripción que pueden obtener acceso a la oferta](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>Paso 7
Después de que se almacene provisionalmente la oferta, puede probar la imagen iniciando sesión en [Azure Portal](https://portal.azure.us) con su cuenta de prueba de Azure Government.

### <a name="step-8"></a>Paso 8
Cuando haya validado la imagen mediante la suscripción de prueba, puede poner la oferta a disposición en vivo haciendo clic en **Publicar** y solicitando autorización para pasar a producción.

![Solicitar la aprobación para ir al comando de producción](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al [blog de Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/).



<!--HONumber=Dec16_HO3-->


