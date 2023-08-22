# 修改 PowerShell 執行權限
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned

# 安裝 ExchangeOnlineManagement 模塊
Install-Module -Name Microsoft.Online.SharePoint.Powershell -Force

# 設定管理 Url
$SPOAdminUrl = "https://xxxxxx-admin.sharepoint.com/"

# 連接 SPO 服務
Connect-SPOService -Url $SPOAdminUrl

# 獲取所有 OneDrive Url
$sites = Get-SPOSite -IncludePersonalSite $true -Limit ALL | ? Url -Like "*/personal/*xxxxxx" | Sort-Object Url

# Export OneDrive Url to CSV
$sites | Export-Csv -Path "C:\ExportFileName.csv" -NoTypeInformation

# 設定只讀（警告：所有 OneDrive）
$sites | Set-SPOSite -LockState Readonly
	
# 解除只讀（所有 OneDrive）
$sites | Set-SPOSite -LockState Unlock

# 設定只讀（單個 OneDrive）
Set-SPOSite https://your_domain-my.sharepoint.com/personal/user1_domain_com -LockState Readonly

# 解除只讀（單個 OneDrive）
Set-SPOSite https://your_domain-my.sharepoint.com/personal/user1_domain_com -LockState Unlock

# 設定只讀 （多個 OneDrive）
$file = Import-Csv -Path "C:\ImportFileName.csv"

foreach ($item in $file) {
    $siteUrl = $item.OneDriveUrl
    Set-SPOSite -Identity $siteUrl -LockState "ReadOnly"
}

# 解除只讀（多個 OneDrive）
$file = Import-Csv -Path "C:\ImportFileName.csv"

foreach ($item in $file) {
    $siteUrl = $item.OneDriveUrl
    Set-SPOSite -Identity $siteUrl -LockState "Unlock"
}


