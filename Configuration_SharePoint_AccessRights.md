# 修改 PowerShell 執行權限
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned

# 安裝 ExchangeOnlineManagement 模塊
Install-Module -Name Microsoft.Online.SharePoint.Powershell -Force

# 設定管理 Url
$SPOAdminUrl = "https://xxxxx-admin.sharepoint.com/"

# 連接 SPO 服務
Connect-SPOService -Url $SPOAdminUrl

# 獲取所有 Site Url
$sites = Get-SPOSite | ? Url -Like "*/sites/*" | select Title,Url,Owner,LockState | sort url

# Export sites.csv （編輯 sites.csv，保留要設定只讀的站點）
$sites | Export-Csv -NoTypeInformation -Encoding UTF8 c:\ExportFileName.csv

# 設定只讀（警告：表中所有 SharePoint 站點將被設為只讀）
foreach ($s in $sites) {Set-SPOSite -LockState Readonly}

# 解除只讀
foreach ($s in $sites) {Set-SPOSite -LockState Unlock}

# 設定只讀（單個站點）
Set-SPOSite #OneDriveUrl# -LockState Readonly

	change v.1
	Set-SPOSite https://xxxxx.sharepoint.com/sites/"SharePointSiteName" -LockState Readonly

# 解除只讀（單個站點）
Set-SPOSite #OneDriveUrl# -LockState Unlock

	change v.1
	Set-SPOSite https://xxxxx.sharepoint.com/sites/"SharePointSiteName" -LockState Unlock

# 設定只讀（多個站點）
$file = Import-Csv -Path "c:\ImportFileName.csv"

foreach ($item in $file) {
    $siteUrl = $item.SharePointUrl
    Set-SPOSite -Identity $siteUrl -LockState "ReadOnly"
}

# 解除只讀（多個站點）
$file = Import-Csv -Path "c:\ImportFileName.csv"

foreach ($item in $file) {
    $siteUrl = $item.SharePointUrl
    Set-SPOSite -Identity $siteUrl -LockState "Unlock"
}
