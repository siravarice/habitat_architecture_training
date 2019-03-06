# habitat_architecture_training
Repository for Habitat training notes and walkthro# Habitat Local Development - Windows

Developing Habitat packages locally in Windows can be done with docker or virtual box. The recommendation from NGSE is to use virtual box instead of docker at this time due the fact that many people are already familiar with these tools. There may be work in the future to include docker into the standard local development environment.

## Getting Started

Required before you begin:

- User GPO = 100
- Computer GPO = 100
- Local admin rights
- Github Account <https://github.com>
- Habitat Builder Account <https://bldr.habitat.sh/#/sign-in> (sign in with GitHub and grant GitHub control it requests)
- Habitat Origin (created from your Builder account)
- Access Token (Also created from your Builder account)

### Creating your Origin

Log into your Habitat Builder account, click _My Origins_ in the left hand bar.

Click the _Create Origin_ button. 

Give your origin an easy to remember name, such as your CDSID.

Select _Publick Packages_.

Click _Save & Continue_.

### Setup your Access Token

Click your profile icon in the top right and select _Profile_.

At the bottom of the page where it says **Personal Access Token**, click the _Generate Token_ button.

Once generated, this can be copied and stored somewhere secure, you will need it later when running through the installation process.

## Install ToolChain

Open a PowerShell window as an *administrator* and follow instructions.

> Note: Feel free to skip individual steps if software is already installed by another means

```Powershell

#Ensure your current PowerShell version is high enough
$PSVersionTable.PSVersion.Major -ge 5

#Configure execution Policy so you can run .ps1 files
Set-ExecutionPolicy RemoteSigned -Confirm:$false -force

#Download and execute the Chocolatey installation script
Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://www.gsoutils.ford.com/files/scripts/install_choco.ps1'))

#Tell choco source to ignore proxy
choco source add -n=chocolatey -shttps://www.gsoutils.ford.com/packages/nuget --bypassproxy

#Install Virtualbox
choco install virtualbox -y

#Install Vagrant
choco install vagrant -y

#upgrade to the latest version of Habitat cli
choco upgrade habitat -y

#Install git command line
choco install git.portable -y

#Install vscode with ford settings like proxy, formatting, etc
choco install vscode.fordbase -y

#install vscode powershell plugin
choco install vscode-powershell

#Install nuget assembly to help with downloading powershell modules
choco install nuget.providerassembly -y

#Configure Ford Powershell Repository
Unregister-PSRepository gsoutils -ErrorAction SilentlyContinue
Register-PSRepository -Name gsoutils -SourceLocation "https://www.gsoutils.ford.com/powershell/nuget"  -InstallationPolicy Trusted

#install local development module
Find-Module -repository gsoutils Ford-Localdev|Install-Module

#install git command prompt module
Find-Module -repository gsoutils Posh-git|Install-Module

#install Habitat Helper module
Find-Module -repository gsoutils PSHabitat|Install-Module

#configure local development environments
Remove-Module ford-localdev -force -erroraction SilentlyContinue
Update-LocalDevEnvironment -verbose
Repair-LocalDevEnvironment -verbose
Enable-LocalDevProfile -verbose
Set-LocalDevGitConfig -verbose
Disable-HttpProxy -verbose

#restart powershell, restart windows
exit

```

## Configuration

> Make sure all software in the toolchain has been installed and the system has been rebooted

### Load Vagrant-VirtualBox images

Downloading the latest verision of OS images to a vagrant cache folder can speed up testing later.

```PowerShell
#add windows OS Image cache
vagrant box add https://www.nexus.ford.com/repository/ngdc_auto_deploy_public_raw_repository/images/vagrant/ford_win2016std_desk_base.json

#add linux OS Image cache
vagrant box add http://www.nexus.ford.com/repository/ngdc_auto_deploy_public_raw_repository/images/vagrant/boxes/sles/sles12sp3.json

#show vagrant box files cached to system
vagrant box list
```

### Configure Habitat

Visit [Profile Page on Habitat Depot server](http://ito000604.fhc.ford.com/#/profile) (Authenticate through Github) and generate a `Habitat Personal Token`

> Note: Token will look similar to this: `_Qk9YLTEKYsmdfksdjfGHJGGJHGkdsfjhkaSDSDSDKJKLSJDLKSLKDSKLDJSLKDJLKShfadhsfkasksjadkashdushaJKHKJSHshSKDhkjshkSHDSKJJDkjHSDJkhKJDHLKSLKalkslakjsLKJWKLHWEKJHSDKLHhjsadjhsadg==`

```PowerShell
#Define Environment
$HAB_BLDR_URL = 'http://ito000604.fhc.ford.com'
$HAB_AUTH_TOKEN = '<Your Habitat Personal Token>' #<- Must replace <Your Habitat Personal Token> with token generated from Habitat Depot server
$HAB_ORIGIN = ($env:username).tolower() #Can change to another origin if needed

Set-LocalEnvironmentVariable -name HAB_BLDR_URL -value $HAB_BLDR_URL -Type User
Set-LocalEnvironmentVariable -name HAB_BLDR_URL -value $HAB_BLDR_URL -Type Current

Set-LocalEnvironmentVariable -name HAB_AUTH_TOKEN -value $HAB_AUTH_TOKEN -Type User
Set-LocalEnvironmentVariable -name HAB_AUTH_TOKEN -value $HAB_AUTH_TOKEN -Type Current

Set-LocalEnvironmentVariable -name HAB_ORIGIN -value $HAB_ORIGIN -Type User
Set-LocalEnvironmentVariable -name HAB_ORIGIN -value $HAB_ORIGIN -Type Current

#Download keys from Habitat Depot server
Sync-HabPrivateKey -Origin $HAB_ORIGIN
Sync-HabPublicKey -Origin $HAB_ORIGIN

```

Validate settings:
```PowerShell

#All environment variables are set
dir env:hab*

Name                           Value
----                           -----
HAB_BLDR_URL                   http://ito000604.fhc.ford.com
HAB_AUTH_TOKEN                 _Qk9YLTEKYsmdfksdjfGHJGGJHGkdsfjhkaSDSDSDKJKLSJDLKSLKDSKLDJSLKDJLKShfadhsfkasksjadkashdushaJKHKJSHshSKDhkjshkSHDSKJJDkjHSDJkhKJD...
HAB_ORIGIN                     abaker9

```
### Setup Habitat
```Powershell

#run Habitat setup
hab setup

Connect to an on-premises bldr instance? [Yes/no/quit] yes

Private builder url: [default: http://ito000604.fhc.ford.com]

Set up a default origin? [Yes/no/quit] yes

Default origin name: [default: <your username>]

Set up a default Habitat personal access token? [Yes/no/quit] yes

Habitat personal access token: [default: <Your Personal Token>]

Set up a default Habitat Supervisor CtlGateway secret? [Yes/no/quit] no

Add binlink directory to PATH? [Yes/no/quit] yes

Enable analytics? [Yes/no/quit] no
```
## Create Directory to work from

It is easier to create a directory to work from on your desktop.

``` Powershell

#Navigate to your desktop and replace <Your username> with your CDSID
cd Windows\Users\<Your username>\Desktop

#Create a folder to any git repository files to be copied to, and for Habitat Initialise files to be created
mkdir habitat_training

#Navigate to your new folder
cd habitat_training

```

### Fork the habitat training repository on gitbub into your own git. 
 
Open the following link:
https://github.com/chef-training/habitat_jumpstart

[Back to Main](README.md)
