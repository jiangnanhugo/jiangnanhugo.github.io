---
layout: "post"
title: "Install Matlab Without GUI"
date: "2015-01-22 14:48"
comments: true
---


1. download **matlab for Unix 2014** , your *license key file* is needed at first.

2. mount the iso file

```bash
mount -o loop,ro Mathworks.Matlab.R2014a.iso /media
```

3. cover the `/media/java/jar` with your licensed jar.

```bash
sudo cp Crack/install.jar /media/java/jar
```

Note: If you can't edit the file in `/media`, you can mount the iso file to your working dir. like:

```bash
cd ~
mkdir matlab_source
sudo cp /media -r matlab_source
```

4. create the filepath to be installed.

```bash
mkdir -p /opt/matlab/etc
mkdir -p /opt/matlab/2014a
```

5. install configure：copy `installer_input.txt, activate.ini,license.lic` files to `/opt/matlab/etc` directory,

```bash
sudo cp serial/license.lic /opt/matlab/etc
sudo cp activate.ini /opt/matlab/etc
sudo cp installer_input.txt /opt/matlab/etc
```

6. configure the `installer_input.txt` file

```bash
sudo vim /opt/matlab/etc/installer_input.txt //edit installer_input file
destinationFolder=/opt/matlab/2014a //where to install matlab
fileInstallationKey=12345-67890-12345-67890 // your key
agreeToLicense=yes //agree to the license
outputFile=/tmp/mathwork_install.log //installing log
mode=silent // install directly without GUI
activationPropertiesFile=/opt/matlab/etc/activate.ini // activation file
licensePath=/opt/matlab/etc/license.lic // your license file
```

```bash
sudo vim /opt/matlab/etc/activate.ini //edit activate file
isSilent=true //open the silent install mode
activateCommand=activateOffline //activation type: online or offline
licenseFile=/opt/matlab/etc/license_405329_R2014a.lic //your license file location
```
7. When configuration finished, run the `install` sctript.

```bash
sudo ./install -inputFile /opt/matlab/etc/installer_input.txt
```

8. After you finished，you need to move `crack/linux/libmwservices.so` to `/opt/matlab/2014a/bin/glnxa64/`. if not, you cannot use matlab,

```bash
sudo cp Linux/libmwservices.so /opt/matlab/2014a/bin/glnxa64/
```

9.  configure your matlab path

```bash
vim /etc/profile
export PATH=/opt/matlab/2014a/bin:$PATH
source /etc/profile
echo $PATH
```

10. setup shortkey for running matlab

```bash
alias matlab="matlab -nodesktop -nodisplay"
```

Note: you have to preinstall **openjdk** or **oracle-jdk** before.
