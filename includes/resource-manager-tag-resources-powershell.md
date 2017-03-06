La versión 3.0 del módulo AzureRm.Resources incluye cambios significativos en la forma de trabajar con las etiquetas. Antes de continuar, compruebe su versión:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Si el resultado muestra una versión 3.0 o posterior, los ejemplos de este tema funcionan con su entorno. Si no tiene la versión 3.0 o posterior, [actualice su versión](/powershell/azureps-cmdlets-docs/) utilizando la Galería de PowerShell o el Instalador de plataforma web antes de continuar con este tema.

```powershell
Version
-------
3.5.0
```

Cada vez que aplique etiquetas a un recurso o grupo de recursos, sobrescribirá las etiquetas existentes en ese recurso o grupo de recursos. Por lo tanto, tiene que utilizar un enfoque diferente en función de si el recurso o grupo de recursos tiene etiquetas existentes que desea conservar. Para agregar etiquetas a un:

* grupo de recursos sin etiquetas existentes.

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* grupo de recursos con etiquetas existentes.

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* recurso sin etiquetas existentes.

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* recurso con etiquetas existentes.

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y **no conservar ninguna de las etiquetas existentes en los recursos**, use el siguiente script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y **conservar las etiquetas existentes en los recursos que no son duplicados**, use el siguiente script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Para quitar todas las etiquetas, pase una tabla hash vacía.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

Para obtener grupos de recursos con una etiqueta específica, use el cmdlet `Find-AzureRmResourceGroup`.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Para obtener todos los recursos con una etiqueta y un valor concretos, use el cmdlet `Find-AzureRmResource`.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

