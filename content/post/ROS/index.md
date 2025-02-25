---
title: "ROS"
description: "This article would walk through how to install ROS on Ubuntu."
slug: "ROS"
categories:
    - Robotics
---

# ROS

## Intro 
Q: why do we need ROS?  
A: ROS is a framework for writing robot software. It is a collection of **tools, libraries, and conventions** that aim to simplify the task of creating complex. Ros has provided a high efficient way to communicate between modules, algorithms and drivers.


## Operating System - Linux
Q: Why linux?  
A: ROS has been very mature on Linux. It is the most popular operating system for robots.
- Operating system manages the hardware and software resources of the system.
- It is the most fundamental software that runs on a computer.
- Linux is the most popular operating system for robots.
- 一般我们说的linux是指他的发行版


### Installation of Linux
1. Download the Ubuntu LTS version from the official website (An iso file).
2. Download VMware Workstation Pro from the official website.
3. Open VMware Workstation Pro and create a new virtual machine.
4. Assign >2GB RAM and >20GB storage. Choose the installed ISO file.
5. Install Ubuntu on the virtual machine.
6. Follow the instructions to install Ubuntu.
7. Download the VMware tools
   1. Open the terminal
   2. `sudo apt upgrade`
   3. `sudo apt install open-vm-tools-desktop -y`
   4. `sudo reboot`


### Simple Commands
1. `ls` - list files and directories
2. `cd` - change directory
3. `pwd` - print working directory
4. `mkdir` - make directory
5. `rm` - remove file or directory
6. `rmdir` - remove directory
7. To open the terminal, press `Ctrl + Alt + T`
8. --help - to get help on a command
9. `mv` - move files or directories
10. `cp` - copy files or directories
11. `touch` - create a file
12. `sudo` - run a command as superuser

### Install g++ and python
1. `sudo apt-get install g++`
2. To compile a C++ program, use `g++ filename.cpp -o outputname`
   

1. Python3 is already installed on Ubuntu.

### Install ROS2
1. Set Locale
   1. `sudo apt update`
   2. `sudo apt install locales`
   3. `sudo locale-gen en_US en_US.UTF-8`
   4. `sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8`
   5. `export LANG=en_US.UTF-8`
   6. `locale`
2. Enable ROS2 repository
   1. `sudo apt install software-properties-common`
   2. `sudo add-apt-repository universe`
   3. `sudo apt update && sudo apt install curl -y`
   4. `curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg`
   5. `echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release; echo "$UBUNTU_CODENAME") main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null`
3. Install development tools and ROS tools
   1. `sudo apt update && sudo apt install ros-dev-tools`
4. Install ROS2
   1. `sudo apt update && sudo apt upgrade`
   2. `sudo apt install ros-jazzy-desktop`
   3. `sudo apt install ros-jazzy-ros-base`
Common Bugs refer to [BUG FIX](https://blog.csdn.net/weixin_46445090/article/details/144812129)


