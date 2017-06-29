| Recurso | Límite predeterminado | Límite máximo |
| --- | --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (por tipo de recurso) |800 |Varía por tipo de recurso |
| Implementaciones por grupo de recursos |800 |800 |
| Recursos por implementación |800 |800 |
| Bloqueos de administración (por ámbito único) |20 |20 |
| Número de etiquetas (por recurso o grupo de recursos) |15 |15 |
| Longitud de la clave de etiqueta |512 |512 |
| Longitud del valor de la etiqueta |256 |256 |


#### <a name="template-limits"></a>Límites de plantilla

| Valor | Límite predeterminado | Límite máximo |
| --- | --- | --- |
| parameters |256 |256 |
| Variables |256 |256 |
| Recursos (incluido el recuento de copia) |800 |800 |
| Salidas |64 |64 |
| Expresión de plantilla |24 576 caracteres |24 576 caracteres |
| Recursos de plantillas exportadas |200 |200 | 
| Tamaño de la plantilla |1 MB |1 MB |
| Tamaño del archivo de parámetros |64 KB |64 KB |

Puede superar algunos límites de plantilla utilizando una plantilla anidada. Para más información, consulte [Uso de plantillas vinculadas en la implementación de recursos de Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Para reducir el número de parámetros, variables o salidas, puede combinar varios valores en un objeto. Para más información, consulte [Objetos como parámetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).