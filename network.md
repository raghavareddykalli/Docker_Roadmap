# Docker Networking

In this session, we explored Docker networking, also known as Container Networking, focused specifically using Docker as the container runtime.

## Why Docker Networking is Needed

-Containers need to communicate with each other and the host system.

-Two major scenarios:

-Scenario 1: Containers need to communicate with each other (e.g., frontend container needs backend container).

-Scenario 2: Containers must be isolated from each other for security reasons (e.g., payment container should be isolated from login container).

## How Containers Communicate with the Host

-By default, containers and hosts are on different subnets.

-Docker automatically creates a virtual ethernet interface (veth) called docker0 (a bridge network) to allow communication between the host and containers.

-Without docker0, containers would not be reachable from the host or internet.


## Docker Networking Modules

**1.Bridge Network (Default):**

![image](https://github.com/user-attachments/assets/fc22c764-404a-42f7-8cdf-b96a4588c2b6)


 -Separate container and host subnets.

 -Communication through the bridge (docker0).

**2.Host Network:**

 -Container shares the network namespace of the host.

 -No additional IP address assigned; uses host's network directly.

 -Less secure as containers have full access to host networking.
**3.Overlay Network:**

 -Used in multi-host container orchestration (e.g., Kubernetes, Docker Swarm).

 -Complex setup, will be covered later.

## Security Considerations

-Using default bridge (docker0), all containers can talk to each other, which might not be ideal for sensitive applications.

-To enhance isolation, custom bridge networks can be created.

-Containers connected to different custom bridges cannot communicate unless explicitly allowed.

-This method keeps critical containers (e.g., finance/payment services) secure from less secure ones (e.g., login services).

## Practical Demonstration:

Create and ran containers (login, logout) using the default bridge network

```docker run -d --name login nginx:latest```
 
```docker run -d --name logout nginx:latest```

Run the below command and observe the network type and ip address for both the containers (login, logout)

```docker inspect login```

Output

![image](https://github.com/user-attachments/assets/6cf1077c-aeea-4100-b1cc-bf66092f051a)


```docker inspect logout```

Output

![image](https://github.com/user-attachments/assets/ea496097-5e7b-4d2c-800f-d96f6ed78135)


In the above images we observed that both the containers have Bridge network and belong to the same subnet address.

Log in to one of the containers

```docker exec -it login /bin/bash```

Update and install the ping
 
```
apt update
apt-get install iputils-ping -y
```

Now, ping the logout ip address and observe that the user should be able to ping the address.

```ping 172.17.0.6```

Output

![image](https://github.com/user-attachments/assets/f6c50052-d730-4856-9c35-f416da529a00)


Created a custom bridge network (secure-network).

```docker network create secure-network```

Create a container and use this custom bridge network

```docker run -d --name finance --network=secure-network nginx:latest```

Again, observe the Network type and the ip address using the inspect command

```docker inspect finance```

Output

![image](https://github.com/user-attachments/assets/a7da1193-ab5a-4d15-b527-93fae35ea2b2)


We observed that the Network is secure-network and the ip address is not in the same subnet as the login and logout.

Verify that containers in different networks cannot ping each other (achieving isolation).

Creating a container (host-demo) using host networking, where no separate container IP is assigned — it shares the host’s IP.

```docker run -d --name host-demo --network=host nginx:latest```

Observe the Network type and the ip address using the inspect command
 

## Important Notes:

-Default bridge (docker0) provides basic networking but lacks isolation.

-Custom bridge networks provide logical isolation and enhance container security.

-Host networking removes container isolation and is not recommended for sensitive workloads.

-Overlay networks will be discussed more deeply in Kubernetes and Docker Swarm contexts.
