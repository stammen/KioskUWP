# KioskUWP

An example on how to run multiple UWP and Desktop Bridge Apps in Kiosk Mode.

The repo describes how to do the following:

1. Install a UWP app as the default fullscreen app in Kiosk Mode using [Shell Launcher V2](https://docs.microsoft.com/en-us/windows/configuration/kiosk-shelllauncher)

1. Launch a second packaged [Desktop Bridge](https://developer.microsoft.com/en-us/windows/bridges/desktop/) WPF app from the UWP app using a protocol.

1. Return to the UWP app when the WPF app is closed.


# Getting Started

The following applications are required to be installed:

* Visual Studio 2017 or 2019 with Windows Universal App Development package installed

* [SysInternals Suite](https://download.sysinternals.com/files/SysinternalsSuite.zip)


The KioskUWP.sln contains the following projects:

* KioskUWP - the main UWP app

* [TreeViewFileExplorer](https://github.com/mikependon/Tutorials/tree/master/WPF/TreeViewFileExplorer) - a WPF App that allows browsing of the files and drives on the computer.

* PackagingProject - A project to package the TreeViewFileExplorer app as a Desktop Bridge app.

Note: Both the PackagingProject (containing the TreeViewFileExplorer app) and the KioskUWP app will need to be deployed for All Users (see below) in order to be able to run in Kiosk mode.


# Build and Run

1. Open the KioskUWP.sln with Visual Studio 2017 or 2019

1. Select the x64/Any CPU configuration

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

1. **Important** Uninstall both applications. We will need to install these applications for All Users (see below).


# Setup the Kiosk Mode Apps

We will now package the KioskUWP and TreeViewFileExplorer apps so they can be installed for All Users. We will then create a simple Kiosk that allows the user to use these apps.

* **Important** Make sure you have uninstalled both applications. 

* Right click on the KioskUWP project and select Store | Create App Packages...

* Select "I want to create packages for sideloading". You can turn off "Enable automatic updates". Click Next

* Select the Architectures you want in your package (x64 is fine for now). Click on Create.

* Repeat the above steps for the PackagingProject.

* Open PowerShell as administrator. cd to this repo's directory

* cd to KioskUWP\AppPackages\KioskUWP_1.0.0.0_Debug_Test (or whatever version of the app you just built)

* Enter the following command


```console
Add-AppxProvisionedPackage -Online -SkipLicense -FolderPath .\KioskUWP_1.0.0.0_x64_Debug.msixbundle
```

Now repeat for the PackagingProject(make sure you navigate to to correct AppPackages folder in the PackagingProject folder)

```console
Add-AppxProvisionedPackage -Online -SkipLicense -FolderPath .\PackagingProject_1.0.0.0_AnyCPU_Debug.msixbundle
```

* You have now installed the 2 apps for All Users (even new accounts added to the computer)

* Note: You may need to uninstall these apps. Use the following command to uninstall:

```console
Get-AppxPackage -AllUsers WPFExplorer-8882ad0a-fa4e-4625-ad88-040cc6f78c60 | Remove-AppxPackage -AllUsers
```

* Note: WPFExplorer-8882ad0a-fa4e-4625-ad88-040cc6f78c60 is the Package Family Name (PFN) of the Packaging Project. Your PFN may be different. You can find the PFN for your app by double clicking on the Package,appxmanifest
file in the project and navigating to the Packaging Tab. The PFN will be at the bottom.

* Repeat the above command using the PFN for the KioskUWP app.

# Setup the Kiosk Mode

We will now setup a simple Windows 10 Kiosk account that will run our 2 packaged apps. You will need to add the PFN of your KioskUWP app to the ShellLauncherAutoLogonUwp.xml file at the root of this repo.

* Replace the PFN in the following line (Near line 11)

```xml      
<Shell Shell="1978ec3e-63e8-4caf-bd96-5f4d5a87f93c_e8xk87pxx0yyw!App" v2:AppType="UWP" v2:AllAppsFullScreen="true">
```

With the PFN of your KioskUWP app. Make sure you append !App at the end of your PFN

* You can now set up the Kiosk using the following instructions:


Note: These instructions are adapted from this [repo](https://github.com/microsoft/Windows-iotcore-samples/tree/develop/Samples/ShellLauncherV2/SampleBridgeWmiScripts)

[ShellLauncherBridgeWmiHelpers.ps1](./SampleBridgeWmiScripts/ShellLauncherBridgeWmiHelpers.ps1) provides below functions

* Set-ShellLauncherBridgeWmi, it takes a parameter FilePath to a raw config xml (not the escaped one) and configure Shell Launcher through bridge WMI 

* Clear-ShellLauncherBridgeWmi, it clears shell launcher configuration using bridge WMI

* Get-ShellLauncherBridgeWmi, it prints out the current shell launcher config xml if configured

To use the scripts,

* Save the scripts file to your PC

* Download [SysInternals Suite](https://download.sysinternals.com/files/SysinternalsSuite.zip), run "psexec.exe -i -s powershell.exe" from elevated command prompt

* In the powershell launched by psexec.exe, first import the scripts, notice the . command when importing the ps1 file

```
PS C:\Users\test> . .\ShellLauncherBridgeWmiHelpers.ps1
```

* After importing, run the command Set-ShellLauncherBridgeWMI with FilePath pointing to a shell launcher config xml

```
PS C:\Users\test> Set-ShellLauncherBridgeWmi -FilePath .\ShellLauncherAutoLogonUwp.xml
```

* To clean up ShellLauncher using bridge WMI, run the other command Clear-ShellLauncherBridgeWMI

```
PS C:\Users\test> Clear-ShellLauncherBridgeWmi
```

* To print current config xml, run the other command Get-ShellLauncherBridgeWMI

```
PS C:\Users\test> Get-ShellLauncherBridgeWmi
```

## Run the Kisok

* Login into the computer using the Kiosk account

* The KioskUWP app should appear fullscreen.

1. Click on the Launch WPF App button. The WPF app should appear on top of the UWP app.

1. Close the WPF app. The UWP app should now appear.


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
