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

1)Chip-floor Planning:
----
Defining the hieght and width of the core 
----

Utilization factor = area occufied by the netlist cells/total core area

Aspect ratio = height/width 


Define the location of prepalce cells:
----

*Some cells in the design are repeatedly using so we need fix the location so PNR tool will not optimize or not alert the location

*ex:memory,clock gating cell,comparator and mux.

*arrangement of these IP in  core is called floorplanning

*Macros(IP) near the IOs

*Adding the decoupling cap to provide the to boost the macro

Power Planning:
----

In order to distrubute the power required range to all cells present in the core by PG mesh shown below

Pin-Placement
---
Pins are placed the boundry in equidistance manner (system default)

Input pins are left and outputs are right.

default openline system setting:

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/5a7f494e-619e-4e40-9728-cc5c125abc01)

floor-plan(PICORV32)
----

Run the following commond for floorplan. The results can be found in OpenLane/designs//RUN_*/runs):

      run_floorplan

default openline system setting:


Note that some of the floorplan switches (can be included with the command above) are FP_CORE_UTIL (floorplan core utilization), FP_ASPECT_RATIO (floorplan aspect ratio), FP_CORE_MARGIN (Core to die margin area), FP_IO_MODE (defines pin configurations: 1 = equidistant and 0 = not equidistant), FP_CORE_VMETAL (vertical metal layer), and FP_CORE_HMETAL (horizontal metal layer). The default values of these are defined in OpenLane/configuration/floorplan.tcl. In order to overwite these, we can define those switches in OpenLane/designs//config.json. Note that in OpenLane, horizontal and vertical metal are one value added to the value we specify.

To view the layout of the floorplan in magic, I used the command below in the results/floorplan directory (note that in my case the pdk was previously downloaded on my desktop in the open_pdks directory):

      magic -T /home/sangamanath/Desktop/open_pdks/sky130/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.min.lef def read picorv32.def &
     
![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/52bc73a0-c110-4e49-8256-923efebae8b3)


After zooming in (left click, right click, z), below is the obtained screenshots (note that when we highlight (s after positioning the cursor), we can type "what" in the tkcon window and 

it will provide the layer of the highlighted object. The standard cells can be found on left bottom corner of the layout, as floorplan does not place those):

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/81471070-06b6-4c20-a04d-3e62944669d3)



2)Placement:
-----

Placement of the standard cells (global and Detailed)

Based on wire lenght and capacitance to insert the repeater(opt placement)

CHECK_LIGALITY:

row_check

site_check

power_check

aced_check

edge_check

overlap_check

![image](https://github.com/sangamanathpuncham/Openlane/assets/132802184/fcd6881f-f60b-4383-9859-98bd6ce8eb38)




