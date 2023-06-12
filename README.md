# Openlane




      sudo apt-get update
      sudo apt-get upgrade
      sudo apt install -y build-essential python3 python3-venv python3-pip make git

      sudo apt install apt-transport-https ca-certificates curl software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

      echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee      
      /etc/apt/sources.list.d/docker.list > /dev/null

      sudo apt update

      sudo apt install docker-ce docker-ce-cli containerd.io

      sudo docker run hello-world

      sudo groupadd docker
      sudo usermod -aG docker $USER
      sudo reboot 

# After reboot
      
      docker run hello-world

Check dependencies
----
      
      git --version
      
      docker --version
     
      python3 --version
      
      python3 -m pip --version
      
      make --version
      
      python3 -m venv -h

Below steps installs PDKs and Tools
---
      
      cd $HOME
      
      git clone https://github.com/The-OpenROAD-Project/OpenLane
      
      cd OpenLane
      
      make
      
      make test
      
Invoke OpenLane
----
      make mount
      ./flow.tcl -interactive
      package require openlane 0.9

Without interactive switch the tool is in automate mode.      
      
      
      
      
      
      
      
