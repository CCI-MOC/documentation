## Instructions for Creating a New dashboard by taking (graphs,Table,list etc) from other dashboards in Grafana.
###Overview
Grafana is a visualisation tool, which takes input from any data source such as influxdb, to provide a neat visualization and analyse those metrics. In grafana we often create a dashboard to collect metrics (cpu usage, memory and disk usage) for our compute nodes. In this wiki we will create a new dashboard,see how to copy the graphs from other dashboards to the newly created dashboard and delete the created dashboard. 

###Create a dashboard.
* Initially we need to connect to the grafana production server (http://129.10.5.55:3033/) and create a new dashboard (click new) and save it with a name ex:vinay.  
![](https://dl.dropboxusercontent.com/u/6646746/newdashboard.png)

###Copy .json from other dashboard.
* To copy the graphs from other dashboards we can just go to the required dashboard from the drop down menu to see the list of all the dashboards (ex:MOC-NEU Network dasboard) and copy that panel .json file (here i just took compute node 25,26 panel.json files). Click on the graph to see the panel options.
![](https://dl.dropboxusercontent.com/u/6646746/graphfromotherdashboard.png)

###Paste .json to your dashboard.
* Then we go back to the newly created created dashboard (in this case vinay) to update the panel .json file and save it.
![](https://dl.dropboxusercontent.com/u/6646746/copyfiletoyourdashboard.png)

###Delete the Created dashboard.
* Finally after pasting the graphs from other dashboards if you want to delete the created dashboard then, click on settings to delete it.
![](https://dl.dropboxusercontent.com/u/6646746/deletethedashboard.png)

  

