![](https://repository-images.githubusercontent.com/191902560/72069b00-8eae-11e9-866c-c2e4fb8b7a40)
# Laravel Homestead Setup Guide For Windows 10 (Intel Processors)
A step by step setup instruction for Laravel Homestead on Windows 10.
## 1. Checking Prerequisites
Before installing Homestead on Windows, check if hardware virtualization is enabled in your machine. Download and install [Intel® Processor Identification Utility](https://downloadcenter.intel.com/download/28539/), run the application and check if Intel Virtualization technology is enabled under CPU technologies.
If hardware virtualization is disabled, then go to _Control Panel\Programs\Programs and Features_ and select _Turn Windows features on or off_ option from sidebar. Here, disable Hyper-V.
## 2. Installing VirtualBox
[Download](https://www.virtualbox.org/wiki/Downloads) and install VirtualBox. During the installation process, make sure all options are selected.
## 3. Installing Vagrant
[Download](https://www.vagrantup.com/downloads.html) and install Vagrant. After installation is complete, restart PC to make sure Vagrant is properly configured.
## 4. Git And Console Prequisite
For the next steps, you'll need Git and a Console. You can [download Git for Windows](https://git-scm.com/download/win) and then [download Cmder Mini](https://cmder.net/), unpack, and run _Cmder.exe_.
## 5. Installing Homestead Vagrant Box
Open console and type in the following command to install a Vagrant box for Homestead:
```sh
vagrant box add laravel/homestead
```
## 6. Cloning Homestead
From console, go to default "home" folder _C:\Users\USER_NAME_, and type in the following to install Homestead (globally)
```sh
git clone https://github.com/laravel/homestead.git Homestead
```
Once the cloning is complete, go to Homestead folder by typing the following in Console
```sh
cd Homestead
```
Then, to initialize Homestead, type in the following in Console
```sh
init.bat
```
## 7. Creating SSH Keys
To create the SSH keys, go to your "home" folder _C:\Users\USER_NAME_, and check if the folder _.ssh_ exists.
If the folder exists, navigate into the folder and see if two files named _id_rsa_ and _id_rsa.pub_ are present.
If they are present, continue to [Step 8]https://github.com/iftee/laravel-windows-setup#8-configuring-homestead.
If the folder .ssh does not exist or the folder exists but the two files are not present. Run the following command from Console replacing _my-email@example.com_ with your own email address.
```sh
ssh-keygen -t rsa -C "my_email@example.com"
```
Keep pressing enter when the Console prompts, which will create a folder named _.ssh_ with two files named _id_rsa_ and _id_rsa.pub_ in it.
## 8. Configuring Homestead
Go to the folder _C:\Users\USER_NAME\Homestead_ and open the file _Homestead.yaml_ in a code editor to edit as following:
Change the value of `authorize` from `~/.ssh/id_rsa.pub` to `c:/Users/USER_NAME/.ssh/id_rsa.pub` and the value of `keys` from `- ~/.ssh/id_rsa` to `- c:/Users/USER_NAME/.ssh/id_rsa` where _USER_NAME_ is your username.
```
authorize: c:/Users/USER_NAME/.ssh/id_rsa.pub

keys:
    - c:/Users/USER_NAME/.ssh/id_rsa
```
Note: For file paths, use lowercase drive letter and forward-slash instead of the backslash.
Then map your code folder in PC to the code folder in Vagrant like the following. In this case, I'm assuming the _code_ folder is inside your user folder where _USER_NAME_ is your username, but this code folder can be elsewhere in your PC as well or can have a different name.
```
folders:
    - map: c:/Users/USER_NAME/code
      to: /home/vagrant/code
```
Note: As these two location are mapped, once you run the Vagrant Homestead Box, ssh into the virtual machine, and go inside the virtual code folder, any file you make here will be created in your actual code folder.
Lastly, map your local development site address (assuming example.com in this case) to your the public folder inside your Laravel project. For Example, I'm assuming a Laravel project called _my-project_ will be in the _code_ folder we just mapped before.
```
sites:
    - map: my-project.test
      to: /home/vagrant/code/my-project/public
```
This way, for adding new Laravel projects to Homestead, you can keep adding new sites here. For example, you can add another Laravel project _second-project_ to Homestead like below.
```
sites:
    - map: my-project.test
      to: /home/vagrant/code/my-project/public
    - map: second-project.test
      to: /home/vagrant/code/second-project/public
```
The next line in the .yaml file will simply create a database name _homestead_ in Vagrant. You can use new databases by adding their under `databases:`
## 9. Editing Host File
To make the site available in browser, add it in the _hosts_ file located in _C:\Windows\System32\drivers\etc\_ with a code editor as following. You can add all your Laravel sites with same Homestead IP defined previously.
```
192.168.10.10 my-project.test
192.168.10.10 second-project.test
```
## 10. Starting Homestead
From console, go to _C:\Users\USER_NAME\Homestead_ and run the following command:
```sh
vagrant up
```
Right now, this command can only be run from within the Homestead folder. By creating aliases, you can run it from anywhere using console. To do this, create a _.bash_profile_ file inside _C:\Users\USER_NAME\Homestead_ with the following lines and then restart console.
```sh
alias ..="cd .."
alias vm="ssh vagrant@127.0.0.1 -p 2222"
function homestead() {
  ( cd ~/Homestead && vagrant $* )
}
```
Using the command `vagrant halt` will stop the running Vagrant Box.
## 11. Creating Laravel Project
After the `vagrant up` command, Homestead virtual machine will start running and then you can use the following console command inside _C:\Users\USER_NAME\Homestead_ folder to login to Homestead virtual machine.
```sh
vagrant ssh
```
Then go inside the _code_ folder
```sh
cd code
```
and use the following command to create the previously assumed Laravel project _my-project_.
```sh
composer create-project --prefer-dist laravel/laravel my-project
```
This will create the _my-project_ Laravel project and will install the required files.
## 12. Running The Application
You can now run the application using the link http://my-project.test in a browser. However, this doesn't complete your Laravel application setup as you'll have to modify the _.env_ file to complete that. But upto step 10 will complete the Homestead setup and make it run. You can use the command `vagrant reload --provision` to reboot the virtual machine and run provisioning steps if any.
## Sources
1. https://tutsforweb.com/installing-laravel-homestead-on-windows-step-by-step/
2. https://laravel.com/docs/5.8/homestead
