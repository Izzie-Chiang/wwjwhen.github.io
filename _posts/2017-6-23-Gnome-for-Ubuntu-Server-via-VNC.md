---
layout: post
title: Gnome for Ubuntu Server via VNC in Local Windows
---

<p class="meta">July 23 2017 - Beijing</p>

To login the Ubuntu Server with Graphical Interface. Many deep learning codes are written for graphical server,
thus I try to login the server with graphical interface in local WINDOWS operating system.

# PART ONE : Install gnome for Ubuntu Server


~~~
sudo apt-get install xinit
~~~



The gdm is short for gnome desktop manager, and other kinds of manager may do no harm, such as *lightdm*, *kdm*

~~~
sudo apt-get install gdm
~~~


Pay attention to the step of *ubuntu-desktop* if you are to install gnome, do use *ubuntu-desktop* 
instead of *[k|l|x]ubuntu-desktop* which is for kde or something else

~~~
sudo apt-get install ubuntu-desktop
~~~


# PART TWO ： VNC install and configuration

The only but most important thing in this part is the *xstartup* file  

[reference](http://onkea.com/ubuntu-vnc-grey-screen/)

~~~
def
export XKL_XMODMAP_DISABLE=1
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

gnome-panel &
gnome-settings-daemon &
metacity &
nautilus &
gnome-terminal &
~~~


But do not forget to install the dependencies first with apt/apt-get: gnome-panel, gnome-settings-daemon, matacity, nautilus, gnome-terminal

~~~
sudo apt-get install gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal
~~~
{: .language-shell}