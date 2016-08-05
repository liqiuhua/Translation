# 树莓派Pi 2

# t树莓派2自动驾驶仪

![rpi2](../pictures/hardware/hardware-rpi2.jpg)

## 快速开发指南

### 系统镜像

使用[Emlid RT Raspbian image](http://docs.emlid.com/navio/Downloads/Real-time-Linux-RPi2/)这个前期配置好的有效的PX4树莓派镜像。这个镜像默认最大化的事先配置了程序。

### 进入设置

此树莓派镜像已经事先设置好了SSH。用户名：pi和密码是：raspberry。你可以直接通过网络去连接你的树莓派2（以太网已经启动并且默认自动分配IP）和可以配置使用wifi。在这篇指南中，我们采取默认的用户名和密码登入树莓派系统。

设置你的树莓派加入你的本地wifi（使你的树莓派连接你的wifi），参考这篇指导文章。 [这篇指导文章](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md).

找到你的树莓派在你的网络中IP地址，随后你可以开始通过SSH的方式连接Pi2。
<div class="host-code"></div>

```sh
ssh pi@<IP-ADDRESS>
```

### Changing hostnames

为了避免与同一网络上的其他树莓派有冲突，我们建议你改变默认主机名为明显的名字。我们使用“px4autopilot”作为我们的主机名。通过ssh连接pi并执行指令。

Edit the hostname file:

```sh
sudo nano /etc/hostname
```

Change ```raspberry``` to whatever hostname you want (one word with limited characters apply)

Next you need to change the hosts file:

```sh
sudo nano /etc/hosts
```

Change the entry ```127.0.1.1 raspberry``` to ```127.0.1.1 <YOURNEWHOSTNAME>```

Reboot the Pi after this step is completed to allow it to re-associate with your network.

### Setting up Avahi (Zeroconf)

To make connecting to the Pi easier, we recommend setting up Avahi (Zeroconf) which allows easy access to the Pi from any network by directly specifying its hostname.

```sh
sudo apt-get install avahi-daemon
sudo insserv avahi-daemon
```

Next, setup the Avahi configuration file

```sh
sudo nano /etc/avahi/services/multiple.service
```

Add this to the file :

```xml
<?xml version="1.0" standalone='no'?>
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
        <name replace-wildcards="yes">%h</name>
        <service>
                <type>_device-info._tcp</type>
                <port>0</port>
                <txt-record>model=RackMac</txt-record>
        </service>
        <service>
                <type>_ssh._tcp</type>
                <port>22</port>
        </service>
</service-group>
```

Restart the daemon

```sh
sudo /etc/init.d/avahi-daemon restart
```

And that's it. You should be able to access your Pi directly by its hostname from any computer on the network.

<aside class="tip">
You might have to add .local to the hostname to discover it.
</aside>

### Configuring a SSH Public-Key

In order to allow the PX4 development environment to automatically push executables to your board, you need to configure passwordless access to the RPi. We use the public-key authentication method for this.

To generate new SSH keys enter the following commands (Choose a sensible hostname such as ```<YOURNANME>@<YOURDEVICE>```.  Here we have used ```pi@px4autopilot```)

These commands need to be run on the HOST development computer!

<div class="host-code"></div>

```sh
ssh-keygen -t rsa -C pi@px4autopilot
```

Upon entering this command, you'll be asked where to save the key. We suggest you save it in the default location ($HOME/.ssh/id_rsa) by just hitting Enter.

Now you should see the files ```id_rsa``` and ```id_rsa.pub``` in your ```.ssh``` directory in your home folder:

<div class="host-code"></div>

```sh
ls ~/.ssh
authorized_keys  id_rsa  id_rsa.pub  known_hosts
```

The ```id_rsa``` file is your private key. Keep this on the development computer.
The ```id_rsa.pub``` file is your public key. This is what you put on the targets you want to connect to.

To copy your public key to your Raspberry Pi, use the following command to append the public key to your authorized_keys file on the Pi, sending it over SSH:

<div class="host-code"></div>

```sh
cat ~/.ssh/id_rsa.pub | ssh pi@px4autopilot 'cat >> .ssh/authorized_keys'
```

Note that this time you will have to authenticate with your password ("raspberry" by default).

Now try ```ssh pi@px4autopilot``` and you should connect without a password prompt.

If you see a message "```Agent admitted failure to sign using the key.```" then add your RSA or DSA identities to the authentication agent, ssh-agent and the execute the following command:

<div class="host-code"></div>

```sh
ssh-add
```

If this did not work, delete your keys with ```rm ~/.ssh/id*``` and follow the instructions again.

### Testing file transfer

We use SCP to transfer files from the development computer to the target board over a network (WiFi or Ethernet).

To test your setup, try pushing a file from the development PC to the Pi over the network now. Make sure the Pi has network access, and you can SSH into it.

<div class="host-code"></div>

```sh
echo "Hello" > hello.txt
scp hello.txt pi@px4autopilot:/home/pi/
rm hello.txt
```

This should copy over a "hello.txt" file into the home folder of your RPi. Validate that the file was indeed copied, and you can proceed to the next step.

### Native builds (optional)

You can run PX4 builds directly on the Pi if you desire. This is the *native* build.
The other option is to run builds on a development computer which cross-compiles for the Pi, and pushes the PX4 executable binary directly to the Pi. This is the *cross-compiler* build, and the recommended one for developers due to speed of deployment and ease of use. For cross-compiling setups, you can skip this step.

The below installation script will automatically update the native build system on the Pi to that required by PX4. Run these commands on the Pi itself!

```sh
git clone https://github.com/pixhawk/rpi_toolchain.git
cd rpi_toolchain
chmod +x install_native.sh
./install_native.sh
```

### Building the code

Continue with our [standard build system installation](../1_Getting-Started/linux.md).

