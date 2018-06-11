Con Azure Resource Manager, puede definir los parámetros de los valores que se usan al implementar la plantilla. La plantilla incluye una sección `parameters` que contiene todos los valores de los parámetros. La plantilla usa cada uno de estos valores de los parámetros para definir los recursos que se desea implementar.

> [!NOTE]
> No defina parámetros para valores que siempre permanezcan igual. Defina parámetros solo para los valores que varíen, en función del proyecto que vaya a implementar o del entorno en el que vaya a realizar la implementación.

Cuando defina los parámetros:

* Para especificar los valores que un usuario puede proporcionar durante la implementación, use el campo **allowedValues**.

* Para asignar los valores predeterminados a un parámetro cuando no se proporcionan valores durante la implementación, use el campo **defaultValue**. 
