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
      
Invoke OpenLane ( PICORV32 RTL TO GDSII)
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
      
It will do the all the synthesis steps (generic,mapping and optimization)
      
      
Reports and Logs:
---

all these found in RUN folder in respective process steps as shown below;

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/8589f75d-1273-45ab-96e3-bdbef66e8ed8)


![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/b86016e0-c6e6-4a64-8752-411e537977da)


To calculate the flop ratio after tech map and opt


Flop ratio = # of D Flipflops / Total # of cells

dfxtp_2 = 1596,

Number of cells = 9541,

Flop ratio = 1596/9541 = 0.1673 = 16.73%


STA 
----
![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/8a4c4e81-76da-4aaa-9ebd-c987a31f7930)

min(reg2reg)

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/3fd0e10b-718b-43ec-bf27-cc5cb38f73cb)

max(reg2reg)

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/51332211-2966-4a1f-ac6d-625d662e39e7)
      
![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/50576b87-dead-4de4-90de-22414c28136e)     

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/c7a60547-ca1d-4063-8390-b00bc55d5334)


Power:
----

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/0c8608e9-f54b-495a-a91c-ab0856e2cc70)

Chip-floor Planning:
----
1)Defining the hieght and width of the core 
----

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/f2bbf23c-fa27-4709-b6dd-d4dcc8c7e13c)


2)Define the location of prepalce cells:
----

*Some cells in the design are repeatedly using so we need fix the location so PNR tool will not optimize or not alert the location

*ex:memory,clock gating cell,comparator and mux.

*arrangement of these IP in  core is called floorplanning

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/0b95ea2e-51ef-4a03-b632-f3df29d3764c)

*Macros(IP) near the IOs

*Adding the decoupling cap to provide the to boost the macro

3)Power Planning:
----

In order to distrubute the power required range to all cells present in the core by PG mesh shown below

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/1e9df759-13a9-4e4e-aa91-d808a308e81d)

4)Pin-Placement
---
![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/bad972db-064c-40b6-b435-a74bdad8e846)

floor-plan(PICORV32)
----

Run the following commond for floorplan. The results can be found in OpenLane/designs//RUN_*/runs):

run_floorplan

default openline system setting:

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/5a7f494e-619e-4e40-9728-cc5c125abc01)


Note that some of the floorplan switches (can be included with the command above) are FP_CORE_UTIL (floorplan core utilization), FP_ASPECT_RATIO (floorplan aspect ratio), FP_CORE_MARGIN (Core to die margin area), FP_IO_MODE (defines pin configurations: 1 = equidistant and 0 = not equidistant), FP_CORE_VMETAL (vertical metal layer), and FP_CORE_HMETAL (horizontal metal layer). The default values of these are defined in OpenLane/configuration/floorplan.tcl. In order to overwite these, we can define those switches in OpenLane/designs//config.json. Note that in OpenLane, horizontal and vertical metal are one value added to the value we specify.

To view the layout of the floorplan in magic, I used the command below in the results/floorplan directory (note that in my case the pdk was previously downloaded on my desktop in the open_pdks directory):










