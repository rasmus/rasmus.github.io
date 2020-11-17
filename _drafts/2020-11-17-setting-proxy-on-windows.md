
## System proxy

```
netsh winhttp set proxy proxy.example.org:3128 "localhost;*.example.com*"
```

## Internet Explorer proxy

```powershell
$reg = "HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
Set-ItemProperty -Path $reg -name ProxyServer -Value "proxy.example.org:3128"
Set-ItemProperty -Path $reg -name ProxyEnable -Value 1
Set-ItemProperty -Path $reg -Name ProxyOverride -Value "localhost;*.example.com"
```

## .NET Framework proxy

```powershell
function Get-ChildNode() {
    [OutputType([System.Xml.XmlNode])]
    param (
        [Parameter(Mandatory=$true)]
        [System.Xml.XmlDocument] $XmlDocument,

        [Parameter(Mandatory=$true)]
        [System.Xml.XmlNode] $XmlNode,

        [Parameter(Mandatory=$true)]
        [string] $Name
    )

    $Node = $XmlNode.SelectSingleNode($Name)

    if ($Node) {
        Write-Host "Found node $Name"
    } else {
        Write-Host "Did not find node $Name, creating it"
        $Node = $XmlDocument.CreateNode("element", $Name, "")
        $XmlNode.AppendChild($Node) | Out-Null
    }

    return $Node
}

function Set-Proxy() {
    param (
        [Parameter(Mandatory=$true)]
        [string] $FilePath
    )

    Write-Host "Setting proxy in $FilePath"

    $XmlDocument = [System.Xml.XmlDocument](Get-Content $FilePath)

    $Configuration = Get-ChildNode -XmlDocument $XmlDocument -XmlNode $XmlDocument -Name "configuration"
    $SystemNet     = Get-ChildNode -XmlDocument $XmlDocument -XmlNode $Configuration -Name "system.net"
    $DefaultProxy  = Get-ChildNode -XmlDocument $XmlDocument -XmlNode $SystemNet -Name "defaultProxy"

    $BypassList    = Get-ChildNode -XmlDocument $XmlDocument -XmlNode $DefaultProxy -Name "bypasslist"
    $Proxy         = Get-ChildNode -XmlDocument $XmlDocument -XmlNode $DefaultProxy -Name "proxy"

    $Proxy.SetAttribute("usesystemdefault", "true");
    $DefaultProxy.RemoveChild($BypassList);
    
    $XmlDocument.Save($FilePath)
}

$ErrorActionPreference = "Stop"

Set-Proxy -FilePath "C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config"
Set-Proxy -FilePath "C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config"
```