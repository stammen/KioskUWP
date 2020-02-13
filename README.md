#KioskUWP

An example on how to run multiple UWP and Desktop Bridge Apps in Kiosk Mode

# Getting Started

The following applications are required to be installed:

* Visual Studio 2017 or 2019 with Windows Universal App Development package installed

# Build and Run


1. Open the KioskUWP.sln with Visual Studio 2019

1. Select the x64/Debug configuration

1. Double-click on the Package.appxmanifest file in the KioskUWP project. 

	* Navigate to the Packaging tab
	
	* Click on Choose Certificate...
	
	* Click on Configure Certificate...
	
	* Select Create test certificate...
	
	* Click on OK (do not enter a password)
	
1. Double-click on the Package.appxmanifest file in the PackagingProject project. 

	* Navigate to the Packaging tab
	
	* Click on Choose Certificate...
	
	* Click on Configure Certificate...
	
	* Select Create test certificate...
	
	* Click on OK (do not enter a password)
	
1. Build the solution

1. Deploy the solution

1. Run the KioskUWP application.

1. Click on the Launch WPF App button. The WPF app should appear

1. Exit both applications.

1. Uninstall both application.


# Setup Kiosk Mode

Note: These instructions are adapted from this [repo](https://github.com/microsoft/Windows-iotcore-samples/tree/develop/Samples/ShellLauncherV2/SampleBridgeWmiScripts)

# Shell Launcher V2 Bridge WMI Sample scripts

[ShellLauncherBridgeWmiHelpers.ps1](./ShellLauncherBridgeWmiHelpers.ps1) provides below functions
1. Set-ShellLauncherBridgeWmi, it takes a parameter FilePath to a raw config xml (not the escaped one) and configure Shell Launcher through bridge WMI 

2. Clear-ShellLauncherBridgeWmi, it clears shell launcher configuration using bridge WMI

3. Get-ShellLauncherBridgeWmi, it prints out the current shell launcher config xml if configured

To use the scripts,

1. Save the scripts file to your PC

2. Download [SysInternals Suite](https://download.sysinternals.com/files/SysinternalsSuite.zip), run "psexec.exe -i -s powershell.exe" from elevated command prompt

3. In the powershell launched by psexec.exe, first import the scripts, notice the . command when importing the ps1 file

```
PS C:\Users\test> . .\ShellLauncherBridgeWmiHelpers.ps1
```

4. After importing, run the command Set-ShellLauncherBridgeWMI with FilePath pointing to a shell launcher config xml

```
PS C:\Users\test> Set-ShellLauncherBridgeWmi -FilePath .\ShellLauncher.xml
```

5. To clean up ShellLauncher using bridge WMI, run the other command Clear-ShellLauncherBridgeWMI

```
PS C:\Users\test> Clear-ShellLauncherBridgeWmi
```

6. To print current config xml, run the other command Get-ShellLauncherBridgeWMI

```
PS C:\Users\test> Get-ShellLauncherBridgeWmi
```


Add-AppxProvisionedPackage -Online -SkipLicense -FolderPath .\PackagingProject_1.0.0.0_Debug_Test\PackagingProject_1.0.0.0_AnyCPU_Debug.msixbundle
Add-AppxProvisionedPackage -Online -SkipLicense -FolderPath .\KioskUWP_1.0.0.0_x64_Debug.msixbundle


How to Remove an AppPackage for all users

Get-AppxPackage -AllUsers WPFExplorer-8882ad0a-fa4e-4625-ad88-040cc6f78c60 | Remove-AppxPackage -AllUsers

Configure Kiosk
Set-ShellLauncherBridgeWmi -FilePath .\ShellLauncherAutoLogonUwp.xml



##  Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.


## Reporting Security Issues

Security issues and bugs should be reported privately, via email, to the Microsoft Security
Response Center (MSRC) at [secure@microsoft.com](mailto:secure@microsoft.com). You should
receive a response within 24 hours. If for some reason you do not, please follow up via
email to ensure we received your original message. Further information, including the
[MSRC PGP](https://technet.microsoft.com/en-us/security/dn606155) key, can be found in
the [Security TechCenter](https://technet.microsoft.com/en-us/security/default).
