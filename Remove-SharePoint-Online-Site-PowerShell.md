# Removing a SharePoint Online Site Collection with Powershell

Removing a Site collection in the admin page in SharePoint takes 30 days to be removed. With PowerShell did can be faster. 

```powershell
PS C:\> import-module Microsoft.Online.Sharepoint.PowerShell
PS C:\> connect-sposervice –url https://<site>-admin.sharepoint.com
PS C:\> remove-sposite –identity <site-collection> -nowait
```