Network Topology: Dedicated Setup
=================================
When the control plane has a separate NIC connected to ToR for Device Management to control various devices like iDRAC, switches and PowerVault, separate switches for management and host network are used. Omnia will run the management network POD for this network. An additional unmanaged switch is needed as a pass through switch.    This connection option uses the server’s dedicated iDRAC connection for management, while the production data traffic flows through the NICs on the server. This configuration separates the two flows of traffic. For more information on the topology, click on the hyperlinked heading.
* Connecting a Pass-Through Switch: Provision and configure a 1GBE pass-through switch which will be used as a pass-through uplink switch. One of the NIC on the control plane must be connected to a data port on the pass-through switch and a second connection must be established from a data port on the pass-through switch to the management port of the TOR network switch.
    .. Note:: Omnia is not responsible for provisioning and configuring the pass-through switch.
* Establishing a management network: From the data ports on the pass-through switch, connect to the following ports:
         * iDRAC ports on manager and compute nodes
         * Management port on the network switches
         * Management port on the PowerVault devices
         * Management port on the InfiniBand switches

       Through this management network, management DHCP assigns IP addresses to the devices in the HPC cluster.
* Establishing a data network:
         * Connect one of the data ports on the PowerVault device to the NIC of the compute node.
         * For InfiniBand DHCP, connect NIC on the control plane to one of the data ports of the InfiniBand Switch. Next, connect one of the data ports of the InfiniBand switch to the NIC on the compute node.
         * Establishing a host network: For Cobbler DHCP to assign an IP address to the compute node NIC, connect NIC of the control plane to the data port on the network switch. Connect another data port on the network switch to the NIC on the compute node. Omnia will provision OS on the compute nodes using PXE when the iDRAC Enterprise license is missing on any of the compute nodes in the HPC cluster.

.. Note::
    * Cobbler web support has been discontinued from Omnia 1.2 onwards.
    * Note that the PowerVault NFS server should have separate NICs configured for management, data (Connecting to other compute nodes) and a dedicated data connection to the storage array.
    * Refer to the Control Plane Pre-Requisites <../../RunningOmniaControlPlane/controlplaneprereqs.html> file to ensure smooth running of the control_plane.



Depending on internet access for host nodes, there are two ways to achieve a dedicated NIC setup:

1. Dedicated Setup with dedicated public NIC on compute nodes

When all compute nodes have their own public network access, ``primary_dns`` and ``secondary_dns`` in ``base_vars.yml`` become optional variables as the control plane is not required to be a gateway to the network. The network design would follow the below diagram:  

.. image:: ../../images/Omnia_NetworkConfig_Inet.png)

2. Dedicated Setup with single NIC on compute nodes

When all compute nodes rely on the control plane for public network access, the variables ``primary_dns`` and ``secondary_dns`` in ``base_vars.yml`` are used to indicate that the control plane is the gateway for all compute nodes to get internet access. Since all public network traffic will be routed through the control plane, the user may have to take precautions to avoid bottlenecks in such a set-up. ``
.. image:: ../../images/Omnia_NetworkConfig_NoInet.png

**Control plane configuration**

Depending on the user input in ``base_vars.yml``, the below table explains the outcomes of running ``provision.yml`` to configure the network:

+-----------------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+
| device_config_support | idrac_support | Outcome                                                                                                                                                                                                                                                                   | One Touch Configuration Support |
+=======================+===============+===========================================================================================================================================================================================================================================================================+=================================+
| TRUE                  | TRUE          | Omnia  will assign IPs to all the   management ports of the different devices. iDRAC  and PXE provisioning is supported. Here,   ethernet, InfiniBand and powervault    configurations are supported.                                                                     | Yes                             |
+-----------------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+
| TRUE                  | FALSE         | An assert  failure on   control_plane_common will manifest and Omnia Control Plane will  fail.                                                                                                                                                                            | No                              |
+-----------------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+
| FALSE                 | TRUE          | Assuming  the device_ip_list is   populated, mgmt_container will not be used to assign  the IPs to all the mgmt ports as a   device_ip_list indicates that IP    assignment is already done. However, ethernet, InfiniBand,   powervault  configurations are   supported. | Yes                             |
+-----------------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+
| FALSE                 | FALSE         | No IPs  will be assigned by Omnia.   Provisioning will only be through PXE.                                                                                                                                                                                               | No                              |
+-----------------------+---------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------+

.. note:: If `device_config_support` is false (ie, no management container is set up), no IPs will be assigned by Omnia. If a device IP list is provided, provisioning will only be through PXE.
