# Vagrant

Vagrant provides easy to configure, reproducible, and portable work environments (rvm, mysql, elasticsearch, redis, etc).

## Getting started

### Prerequisites:

1. Install standard ruby stack (Git, Ruby, RubyGems): [RailsInstaller](http://www.railsinstaller.org/) for Mac/Win or [How to install Ruby on Rails in Ubuntu on the Sudobits Blog](http://blog.sudobits.com/2012/05/02/how-to-install-ruby-on-rails-in-ubuntu-12-04-lts/) if you on Linux
note: if your Linux distro is not Ubuntu, so you must smart enough to install it by himself :)
2. Install VirtualBox: https://www.virtualbox.org/wiki/Downloads
3. Install Vagrant: http://www.vagrantup.com/ (We require Vagrant 1.1.2+ or later)
4. Open a terminal
5. Clone the project: `git clone https://github.com/pelesend/project_name.git`
6. Enter the project directory: `cd project_name`
7. Install Chef: `gem install chef`
8. Install Librarian (bundler for chef cookbooks): `gem install librarian-chef`

### First-time run (build VM)

Install required cookbooks for chef:
~~~ sh
$ librarian-chef install
~~~

Then boot up VM:
~~~ sh
$ vagrant up
~~~

Vagrant will prompt you for your admin password. This is so it can mount your local files inside the VM for an easy workflow.
(The first time you do this, it will take a while as it downloads the VM image and installs it.)

**Note to All users**: This is **very important** to sync project folder via NFS, because default shared folder implementations (such as VirtualBox shared folders) have high performance penalties.
[Additional info about NFS on Vagrant Docs](http://docs.vagrantup.com/v2/synced-folders/nfs.html)

**Note to Windows users**: NFS folders do not work on Windows hosts. Vagrant will ignore your request for NFS synced folders on Windows.

**Note to Linux users**: Your Project directory cannot be on an ecryptfs mount or you will receive an error: `exportfs: /home/your/path/to/project does not support NFS export`

**Note to OSX/Linux users**: Vagrant will mount your local files via an NFS share. Therefore, make sure that NFS is installed or else you'll receive the error message:

```
Mounting NFS shared folders failed. This is most often caused by the NFS
client software not being installed on the guest machine. Please verify
that the NFS client software is properly installed, and consult any resources
specific to the linux distro you're using for more information on how to
do this.
```

## Using

### Up

Once the machine has halted in last working session, you can boot up it again:

```
vagrant up
```


### SSH

Once the machine has booted up, you can shell into it by typing:

```
vagrant ssh
```

The project code is found in the /vagrant directory in the image.

**Note:** After `vagrant ssh` by default you appear at vagrant user home directory `~` or `/home/vagrant`, and for enter project directory you need run `cd /vagrant`

**Note to Windows users**: You cannot run ```vagrant ssh``` from a cmd prompt; you'll receive the error message:

```
`vagrant ssh` isn't available on the Windows platform. You are still able
to SSH into the virtual machine if you get a Windows SSH client (such as
PuTTY). The authentication information is shown below:

Host: 127.0.0.1
Port: 2222
Username: vagrant
Private key: C:/Users/Your Name/.vagrant.d/insecure_private_key
```

At this point, you will want to get an SSH client, and use it to connect to your Vagrant VM instead. We recommend
PuTTY:

**[PuTTY Download Link](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)**

You may use this client to connect to the VM by using ```vagrant/vagrant``` as your username/password, or by [using
PuTTYGen to import the insecure_private_key file](http://jason.sharonandjason.com/key_based_putty_logins_mini_how_to.htm)
(mentioned above) into a PuTTY profile to quickly access your VM.

### Ruby on Rails

Now you're in a virtual machine is almost ready to start developing. It's a good idea to perform the following instructions every time you pull from master to ensure your environment is still up to date.

```
cd /vagrant
bundle install
bundle exec rake db:migrate
bundle exec rails s
```

In a few seconds, rails will start serving pages. To access them, open a web browser to http://localhost:3000 - if it all worked you should see project site!
Congratulations, you are ready to start working!

You can now edit files on your local file system, using your favorite text editor or IDE. When you reload your web browser, it should have the latest changes.

### MySQL access from host

You may want to access MySQL DB with such admin tool as 'Sequel Pro', in this case use SSH connection type and this access details:

```
MySQL address: 10.0.2.15
user: 'root', password: 'password'

SSH address: 10.255.255.10
user: 'vagrant', password: 'vagrant'
```

more info: [Connect to a Remote MySQL Server](http://www.sequelpro.com/docs/Connecting_to_a_MySQL_Server_on_a_Remote_Host)

### Shutting down the VM

When you're done working on project, you can shut down Vagrant with:

exit from Vagrant shell
```
exit
```

and halt VM
```
vagrant halt
```
