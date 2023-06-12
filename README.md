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

Prepare Design:
-----

      prep -design picorv32a
      
To prepare the design, I used the following command (designs/picorv32a contains the verilog file, .sdc file, needed PDK, and config.json that has configurations for the design that      

will overwrite the default OpenLane settings. When the design is prepared, the LEF files are merged, and a "runs" directory is created inside the picorv32a directory, and inside it a    

directory with the current date is created. Inside that directory, all folder structures required by OpenLanes are present empty, except for temp folder. temp folder has the merged LEF   

files. Note that when synthesis is performed for example a file will be created inside the results/synthesis directory. Inside the runs/<RUN_today_date> directory there is a             

config.tcl file which contains the default OpenLane configuration settings, and it is important to check whether our modifications are reflected in it):
      
Synthesis:
----

      run_synthesis
      
It will will do the all the synthesis steps (generic,mapping and optimization)
      
      
Reports and Logs:
---

all these found in RUN folder in respective process steps as shown below;

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/44169b9d-a4a0-4645-a7b5-8ffff6ecabc7)

To calculate the flop ratio, I used the following formula


Flop ratio = # of D Flipflops / Total # of cells

dfxtp_2 = 1596,

Number of cells = 9541,

Flop ratio = 1596/9541 = 0.1673 = 16.73%







      
      
