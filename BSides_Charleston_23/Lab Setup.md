# BSides_Charleston_Malicious_Kubernetes_Workshop


Welcome to the malicious Kubernetes training, the following destructions will help you set up a lab for the BSides Charleston Workshop. The K8s lab component is predominantly built w/ the assistance of `kind` - a tool for rapid prototyping in k8s, and `ansible` for orcestration. It’s not suitable for production usage,but it builds fast and reliably given our time constraints. It’ll give us an environment that will teach us the fundamental components. 

If you have questions about running the lab on something other than GCP, please see the [FAQ](#FAQ) at the end of the setup instructions.

**Time:** 5-10 mins including spinning dials

**1. Create a new VM instance.** Select a e2-standard-2 for this session, so that you can run a large number of nodes and pods. 
This should come out to around the following cost per month: $51.92, or $0.07 cents an hour from your free credits you get by registering a new email address. 
        Goto: https://cloud.google.com/free 
        Create an account, or log in. 
![compute_engine](https://user-images.githubusercontent.com/32903188/182159860-24dde591-f87f-4e70-8df1-be6e27455108.png)

**1a. Enable the compute API:** 

![compute_api](https://user-images.githubusercontent.com/32903188/182159962-e40dd9f9-d7d1-4410-957a-e03ca309e653.png)

![image](https://user-images.githubusercontent.com/32903188/182160064-ae2c5d3e-baaf-48a5-85ba-8f01c88b511f.png)

**1b. Configure the machine:** 

![machine_config](https://github.com/lockfale/Malicious_Containers_Workshop/assets/32903188/695c07ec-1c25-4d71-a524-d23063e5d764) 




**1c. Configure the boot disk and image size.** 
![boot_disk](https://user-images.githubusercontent.com/32903188/182160383-ebeb8930-ab12-4a36-8595-ba71622ce26c.png)


**2. Connect to the instance over SSH.** Gcloud has a nice web browser based SSH that will work fine for the lab: 

![ssh_connect](https://user-images.githubusercontent.com/32903188/182160599-ac61a507-3f02-4a3f-865f-39416aed9e31.png)

**3. Run the following setup commands:** 

```
sudo apt update && sudo apt install -y python3-pip
```

```
sudo pip install ansible
```

```
curl -LO https://raw.githubusercontent.com/lockfale/Malicious_Containers_Workshop/main/BSides_Charleston_23/lab-ansible-setup.yml
```

```
ansible-playbook lab-ansible-setup.yml
```
  
**4. Start a new terminal** - then `exit` the existing one. 
  
  ```
  kind version
  ``` 
  
  ```
  kind create cluster --image=kindest/node:v1.27.3
  ``` 
  
  
**Note:** by default it’ll pull an old version of Kubernetes so image argument is added to specify newer version of 
kubernetes

**Note:** kubectl may be one minor version ahead of our Kubernetes install but that shouldn't effect our labs
```
kubectl version
```

```
docker ps
```

```
kind get clusters
``` 


```
kind delete cluster
``` 


**5. Build Lab Cluster** 

 The ansible playbook downloaded a file for you: 
 ```
 less kind-lab-config.yaml
 ``` 

 Note that the YAML file is annotated, so you can understand how it works. 

 Press &darr; and &uarr; to scroll through file, and `q` to exit

 Run the following command to setup the kind cluster
 
 ```
 kind create cluster --config=kind-lab-config.yaml
 ``` 
 
 This may take a couple of minutes, go get a coffee or something. 
 
 **6. Confirm lab is operational**
 
 ```
 kubectl cluster-info --context kind-lab
 ```

```
kubectl get nodes
```

You should see 2 worker nodes and a control plane running.
 
![get_nodes](https://user-images.githubusercontent.com/32903188/182169551-f2564d91-33e9-4cc6-b4f2-ba9f9cd62834.png)

**7. Setup ngrok account**

Sign up for a free ngrok account on https://ngrok.com, you can Oauth through the same Google Account if you want to keep it simple.

You'll likely get redirected to a Setup & Installation page under Getting Started once you're signed in.

Run the command under "Connect your account" in the terminal on your VM. You won't need to run the `unzip` command because we put it on with ansible already.
![image](https://github.com/lockfale/Malicious_Containers_Workshop/assets/32903188/637031ab-73d2-4d43-9535-84e543afd023)


Example: `ngrok config add-authtoken <authtoken>`

Ngrok will be used for some exercises, so having this step completed ahead of time will be useful.

You can check if ngrok is working with `ngrok http 80`. 

**8. Do not turn off the VM after setup whilst waiting for the workshop. Otherwise you'll lose all the above (ephemeral storage).** 

   

**Troubleshooting note:**

If you have an empty .kubeconfig file - your session was probably duplicated and not restarted after you installed kind. - make sure to exit your session and start a new one before continuing after the kind install.


## FAQ

**Can I just use my own local VM?**

We discourage the use of local VMs for several reasons, one is platform agnosticism. We want everyone to be able to participate whether they use Windows, OS X, Linux, x86, ARM, etc.  We don't have time to troubleshoot everyone's individual setup, hypervisor, or have to care about what hardware/OS they bring beyond it can run a modern web browser. 

However, the biggest reason is the bandwidth usage downloading images during the exercises would be untenable over the local wifi, especially when the bandwidth is shared across multiple students in our workshop and others. What takes less than 30 seconds in a datacenter may take 10-15 minutes downloading to a local machine and depending on QoS may cause problems for other students. Most wifi can easily support multiple SSH/web sessions to remote machines however. 

After the workshop, if you want to shutdown/delete the GCP instances and run the lab setup on a local VM for home/personal use, you're more than welcome to.

**Can I just use my own favorite cloud provider?**

If you have a particular dislike for GCP or getting the free credit is an issue for you and you want to use your own cloud provider of choice for some reason, do so at your own risk. While the lab setup *should* work on any VM running Ubuntu 20.04-22.04, we have not tested this extensively and don't think its particularly worth the trouble. 

As with the local VM, if you want to migrate the lab to another provider after the workshop, you're more than welcome to, but we feel during the workshop its easier if everyone sticks to the same setup to eliminate unexpected behavior.
