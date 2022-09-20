# Exploiting the Edge-Cloud Continuum with Self-distributing Systems 

In this repository you will find all the code used for both experiments reported in our paper. We make available the entire code and pre-configured docker images for results replication.

## Running the Experiments

We ran all experiments on the Google Cloud platform. We created a cluster of 5 VMs on the Google Kubernetes Engine (GKE) using the “Ubuntu with Docker” image. Each VM is equipped with 2 vCPUs and 8GB of memory. The next steps considers users have access to GKE cluster configured and running for best results replication. Otherwise, we suggest users to have access to any kubernetes-managed cluster.

We suggest users to 'git clone' the repository into the main node on the cluster (the machine where we get shell access and there is `kubectl' configured and running). We also suggest the user get a copy of the repository to the machine running on the edge. For the machine on the edge, the user should download and install the Dana programming language. For a complete tutorial on how to install the language for your system, please visit http://www.projectdana.com/dana/guide/installation. 

After setting up both the cloud and the edge follow the steps below.

### First Experiment

To replicate the first experiment on the paper, we need to run the 'rbac' script to allow any pod-to-pod communication. To execute 'rbac', go to 'scripts' folder, then go to 'kubernetes/rbac' and execute the 'naive.yaml' configuration file: 'kubectl apply -f naive.yaml'.

Next, we need to execute two instances of the 'remote-dist' process. These are the processes that will receive the replicated list component our distributor process running on the edge offlloads to the cloud. To execute the two 'remote-dist' processes go to 'scripts/kubernetes/first_experiment/remote_dist1' and execute 'kubectl apply -f deployment.yaml' and then 'kubectl apply -f service.yaml'. Then go to 'scripts/kubernetes/first_experiment/remote_dist2' and execute 'kubectl apply -f deployment2.yaml' and then 'kubectl apply -f service2.yaml'. After a while, type 'kubectl get svc' and copy both public ip address from the two 'remote-dist' processes running on the cloud and copy them to the 'distributor/data/adt/ListCPSharding.dn' code on the edge and place them into the IP1 and IP2 variables.

The next step is to compile the edge code and run the Distributor on the edge. After updating the public ip addresses on the 'ListCPSharding.dn' code on the edge, compile the entire system by running the './compile-dana.sh' script. Then, go to 'distributor' folder and execute 'dana -sp ../server Distributor.o'.

Now you should have the entire system up and running. Go to 'client' and update the Add.dn and Get.o and ClientExp1.dn 'baseUrl' to the edge machine where the 'Distributor' is running. Then, compile all client programs by typing 'dnc . -v'. Then execute 'Add.o'. This should add 8 items to the list. From this point on, we need to execute the 'ClientExp1.o' for three configurations.

#### For the Distributor on the Edge

Just run 'ClientExp1.o' by typing 'dana ClientExp1.o' on the 'client' folder. Then place all the numbers it outputs into a spreadsheet's column named "Edge".

#### For the Distributor on the Edge-Cloud

After adding 8 elements into the list by running Add.o, then on the Distributor process running on the Edge type: 'sharding'. This should trigger the Distributor to replicate its list with 8 initial elements to the two shards on the cloud. Then go to the 'client' folder and execute the client 'dana ClientExp1.o'. Place all numbers the client outputs into a spreadsheet's column named "Edge-Cloud".

#### For the Distributor swapping between Edge and Edge-Cloud

Finally, after adding 8 elements into the list by running Add.o, then execute the client 'dana ClientExp1.o'. After the client runs until the middle of its execute (at around 1500 items added to the list), go to Distributor and type 'sharding'. This will make the Distributor relocate its list into the shards running on the cloud. Then, place all numbers the client outputs into a spreadsheet's column named "Self-distributing System".

### Second Experiment

To replicate the first experiment on the paper, we need to run the 'rbac' script to allow any pod-to-pod communication. To execute 'rbac', go to 'scripts' folder, then go to 'kubernetes/rbac' and execute the 'naive.yaml' configuration file: 'kubectl apply -f naive.yaml'.

This experiment does not require the edge. After setting up the cloud and running rbac, the user should run the './start_sys.sh' located in 'scripts/kubernetes/second_experiment/start_sys.sh'. This script will create and run the Distributor container, and the service container that interacts with Kubernetes to create multiple instances of the remote-dists processes at runtime.

After executing the script, the user should gain access to the distributor container. For that, the user can list the executing containers by typing 'kubectl get pods'. Then, the user should get access to the distributor's pod's terminal by typing 'kubectl exec -it <pod-name> -- /bin/bash'. Once the user gets into the terminal, they should run the distributor's script './run.sh'.

Now the user should update the clients 'Add.dn', 'Get.dn' and 'ClientExp2.dn' url with the Distributor's ip. The user should find the distributor's ip by typing 'kubectl get svc'. Once the client scripts have been properly updated, the user should compile the client program by typing 'dnc . -v'.

The user should start adding some items to the distributor by executing Add.o by typing 'dana Add.o'. Then, the user should execute the ClientExp2.o by typing 'dana ClientExp2.o'. The output generated by the client program should be placed on a spreadsheets. This process should be repeated for the local configuration, for 2 shards, 4 shards and 8 shards. To go from local to x shards, just go to the Distributor container and type 'sharding', the Distributor will then ask for a number of pods type 2 for 2 shards, 4 for 4 shards and so on.


