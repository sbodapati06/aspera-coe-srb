# Aspera Console Dashboard

This lab will guide you how to use Aspera Console to visualize the file transfers you did in lab1. <br>

<br>

Logon to the VDI Desktop! <br>

Open Google Chrome or Firefox browser.<br>

Copy / paste Aspera Console URL https://localhost/aspera/console <br>

![](./images/aspera-console-login.png)

### Create a New Managed Cluster

You will add, Aspera High Speed Transfer instance deployed in the Cloud Pak for Integration.<br>

Click on "Nodes" > New Managed Cluster <br>

![](./images/new-managed-cluster.png)

![](./images/aspera-console-nodes.png)

Click on "edit" icon <br>
This will open Node Maintenance page. Here, you will be able to add your IBM Cloud Object Storage Access Key, and Secret. <br>
![](./images/add-access-key.png)
Click "Add Access Key" <br>

Enter the Access Key, and the Secret of IBM Cloud Object Storage registered with Aspera CP4I HSTS instance. <br>
![](./images/enter-access-key.png)


Test the Credentials! <br>
![](./images/test-access-key.png)

Make sure it's tested successfully! Click <OK> <br>
<br>

### Aspera desktop client - Transfer some files 

Use the Aspera Desktop Client and transfer some files or directory. <br>

### Aspera Console - view transfers
Click on the Dashboard, and watch the files being transferred. <br>
Click on each transfer file to see additional details of the file transfer. <br>

!!! Congratulations - You have finished Aspera Console lab. You now able to monitor the file transfers and history. !!! <br> 


