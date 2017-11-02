Los ejemplos de este artículo requieren la versión 3.0 o posterior de Azure PowerShell. Si no tiene la versión 3.0 o posterior, [actualice su versión](/powershell/azureps-cmdlets-docs/) utilizando la Galería de PowerShell o el Instalador de plataforma web.

Para ver las etiquetas existentes de un *grupo de recursos*, use:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Ese script devuelve el siguiente formato:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Para ver las etiquetas existentes de un *recurso que tiene un identificador de recurso especificado*, use:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

O bien, para ver las etiquetas existentes para un *recurso que tiene un nombre y un grupo de recursos especificados*, use:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Para obtener *grupos de recursos que tengan una etiqueta específica*, use:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Para obtener *recursos que tengan una etiqueta específica*, use:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Cada vez que aplique etiquetas a un recurso o grupo de recursos, sobrescribirá las etiquetas existentes en ese recurso o grupo de recursos. Por lo tanto, tiene que utilizar un enfoque diferente en función de si el recurso o grupo de recursos tienen etiquetas existentes.

Para agregar etiquetas a un *grupo de recursos sin etiquetas*, use:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Para agregar etiquetas a un *grupo de recursos que ya tiene etiquetas*, recupere las etiquetas existentes, agregue la nueva y vuelva a aplicar todas:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Para agregar etiquetas a un *recurso sin etiquetas*, use:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Para agregar etiquetas a un *recurso que ya tiene etiquetas*, use:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y *no conservar ninguna de las etiquetas existentes en los recursos*, use el siguiente script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y *conservar las etiquetas existentes en los recursos que no son duplicados*, use el siguiente script:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Para quitar todas las etiquetas, pase una tabla hash vacía:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```
