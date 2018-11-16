cls
Add-PSSnapin Microsoft.Exchange.Management.PowerShell.SnapIn;
Import-Module -Name  "C:\Program Files\Microsoft\Exchange Server\V15\bin\Microsoft.Exchange.WebServices.dll"

$mailbox = "user@domain.com"
$FolderName = "FolderName"

$Service = New-Object Microsoft.Exchange.WebServices.Data.ExchangeService
$Service.AutodiscoverUrl($mailbox)
$Service.UseDefaultCredentials = $true
$Service.ImpersonatedUserId = New-Object Microsoft.Exchange.WebServices.Data.ImpersonatedUserId([Microsoft.Exchange.WebServices.Data.ConnectingIdType]::SmtpAddress,$mailbox)

$RootFolderID = New-Object Microsoft.Exchange.WebServices.Data.FolderID([Microsoft.Exchange.WebServices.Data.WellKnownFolderName]::Root,$mailbox)
$RootFolder = [Microsoft.Exchange.WebServices.Data.Folder]::Bind($Service,$RootFolderID)


$ItemView = New-Object Microsoft.Exchange.WebServices.Data.ItemView(100000)
$FolderView = New-Object Microsoft.Exchange.WebServices.Data.FolderView(1000)
$FolderView.Traversal = [Microsoft.Exchange.WebServices.Data.FolderTraversal]::Deep


$Folders = $RootFolder.FindFolders($FolderView)

ForEach ($Folder in $Folders.Folders) {

    if($folder.DisplayName -eq $FolderName){
        
        $Items = $folder.FindItems($ItemView)
        ForEach($Item in $Items){$Item.delete([Microsoft.Exchange.WebServices.Data.DeleteMode]::SoftDelete) } 

    } 
}