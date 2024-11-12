---
title: Quick Start
author: NVIDIA
weight: 20
product: NVIDIA Air
---

This quick start provides the basics so that you can get started with the NVIDIA Air network simulation platform.

## Supported Browsers

The following browsers are fully supported:

- Google Chrome 120 or later
- Mozilla Firefox 121 or later

The following browsers are supported on a best-effort basis:

- Microsoft Edge 
- Safari

## Login

To log in, go to {{<exlink url="https://air.nvidia.com" text="air.nvidia.com">}}. Enter your business email address, then click **Next.** A valid business email address is required to access NVIDIA Air. If you do not have an account, create a new one.

{{<img src="/images/guides/nvidia-air/Login.png" alt="" width="800px">}}

{{%notice note%}}
If your email address is not accepted as a valid business email address and you believe this to be incorrect, please contact us at {{<exlink url="mailto:air-support@nvidia.com" text="air-support@nvidia.com">}}
{{%/notice%}}

## Simulations
When you log into Air, you will first see your list of current simulations in your account. Click on a simulation to view it. You can power on/off your simulations, edit {{<link url="#edit-simulations" text="edit">}} various aspects of it, {{<link url="#sharing-simulations" text="share it with others">}} and delete it with the **Actions** ![image](https://github.com/user-attachments/assets/b3a58121-fb87-49d3-a506-959010b63908) button on each sim.

You can click the **Topology**, **Nodes** and **Links** tabs for different views. Single click on a node to view its **Node Properties** and double click to open its console.

The ticking timer represents when your sim will automatically sleep, or be **stored**. You can add more time by clicking the **Actions ![image](https://github.com/user-attachments/assets/b3a58121-fb87-49d3-a506-959010b63908) > Add Time**.

### Create a Simulation

You can create new simulations in several different ways. NVIDIA Air provides multiple means of creating your own topologies from scratch, and also provides a {{<exlink url="https://air.nvidia.com/demos" text="Demo Marketplace">}} for fully preconfigured simulations.

To create a new sim, click the **Create Simulation** button. You can also click **Workspace > New Simulation**.

To learn about custom topologies and creating sims with Air's built-in drag-and-drop editor, visit {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/nvidia-air/Custom-Topology/" text="Custom Topology">}}.

To learn about preconfigured, ready out-of-box simulations, visit {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/nvidia-air/Pre-Built-Demos/" text="Pre-built Demos">}}.

### Import a Topology

_I think these should go under Custom Topology_

Network topologies describe which nodes a data center is comprised of, how they are configured and which other nodes they are connected to. A format is a way to structure and represent such topologies. Air is able to create simulations out of network topologies structured using a supported format.

{{< tabs "TabID110 ">}}
{{< tab "Example 1">}}

The following topology defines two nodes (`node-1` and `node-2`) connected to each other via their respective `eth1` interfaces, and the Out-of-Band management network enabled by default. 

```
{
    "nodes": {
        "node-1": {
            "os": "generic/ubuntu2204"
        },
        "node-2": {
            "os": "generic/ubuntu2204"
        }
    },
    "links": [
        [{"node": "node-1", "interface": "eth1"}, {"node": "node-2", "interface": "eth1"}]
    ]
}
```

{{< /tab >}}
{{< tab "Example 2">}}

The following topology defines two nodes (`node-1` and `node-2`) connected to each other via their respective `eth1` interfaces, and the Out-of-Band management network disabled (`"oob": false`). The example showcases:
- Custom values for configurable node fields (`cpu`, `memory`, `storage`)
- Public-facing interface (with a custom `mac` address) to the outside world (`eth2` of `node-1`)
- Referencing `os` image by specific UUID (`node-2`)

```
{
    "oob": false,
    "nodes": {
        "node-1": {
            "os": "generic/ubuntu2204",
            "cpu": 2,
            "memory": 2048
        },
        "node-2": {
            "os": "defb3ffc-e29b-4d3a-a5fb-41ed1974f938",
            "memory": 2048,
            "storage": 25
        }
    },
    "links": [
        [{"node": "node-1", "interface": "eth1"}, {"node": "node-2", "interface": "eth1"}],
        [{"node": "node-1", "interface": "eth2", "mac": "02:00:00:00:00:07"}, "exit"]
    ]
}
```

{{< /tab >}}
{{< /tabs >}}

A more detailed schema for this format can be viewed by visiting the [API documentation](https://air.nvidia.com/api/#/v2/v2_simulations_import_create).

{{< expand "Import Instructions" >}}

In order to import a topology, the following API v2 SDK method can be used:

```
from air_sdk.v2 import AirApi

air = AirApi(
    authenticate=True,
    username='<username>',
    password='<password-or-token>',
)
simulation = air.simulations.create_from(
    title='<simulation-name>',
    format='JSON',
    content=<topology-content>,
    organization=<optional-organization>
)
```

{{%notice info%}}
Minimum required SDK version for this feature is `air-sdk>=2.14.0`
{{%/notice%}}

Topology content can be provided in multiple ways:

{{< tabs "TabID111 ">}}
{{< tab "Python Dictionary">}}

```
simulation = air.simulations.create_from(
    'my-simulation',
    'JSON',
    {
        'nodes': {
            'node-1': {
                'os': 'generic/ubuntu2204',
            },
            'node-2': {
                'os': 'generic/ubuntu2204',
            },
        },
        'links': [
            [{'node': 'node-1', 'interface': 'eth1'}, {'node': 'node-2', 'interface': 'eth1'}]
        ]
    },
)
```

{{< /tab >}}
{{< tab "JSON String">}}

```
simulation = air.simulations.create_from(
    'my-simulation',
    'JSON',
    '{"nodes": {"node-1": {"os": "generic/ubuntu2204"}, "node-2": {"os": "generic/ubuntu2204"}}, "links": [[{"node": "node-1", "interface": "eth1"}, {"node": "node-2", "interface": "eth1"}]]}'
)
```

{{< /tab >}}
{{< tab "File Path">}}

```
import pathlib
simulation = air.simulations.create_from(
    'my-simulation',
    'JSON',
    pathlib.Path('/path/to/topology.json')
)
```

{{< /tab >}}
{{< tab "File Descriptor">}}

```
import pathlib
with pathlib.Path('/path/to/topology.json').open('r') as topology_file:
    simulation = air.simulations.create_from(
        'my-simulation',
        'JSON',
        topology_file
    )
```

{{< /tab >}}
{{< /tabs >}}

{{< /expand >}}

### Export a Topology

_I think these should go under Custom Topology_


Existing simulations can be exported into a format representation. More information about this process can be found by visiting the [API documentation](https://air.nvidia.com/api/#/v2/v2_simulations_export_retrieve).

{{< expand "Export Instructions" >}}

In order to export a simulation, the following API v2 SDK method can be used:

```
from air_sdk.v2 import AirApi

air = AirApi(
    authenticate=True,
    username='<username>',
    password='<password-or-token>',
)
topology = air.simulations.export(
    simulation='<simulation-instance-or-id>',
    format='JSON',
    image_ids=True,  # defaults to False
)
```

{{%notice info%}}
Minimum required SDK version for this feature is `air-sdk>=2.15.0`
{{%/notice%}}

{{< /expand >}}

### Node Consoles

Double click on any node to connect to its console. 

Click the ![image](https://github.com/user-attachments/assets/9b32b625-9126-4570-82a1-23969cc9b43a)
 icon to view its login credentials. 
 
 Click the ![image](https://github.com/user-attachments/assets/8e68f8bd-f65c-4525-acfb-b14a2cb9bb4a)
 button to open the console in its own window.

### Services

Enabling services allows more integration with your sim, such as using a preferred SSH client to access your sim, running Grafana, or setting up SNMP polling.

To add a new service to your sim:
1. Click **Services > New Services** to create an external connection with your sim.
2. **Service Name**: Name for your service. Handy for running multiple instances of the same service on different interfaces or ports.
3. **Interface**: Where the connection terminates. Typically eth0 on the `oob-mgmt-server`.
4. **Service Type**: NVIDIA Air creates a hyperlink to the URL automatically in the Services panel for _SSH_, _HTTP_ or _HTTPS_ services. For _Other_ services, any port can be used, but Air will not generate a hyperlink. The hyperlink provides a quick way to copy and paste the service if your browser supports it.
5. **Service Port**: Internal port where service terminates.
6. Click **Create**.

Click **Services > Enable SSH** to enable SSH into the `oob-mgmt-server` immediately without having to create a custom service. Use this to leverage your preferred local SSH client. Only available when the OOB network is enabled. SSH password authentication is disabled on the `oob-mgmt-server` by default. To use SSH password authentication, you must upload SSH keys to your user profile; see {{<link url="#SSH-Keys" text="SSH Keys">}} below.

Click **Services > View Services** to view existing services enabled on the sim. Here you can also view important access information such as the port and external host to connect with.

### Rebuilding & Resetting Nodes

Single click on a node to view its **Node Properties**. Click **Advanced Properties > Actions** to **Rebuild** or **Reset** the node.

- **Rebuild**: Restores the node to its original or default configuration. If the node was created from a demo, or other snapshot, it will revert to that configuration had you just launched a copy.
- **Reset**: Performs a hard reboot to the node.

You can also rebuild the entire simulation with **Workspace > Rebuild All Nodes**.

### Edit Simulations

Click **Workspace > Edit Simulation** to edit various aspects of your sim.

- **Name**: Edit the sim name. Simulations can have duplicate names, as they each have a unique ID under the hood.
- **Organization**: Assign an Organization. You can read more about Organizations {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/nvidia-air/Organizations/" text="here">}}. This will assign this exact simulation to the Organization, _not a copy_. This means each user with appropriate permissions will have access to this sim.

- **Sleep date**: When the simulation will be automatically put to sleep. This means the state of the sim is saved and resources are freed for your account. Nodes are _not_ powered off.
- **Expiration date**: When the simulation will be automatically deleted.

You can also edit your sim from the **Simulations homepage > Actions > Edit Simulation**.


## API Tokens & SSH Keys

Click your username in the top right and click **Settings** to view your API tokens and SSH keys.

### API Tokens

API tokens allow you to execute authenticated activities using the NVIDIA Air API or SDK.

To generate an API token:

1. **Name** your API token.
2. Set an **Expiration Date**.
3. Click **Create**.
4. Save your token somewhere safe. You will not be able to view it again.

### SSH Keys
SSH keys must be added here when you wish to enable the SSH service on simulations. They are automatically added to the `oob-mgmt-server`.

To add an SSH key:

1. **Name** your SSH key.
2. Copy your **Public Key**.
3. Click **Add**.

You can revoke/delete both API tokens and SSH keys if you no longer need them, or they are believed to be compromised.

## Sharing Simulations
Sharing a simulation with another user is a common use case in Air. 

To share a sim: 

1. Click **Workspace > Manage Users** to share this exact sim, _not a copy_, with any other user. 
2.	Enter their email address. You can enter multiple addresses.
3.	Toggle whether they only have **Read Only** access. This means they will not be able to make any modifications to the simulation in Air, such as deleting it or placing it in an Organization. The user can still access consoles and modify the simulation directly.
4.	Click **Add User**.
   
The user(s) will now see the simulation in their Simulations list to access. The user will not receive any notification they were given access to the sim. 

You can also share simulations via Organizations. Read more about them {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/nvidia-air/Organizations/" text="here">}}. 

## Resource Budgets

_this may want to go under Orgs?_
The number of simulation resources a user can consume is limited for each user's account. For an account using a valid business email, a user is granted the following limits:

- 60 vCPUs
- 90GB memory
- 650GB storage
- 4 running simulations

NVIDIA users are granted the following limits:

- 100 vCPUs
- 100GB memory
- 1TB storage
- 5 running simulations

Individual user account resources are great for running demos and smaller simulations but to run larger simulations it is best to use {{<link title="Organizations">}}.
Organizations have a much higher resource budget than an individual user account. The default resource budget for an organization is:

- 300 vCPUs
- 300GB memory
- 3TB storage
- 10GB Image Storage
- 15 running simulations

The budgets for organizations can be adjusted based on the needs of that organization. If a resource budget for an organization needs to be expanded, contact the Air Support team via the option to "Report An Issue" from air.nvidia.com.

## Other Notes

- Using {{<exlink url="https://docs.nvidia.com/networking-ethernet-software/cumulus-linux/Cumulus-Linux-in-a-Virtual-Environment/" text="Cumulus Linux in a Virtual Environment">}}
