# Creating-a-container-image-and-uploading-to-azure-through-azure-container-registry
This documentation explains how to create a container image and upload it to Azure's Container Registry (ACR), covering setup, image creation, and the upload process.


## Introduction
This guide walks through the essentials of deploying a Docker container image to Azure Container Registry. Whether you're new to containerization or experienced with Docker, this step-by-step approach will clarify the deployment workflow.


## Steps

- Provision a new virtual machine (VM) on Azure.
- Install Docker on the VM.
- Set up an Azure Container Registry (ACR) in the Azure portal.
- Transfer application code to the VM using FileZilla.
- Build the Docker image.
- Tag and push the image to ACR.
- Verify the image in ACR.


### Step 1: Provision a New Virtual Machine (VM)
1. Go to the [Azure portal](https://portal.azure.com) and navigate to **Virtual Machines**.
2. Select **Create** and configure the VM with your preferred OS (e.g., Ubuntu or Windows Server) we will be using a linux vm
3. Complete the setup, and then connect to your VM using SSH or RDP.
<img width="1680" alt="1" src="https://github.com/user-attachments/assets/336ae088-1d06-4e83-a87d-273cace5b947">


### Step 2: Install Docker on the VM
Follow these steps to securely install Docker on your Ubuntu VM:

1. Update the package list:
    ```bash
    sudo apt-get update
    ```

2. Install necessary certificates and curl:
    ```bash
    sudo apt-get install -y ca-certificates curl
    ```

3. Create a directory for storing Docker's GPG key:
    ```bash
    sudo install -m 0755 -d /etc/apt/keyrings
    ```

4. Download Docker's official GPG key:
    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    ```

5. Add Docker's repository to the apt sources list:
    ```bash
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

6. Update package information again and install Docker:
    ```bash
    sudo apt-get update
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

7. Verify Docker installation:
    ```bash
    docker --version
    ```


### Step 3: Set Up Azure Container Registry (ACR)
1. In the Azure portal, navigate to **Container Registries** and select **Create**.
2. Configure the registry by selecting a **Resource Group**, **Registry Name**, and **Region**.
3. Choose an appropriate **SKU** and complete the setup.
<img width="1680" alt="2" src="https://github.com/user-attachments/assets/efa26539-2192-4940-af7a-0edbe81320de">

### Step 4: Transfer PHP Code from VSC to the VM Using FileZilla
Since i already prepared the PHP files in Visual Studio Code. ow i can transfer them to my VM, but firstly make sure you have the dockerfile in your vsc before transferring
<img width="1680" alt="Screenshot 2024-11-14 at 09 40 08" src="https://github.com/user-attachments/assets/d15ecf60-859c-4978-8eca-2fea5350b29d">


1. Open **FileZilla** and connect to your VM by entering the IP address, username, and password (or SSH key) in the connection settings.
2. Locate the PHP project directory on your local machine (from VSC) and the corresponding destination directory on the VM.
3. Transfer the files to the VM, e.g., `/home/Cloudtest/phpapp`.
<img width="1680" alt="Screenshot 2024-11-11 at 08 36 28" src="https://github.com/user-attachments/assets/5520b75d-97c0-4f5b-9cee-3655077bd559">



### Step 5: Build the Docker Image for the PHP Application
In this step, you will customize the Docker image for your application. You will use a **PHP 8.2 with Apache** base image, but this step can easily be adapted for other environments like **Java**, **Python**, or **Node.js**.

After transferring the PHP files, you are now ready to build the Docker image:

1. SSH into your VM and navigate to the directory where the PHP code was transferred (e.g., `/home/Cloudtest/phpapp`).
2. Build the Docker image:
    ```bash
    docker build -t phpapp .
    ```
3. Let's see if the container is working as expected, run the container with the following command, which will expose the application on port 80:

    ```bash
    docker run -d -p 80:80 phpapp
    ```
<img width="1680" alt="Screenshot 2024-11-11 at 08 54 20" src="https://github.com/user-attachments/assets/47e0d3a7-a4c9-4cb4-a118-b04bebf770be">

4. After running the container, open a browser and navigate to your VM's public IP address. You should be able to see your application running.

### Step 6: Tag and Push the Image to ACR
1. Go to the azure portal and go to the registry created and go to the access key and select the "Admin user" option to see your Password. You will need to note the registry name, login server and username details
<img width="1680" alt="Screenshot 2024-11-11 at 08 20 52" src="https://github.com/user-attachments/assets/9704823e-558e-42e6-ae33-5868ab863165">

2. login to the server on from your ssh terminal and run : sudo docker login (login server) -u (username) -p (password)
<img width="1680" alt="Screenshot 2024-11-11 at 09 05 16" src="https://github.com/user-attachments/assets/b13464ad-487a-489d-b3de-33b2a1658233">

3.  Tag the image with your ACR registry's login server: sudo docker tag (docke rname) (name of server)/(image name)

4. Push the image to ACR: sudo docker push
<img width="1680" alt="Screenshot 2024-11-11 at 09 11 33" src="https://github.com/user-attachments/assets/92f26811-9b23-48ea-8233-ad38dc3946d9">

5. chcek the ACR dashborad to see your container
<img width="1680" alt="Screenshot 2024-11-11 at 09 11 55" src="https://github.com/user-attachments/assets/56988c5f-0186-4680-8005-7fd816482192">


## Conclusion

By following the steps outlined in this guide, you have successfully:
- Provisioned a new Azure VM.
- Installed Docker and configured it for use.
- Built a custom Docker image for your application.
- Uploaded the image to Azure Container Registry (ACR).
- Run the container and tested it by exposing it on port 80.

Your application is now containerized and ready for deployment in the cloud, ensuring a scalable and reliable infrastructure. You can further expand this setup by deploying your container to Azure Container Instances, Azure Kubernetes Service, or other Azure services for production use.


## Additional Resources

For further information and in-depth details, refer to the official Docker and Azure documentation:

- [Docker Documentation](https://docs.docker.com/)
- [Azure Container Registry Documentation](https://learn.microsoft.com/en-us/azure/container-registry/)
- [Azure Container Apps Documentation](https://learn.microsoft.com/en-us/azure/container-apps/)
- [Udemy course on using containers in azure](https://www.udemy.com/course/microsoft-certified-azure-administrator/?couponCode=KEEPLEARNING)

Thank you for following this guide! Feel free to explore more and customize your containerized application as per your needs.





































