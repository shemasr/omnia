Configuring Ethernet Switches (S3 and S4 series)
------------------------------------------------

* Edit the ``ethernet_tor_input.yml`` file for all S3* and S4* PowerSwitches such as S3048-ON, S4048T-ON, S4112F-ON, S4048-ON, S4048T-ON, S4112F-ON, S4112T-ON, and S4128F-ON.

+----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Name                       | Default, accepted values                                                                                                                                                | Required? | Purpose                                                                                                                                                                                       |
+============================+=========================================================================================================================================================================+===========+===============================================================================================================================================================================================+
| os10_config                | "interface vlan1"                                                                                                                                                       | required  | Global configurations for the switch.                                                                                                                                                         |
|                            | "exit"                                                                                                                                                                  |           |                                                                                                                                                                                               |
+----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| snmp_trap_destination      |                                                                                                                                                                         | optional  | The trap destination IP address is the IP address of the SNMP Server where the trap will be sent. Ensure that the SNMP IP is valid.                                                           |
+----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| snmp_community_string      | public                                                                                                                                                                  | optional  | An SNMP community string is a means of accessing statistics stored within a router or other device.                                                                                           |
+----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ethernet 1/1/(1-52) config | By default:                                                                                                                                                             | required  | By default, all ports are brought up in admin UP state                                                                                                                                        |
|                            | Port description is provided. Each interface is set to "up" state. The fanout/breakout mode for 1/1/1 to 1/1/52 is as per the value set in the breakout_value variable. |           +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                            |                                                                                                                                                                         |           | Update the individual interfaces of the Dell PowerSwitch SS3048-ON.                                                                                                                           |
|                            |                                                                                                                                                                         |           | The interfaces are from ethernet 1/1/1 to ethernet 1/1/52. By default, the breakout mode is set for 1/1/1 to 1/1/52. Note: The playbooks will fail if any invalid configurations are entered. |
+----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| save_changes_to_startup    | false                                                                                                                                                                   | required  | Change it to "true" only when you are certain that the updated configurations and commands are valid.                                                                                         |
|                            |                                                                                                                                                                         |           | WARNING: When set to "true", the startup configuration file is updated. If incorrect configurations or commands are entered, the Ethernet switches may not operate as expected.               |
+----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+


* When initializing a factory reset switch, the user needs to ensure DHCP is enabled and an IPv6 address is not assigned. Omnia will assign an IP address to the switch using DHCP with all other configurations.


**Running the playbook**::

    cd omnia/network

    ansible-playbook ethernet_switch_config.yml -i inventory -e ethernet_switch_username=”” -e ethernet_switch_password=””

* Where ``ethernet_switch_username`` is the username used to authenticate into the switch.

* The inventory file should be a list of IPs separated by newlines. Check out the device_ip_list.yml section in `Sample Files <https://omnia-documentation.readthedocs.io/en/latest/samplefiles.html>`_

* Where ``ethernet_switch_password`` is the username used to authenticate into the switch.



