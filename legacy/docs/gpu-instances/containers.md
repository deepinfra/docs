---
title: Containers
---

## Overview

GPU Containers provide on-demand access to high-performance GPU compute resources in the cloud. With GPU Containers, you can quickly spin up containers with dedicated GPU access for machine learning training, inference, data processing, and other compute-intensive workloads.

Key features:

- **On-demand GPU access**: Launch containers with dedicated GPU resources when you need them
- **Flexible configurations**: Choose from various GPU configurations based on your performance and budget requirements
- **SSH access**: Connect directly to your containers via SSH for full control over your environment
- **Pay-per-use**: Only pay for the time your containers are running
- **Quick setup**: Get started in minutes with our streamlined creation process

GPU Containers are ideal for:

- Machine learning model training and fine-tuning
- Running inference workloads that require GPU acceleration
- Data processing and analysis tasks
- Development and testing of GPU-accelerated applications
- Prototyping and experimentation with different GPU configurations

## Usage

### Web UI

#### Starting a New Container

1. **Navigate to GPU Instances**
   - Go to your [Dashboard](/dash) and select "Instances" from the sidebar
   - Click the "New Container" button

[<img src="/docs/instances.webp" width="100%" alt="GPU Instances Web UI" />](/dash/instances)

2. **Select GPU Configuration**
   - Choose from available GPU configurations based on your needs
   - Each configuration shows:
     - GPU type, quantity and memory (e.g., "1xB100-180GB", "2xB200-180GB")
     - Hourly pricing
     - Current availability status
   - Configurations marked "Out of capacity" are temporarily unavailable

<img src="/docs/new-container-1.webp" width="75%" alt="Select GPU config" />

3. **Enter Container Details**
   - **Container Name**: Provide a descriptive name for your container
   - **SSH Key**: Paste your public SSH key for secure access
     - Use the format: `ssh-rsa AAAAB3NzaC1yc2E...`
     - This key will be added to the `ubuntu` user account

<img src="/docs/new-container-2.webp" width="75%" alt="Enter container name and SSH key" />

4. **Accept License Agreements**
   - Review and accept the NVIDIA software license agreements
   - Acknowledge the cryptocurrency mining prohibition policy
   - Click "I agree to the above" to create your container

#### Connecting to a Running Container

**Access and Connect**

- Wait for your container status to show "running" in the GPU Instances list
- Click on SSH login field
- Open your terminal and run: `ssh ubuntu@<ip-address>`
- Your container is ready to use with GPU access configured

<img src="/docs/instance-copy-ssh-login.webp" width="100%" alt="Copy SSH Login" />

#### Stopping a Container

**Terminate Container**

- Click on the container you want to stop from the instances list
- Click the "Terminate" button
- Type "confirm" in the dialog and click "Terminate"
- Warning: All container data will be permanently lost

### HTTP API

#### Starting a New Container

**Create Container**

```bash
curl -X POST https://api.deepinfra.com/v1/containers \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-container",
    "gpu_config": "8xB200-180GB",
    "container_image": "di-cont-ubuntu-torch:latest",
    "cloud_init_user_data": "#cloud-config\nusers:\n- name: ubuntu\n  shell: /bin/bash\n  sudo: '\''ALL=(ALL) NOPASSWD:ALL'\''\n  ssh_authorized_keys:\n  - ssh-rsa AAAAB3NzaC1yc2E..."
  }'
```

#### Connecting to a Running Container

**Get Container Details**

```bash
curl -X GET https://api.deepinfra.com/v1/containers/{container_id} \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN"
```

Once the container state is "running" and an IP address is assigned, connect via SSH:

```bash
ssh ubuntu@<container-ip>
```

#### Listing Containers

```bash
curl -X GET https://api.deepinfra.com/v1/containers \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN"
```

#### Terminating a Container

```bash
curl -X DELETE https://api.deepinfra.com/v1/containers/{container_id} \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN"
```

### Container States

Containers progress through several states during their lifecycle:

- **creating**: Container is being initialized
- **starting**: Container is booting up
- **running**: Container is active and accessible
- **shutting_down**: Container is being terminated
- **failed**: Container failed to start or encountered an error
- **deleted**: Container has been permanently removed
