## <a name="overview-of-azure-resource-manager-templates"></a>Información general de las plantillas del Administrador de recursos de Azure
Las plantillas del Administrador de recursos de Azure le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje Json definiendo las dependencias entre recursos. Para obtener información más detallada de las plantillas del Administrador de recursos de Azure, consulte los siguientes artículos:

[Información general del grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md)

## <a name="sample-template-snippet-for-vm-extensions"></a>Fragmento de plantilla de ejemplo para extensiones de VM.
La implementación de extensiones de máquinas virtuales como parte de la plantilla de Administrador de recursos de Azure requiere que especifique de forma declarativa la configuración de la extensión en la plantilla.
Este es el formato para especificar la configuración de la extensión.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Como se puede ver en lo anterior, la plantilla de extensión contiene dos partes principales:

1. Nombre de extensión, editor y versión.
2. Configuración de la extensión.

## <a name="identifying-the-publisher-type-and-typehandlerversion-for-any-extension"></a>Identificación de publisher, type y typeHandlerVersion para cualquier extensión.
Tanto Microsoft como publicadores de terceros de confianza publican las extensiones de VM de Azure y cada extensión se identifica de forma exclusiva por publisher, type y typeHandlerVersion. Pueden determinarse de la manera siguiente:  



<!--HONumber=Nov16_HO3-->


