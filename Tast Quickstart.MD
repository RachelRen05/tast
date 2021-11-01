# Introduction
Tast is an integration-testing system for Chrome OS. Its focus is on maintainability, speed, and ease of interpreting and reproducing test results. It supports building, deploying, and running tests. It doesn't implement other functionality like managing labs of devices used for testing, scheduling tests, or storing test results.
# Tast Quickstart
  ## Prerequisites
   ### 1.Install [Chrome OS chroot](https://www.chromium.org/chromium-os/quick-start-guide)
   **Note:Do not run any of the commands listed in this document as root – the commands themselves will run sudo to get root access when needed**
   #### Prerequisites
   * Ubuntu Linux (version == 16.04 - Xenial)
      * This is the only officially support distro, but building Chromium OS should work fine on any x86_64 Linux distro running a 2.6.16+ kernel
   * a 64-bit system for performing the build
   * an account with sudo access
   
   **1. Install the git and subversion revision control systems, the curl download helper, and lvm tools**  
   
    16.04 sudo apt-get install git-core gitk git-gui subversion curl lvm2 thin-provisioning-tools python-pkg-resources python-virtualenv python-oauth2client  
    20.04 sudo apt-get install git-core gitk git-gui curl lvm2 thin-provisioning-tools \
     python3-pkg-resources python3-virtualenv python3-oauth2client xz-utils \
     python3.6
    20.04 default python3
    >error:/usr/bin/env: ‘python’: No such file or directory
    >solution:sudo apt-get install python-is-python3
    
   **2. Install [depot tools](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up)**  
   * GET DEPOT TOOLS
    * Clone the depot_tools repository  

      `mkdir ~/chromiumos`
      `cd ~/chromiumos`
      `git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git`
      
   * Add depot_tools to the front of your PATH or add this to ~/.bashrc
  
     `export PATH=/home/$hostname/chromiumos/depot_tools:$PATH`  
     
   **3.  [tweak your sudoers configuration](http://www.chromium.org/chromium-os/tips-and-tricks-for-chromium-os-developers#TOC-Making-sudo-a-little-more-permissive). This is required for using cros_sdk**  
   
   >To set up the Chrome OS build environment, you should turn off the tty_tickets option for sudo, because it is not compatible with  cros_sdk.  
   
     cd /tmp
     cat > ./sudo_editor <<EOF
     #!/bin/sh
     echo Defaults \!tty_tickets > \$1          # Entering your password in one shell affects all shells
     echo Defaults timestamp_timeout=180 >> \$1 # Time between re-requesting your password, in minutes
     EOF
     chmod +x ./sudo_editor
     sudo EDITOR=./sudo_editor visudo -f /etc/sudoers.d/relax_requirements  
     
   >depending on your Linux distribution's configuration, sudoers.d may not be the correct directory, you may check /etc/sudoers for an #includedir directive to see what the actual directory is.
   #### Get the Source
   **Note:when useing git for the first time,you need to set your account's default identity.**
   
    cd ~/chromiumos
    repo init -u https://chromium.googlesource.com/chromiumos/manifest.git
    repo sync -j4
    >error log:SyntaxError: invalid syntax
    >solution:
    curl https://storage.googleapis.com/git-repo-downloads/repo-1 > ~/bin/repo   #Download last version of repo
    chmod a+x ~/bin/repo  #Change right to make it executable
    python3 ~/bin/repo init -u https://chromium.googlesource.com/chromiumos/manifest.git
  #### Build Chromium OS
    cd ~/chromiumos
    cros_sdk
  
     
     
     
     
     
