---
title: Custom Topology
author: NVIDIA
weight: 40
product: NVIDIA Air
---

NVIDIA Air offers multiple means of creating custom topologies and new simulations.

To access custom topologies, click the **Build Your Own** card on the Create a Simulation page:

  {{<img src="/images/guides/nvidia-air/Catalog.png" width="800px">}}

You can also click the {{<exlink url="https://air.nvidia.com/build" text="air.nvidia.com/build">}} link.



## The Drag-and-Drop Topology Builder

One way to create fully custom simulations is with the built-in topology builder. It provides a drag-and-drop editor to design any custom network.

To get started, perform the following instructions:

1. Click the **Create Simulation** button. You can also click **Workspace > New Simulation**.
2. Give your simulation a **Name**.
3. Select **Blank Canvas** as the **Type**.
4. Optionally, assign an Organization to the sim. Read more about them in {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/nvidia-air/Organizations/" text="Organizations">}}. 
5. Optionally, add a **ZTP script** to the simulation. You can read more about them in {{<link url="#ZTP-Scripts" text="ZTP Scripts">}}.
     - Toggle on the **Apply ZTP Template** button.
     - Enter your ZTP script.
     - A default script is prefilled to help you get started. 
6. Click **Create**.

#### ZTP Scripts
You can add an optional **ZTP script** to the simulation when creating a new one. The ZTP script will be copied directly as-is into the `oob-mgmt-server` of the simulation. Any node making a ZTP request on the OOB management network has access to this ZTP script through a DHCP server and web server running on the `oob-mgmt-server`.

A default script is prefilled to help you get started. It implements some common ZTP features on Cumulus Linux, like changing the default password or downloading SSH keys. You can modify it to your needs if you like.

### Manage Nodes

Drag and drop servers and switches from the **System Palette** on the right into the workspace.

You can choose your hardware model based on available NVIDIA Spectrum (SNXXXX) switches. This does not affect the simulation but acts as a macro to pre-populate the number of ports per switch model. You don’t need to use each port.

Click on a node to view its **Node Properties**.

- **Name**: Node hostname.
- **Operating System**: OS automatically installed onto the node. No need to boot into ONIE or install it yourself.
- **CPUs**: Number of CPUs. Default 1-2 GB depending on Spectrum switch selected.
- **Memory (GB)**: Amount of RAM. Default 2 GB.
- **Storage (GB)**: Amount of hard disk space. Default 10 GB.
- **Connectors**: Choose an available port to directly connect to any port on another node.

There are also various **Advanced Options**, such as enabling **UEFI Secure Boot**.

When you are done creating your topology, click **Workspace > Start Simulation** to start the sim. **You cannot add, remove or edit nodes once the sim is started for the first time.**

### OOB Management Network

On the **System Palette**, there is an option to toggle **Enable OOB**. Toggling this setting enables the out-of-band management network 

This setting creates an OOB network for you that connects all nodes with each other. It also adds an `oob-mgmt-switch` and `oob-mgmt-server` to your simulation. When you {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/nvidia-air/Quick-Start/#services" text="enable SSH">}} in your sim, you will SSH into the oob-mgmt-server, making this node an ideal start point for configuration. Air handles the configuration automatically for you.

You can manually add more `oob-mgmt-switches` and `oob-mgmt-servers` to your simulation if you need. But the **Enable OOB** toggle must be enabled to use the OOB network.


## Custom Topologies with DOT Files
Air supports creating custom topologies using DOT files. 

DOT files are the filetype used with the open-source graph visualization software Graphviz. They are simple, text-based files and allow for quick and easy customization. 

You can upload DOT files directly into Air to generate a topology. This allows you to easily share and create copies of a topology and save the topology somewhere in a reusable file. You can modify them in any text editor, like notepad or VS Code.

Here is an example of a simple topology DOT file with 1 spine, 2 leaves and 2 servers connected to each leaf.
```
graph "Demo" {
  "spine01" [ function="spine" memory="4096" os="sonic-vs-202305" cpu="2" ]
  "leaf01" [ function="leaf" memory="4096" os="sonic-vs-202305" cpu="2" nic_model="e1000"]
  "leaf02" [ function="leaf" memory="4096" os="sonic-vs-202305" cpu="2" secureboot="true"]
  "server01" [ function="server" memory="2048" os="generic/ubuntu2404" cpu="2"]
  "server02" [ function="server" memory="2048" os="generic/ubuntu2204" cpu="3" storage="20"]
    "leaf01":"eth1" -- "server01":"eth1"
    "leaf02":"eth1" -- "server02":"eth1"
    "leaf01":"eth2" -- "spine01":"eth1"
    "spine01":"eth2" -- "leaf02":"eth2"
}
```

### DOT Syntax
DOT files use the `.dot` file extention. They define nodes, attributes and connections for generating a topology of a networks. They are inherently 
This list cannot be exhaustive because users can define new passthrough attributes and use them with custom templates.

Below are some common use cases for customizing your topology with DOT files. Air is not limited to accepting only these options. Contact NVIDIA support for more information.

#### Operating System
You can set the OS of the node with the `os` option: 
```"server" [os="cumulus-vx-5.10.1"]```

For a list of available operating systems, view the **Operating System** dropdown in the **Node Properties** when using the {{<link url="#The-Drag-and-Drop-Topology-Builder" text="drag-and-drop editor">}}.

#### Disk Space
By default, nodes in Air have 10GB of hard disk space. You can give more with the `storage` option, in GB:

```"server" [os="generic/ubuntu2404" storage="20"]```

If the node does not recognize the increase in storage, you can perform the following commands in the node to extend the partition and resize the fileystem: 
```
sudo growpart /dev/vda 1
sudo resize2fs /dev/vda1
```

Verify the change was applied:

```
df -h | grep vda1
/dev/vda1        20G  2.1G   18G  11% /
```

#### CPU
You can customize the CPU with the `cpu` option:
```"server" [os="generic/ubuntu2404" cpu="4"]```


#### Creating Connections
You can create port connections by defining the node and its port with another.
```
"leaf01":"swp49" -- "leaf02":"swp49"
"leaf01":"swp50" -- "leaf02":"swp50"
```

#### Memory
You can customize the RAM with the `memory` option, in MB: 
```"server" [os="generic/ubuntu2404"  memory="2048"]```

### Examples
Labs in the {{<exlink url="https://air.nvidia.com/demos" text="Demo Marketplace">} are maintained with external GitLab repositories. Here you can find the `topology.dot` file used to build the lab to reference from.

To access them, click on the **Documentation** button on any lab in the Demo Marketplace. It will lead you to the GitLab repo for the lab. You may have to explore the GitLab a bit to find the `topology.dot` file. 

### Uploading a DOT
To upload a DOT file into Air:
1.	Click the **Create Simulation** button. You can also click **Workspace > New Simulation**. 
2.	Give your simulation a **Name**.
3.	Select **DOT** as the **Type**.
4.	Drag or select your DOT file to upload.
5. Optionally, assign an Organization to the sim. Read more about them in {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/nvidia-air/Organizations/" text="Organizations">}}. 
6. Optionally, add a **ZTP script** to the simulation. You can read more about them in {{<link url="#ZTP-Scripts" text="ZTP Scripts">}}.
     - Toggle on the **Apply ZTP Template** button.
     - Enter your ZTP script.
     - A default script is prefilled to help you get started. 
7. Click **Create**.

Air will build a custom topology based on the DOT file. 

### Create a Custom Topology from the Production Network

This section describes how to create a simulation based on an existing production deployment.

<!-- vale off -->
### Gather cl-support from the Production Network
<!-- vale on -->

Use {{<exlink url="https://gitlab.com/cumulus-consulting/features/cl_support_ansible" text="these playbooks">}} to gather the `cl-support` script output. The `ReadMe` in the repository provides instructions on how to run the playbook to gather the `cl-support` output.

<!-- vale off -->
### Create topology.dot from the Production Network
<!-- vale on -->

After you obtain the `cl-support` output, you can create a `topology.dot` file with {{<exlink url="https://gitlab.com/cumulus-consulting/features/cl_support_lldp_parser" text="this script">}}. You can run the script using `python3`. Here is sample output:

```
$ python3 cl_support_lldp_parser.py
Extracting: /home/cumulus/cl_support_lldp_parser/cl_support_leaf01_20210721_164553.txz
Extracting: /home/cumulus/cl_support_lldp_parser/cl_support_spine02_20210721_164553.txz
Extracting: /home/cumulus/cl_support_lldp_parser/cl_support_leaf02_20210721_164553.txz
Extracting: /home/cumulus/cl_support_lldp_parser/cl_support_spine01_20210721_084129.txz
folder is: /home/cumulus/cl_support_lldp_parser/cl_support_leaf01_20210721_164553
leaf01
    leaf01:eth0 -- oob-mgmt-switch:swp2
    leaf01:swp31 -- spine01:swp1
    leaf01:swp32 -- spine02:swp1
folder is: /home/cumulus/cl_support_lldp_parser/cl_support_spine02_20210721_164553
spine02
    spine02:eth0 -- oob-mgmt-switch:swp6
    spine02:swp1 -- leaf01:swp32
    spine02:swp2 -- leaf02:swp32
folder is: /home/cumulus/cl_support_lldp_parser/cl_support_leaf02_20210721_164553
leaf02
    leaf02:eth0 -- oob-mgmt-switch:swp4
    leaf02:swp31 -- spine01:swp2
    leaf02:swp32 -- spine02:swp2
folder is: /home/cumulus/cl_support_lldp_parser/cl_support_spine01_20210721_084129
spine01
    spine01:eth0 -- oob-mgmt-switch:swp5
    spine01:swp1 -- leaf01:swp31
    spine01:swp2 -- leaf02:swp31
DOTFILE: cl_support_lldp_parser.dot
```

The command writes the output to `cl_support_lldp_parser.dot`. You need to manually edit this file to define the node versions and clean up any superfluous configurations:

```
$ cat cl_support_lldp_parser.dot
graph dc1 {
"leaf01" [function="leaf" ]
"oob-mgmt-switch" [function="leaf" ]
"spine01" [function="leaf" ]
"spine02" [function="leaf" ]
"leaf02" [function="leaf" ]
    "leaf01":"eth0" -- "oob-mgmt-switch":"swp2"
    "leaf01":"swp31" -- "spine01":"swp1"
    "leaf01":"swp32" -- "spine02":"swp1"
    "spine02":"eth0" -- "oob-mgmt-switch":"swp6"
    "spine02":"swp2" -- "leaf02":"swp32"
    "leaf02":"eth0" -- "oob-mgmt-switch":"swp4"
    "leaf02":"swp31" -- "spine01":"swp2"
    "spine01":"eth0" -- "oob-mgmt-switch":"swp5"
}
```
<!-- vale off -->
### Restore Configuration Files
<!-- vale on -->

After you create the simulation, you can restore the configuration files.

This {{<exlink url="https://gitlab.com/cumulus-consulting/features/cl_support_file_extractor" text="python script">}} pulls out all the relevant files and collates them into folders so you can use them to restore configuration from inside the simulation.

You can also use the {{<exlink url="https://gitlab.com/cumulus-consulting/features/simple-iac" text="infrastructure as code">}} Ansible playbook to restore configurations.
