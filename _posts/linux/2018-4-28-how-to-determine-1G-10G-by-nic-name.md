### How to determine a network card is 1G or 10G by name in CentOS 7?

CentOS 7  provides methods for consistent and predictable [network] device naming for network interfaces. 

PCI bus provides 1G(1000Mbps) speed at most.

PCI-E  bus provides 10G(10000Mbps) speed at most. 

The names have two-character prefixes based on the type of interface [device]:
- en for Ethernet,
- wl for wireless LAN (WLAN),
- ww for wireless wide area network (WWAN).

#### 8.1. Naming Schemes Hierarchy

By default, systemd will name interfaces using the following policy to apply the supported naming schemes:
- Scheme 1: Names incorporating Firmware or BIOS provided index numbers for on-board devices (example: eno1), 
are applied if that information from the firmware or BIOS is applicable and available, else falling back to scheme 2.
- Scheme 2: Names incorporating Firmware or BIOS provided PCI Express hotplug slot index numbers (example: ens1) are
applied if that information from the firmware or BIOS is applicable and available, else falling back to scheme 3.
- Scheme 3: Names incorporating physical location of the connector of the hardware (example: enp2s0),
are applied if applicable, else falling directly back to scheme 5 in all other cases.
- Scheme 4: Names incorporating interface's MAC address (example: enx78e7d1ea46da), is not used by default,
but is available if the user chooses.
- Scheme 5: The traditional unpredictable kernel naming scheme, is used if all other methods fail (example: eth0).

This policy, the procedure outlined above, is the default. If the system has biosdevname enabled, it will be used. 

Note that enabling biosdevname requires passing biosdevname=1 as a kernel command-line parameter, except in the case of a Dell system, where biosdevname will be used by default as long as it is installed. 
If the user has added udev rules which change the name of the kernel devices, those rules will take precedence.

#### device features  
[device feature]
Red Hat Enterprise Linux 7 uses predictable names for network devices. These
names are stable across reboots and network adapter replacements.
Predictable naming is enabled by default. In this naming scheme, a mainframe
network device has a name of the following form:

<pf><type><bus_id>

For example:

enccw0.0.f5f0

Where:

<pf> A two-character prefix for the network type. 

The type can be one of the following:
  en - Ethernet
  ww - WAN
  sl - serial line, such as CTC

<type> The device type. The device type of CCW devices is ccw.

<bus_id>

The bus ID identifies the device within the scope of a Linux instance. For a
CCW device, the bus ID is the device number of the device with a leading
“0.n.”, where n is the subchannel set ID, for example, 0.1.0ab1.

You can use znetconf -c or lscss -a to obtain a list of the devices in your system.
For more information about the predictable naming scheme, see the Red Hat
Enterprise Linux 7.2 Networking Guide.


  [network]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/ch-consistent_network_device_naming
  [device]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-understanding_the_predictable_network_interface_device_names
  [device feature]: http://public.dhe.ibm.com/software/dw/linux390/docu/lgu2dd02.pdf
