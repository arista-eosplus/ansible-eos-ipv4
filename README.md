IPv4 Role for EOS
=================

The arista.eos-ipv4 role creates an abstraction for EOS IP interface configuration.
This means that you do not need to write any ansible tasks. Simply create
an object that matches the requirements below and this role will ingest that
object and perform the necessary configuration.

This role is used to configure the IP address of a layer 3 interface.
You can also use it to set just the MTU on an interface without setting the IP
address. As noted below, the arista.eos-interfaces role may be required prior
to running this role as this role will not create the interface. This would
be in the case of setting the MTU on a non-default interface, like a Port-Channel,
as well as setting an IP address on a Loopback interface that isn't present.

Installation
------------

```
ansible-galaxy install arista.eos-ipv4
```


Requirements
------------

Requires an SSH connection for connectivity to your Arista device. You can use
any of the built-in eos connection variables, or the convenience ``provider``
dictionary.

Role Variables
--------------

The tasks in this role are driven by the ``ip_interfaces`` object described below:

**ip_interfaces** (list) each entry contains the following keys:

|     Key | Type                      | Notes                                                                                                                                                                                                                       |
|--------:|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    name | string (required)         | The unique interface identifier name. The interface name must use the full interface name (no abbreviated names). For example, interfaces should be specified as Ethernet1 not Et1                                          |
| address | string                    | Configures the IPv4 address for the interface. The value must be in the form of A.B.C.D/E. The EOS default value for address is None                                                                                        |
|     mtu | string                    | Sets the IP interface MTU value. The MTU value defines the maximum transmission unit (or packet size) that can traverse the link. Valid values are in the range of 68 to 65535 bytes. The EOS default value for mtu is 1500 |
|   state | choices: present*, absent | Set the state for the IP interface configuration. The interface IP address will be removed and the interface set back to switchport if set to absent.                                                                       |


```
Note: Asterisk (*) denotes the default value if none specified
```


Dependencies
------------

This role requires the underlying interface to be created before you set
an IP address or the MTU. This implies that we must use the arista.eos-interfaces
role in conjunction with arista.eos-ipv4.  You do not need to include the role
in your playbook, simply create the ``interfaces`` list documented in the
arista.eos-interfaces README.

The arista.eos-ipv4 role is built on modules included in the core Ansible code.
These modules were added in ansible version 2.1.0.

- Ansible 2.1.0
- arista.eos-interfaces

See the example below for more information.


Example Playbook
----------------

The following example will use the arista.eos-ipv4 role to setup
a few IP interfaces and set the MTU without writing any tasks. We'll create a
``hosts`` file with our switch, then a corresponding ``host_vars`` file and
then a simple playbook which only references the eos-ipv4 role.
By including the role, we automatically get access to all of the tasks
to configure layer 3 interfaces. What's nice about this is that if you have a
host without layer 3 configuration, the tasks will be skipped without any issue.


Sample hosts file:

    [leafs]
    leaf1.example.com

Sample host_vars/leaf1.example.com

    interfaces:
      - name: Loopback0
        enable: true
      - name: Loopback1
        enable: true
      - name: Ethernet1
        description: "[BGP]Connection to Spine1"
        enable: true
      - name: Ethernet2
        description: "[BGP]Connection to Spine2"
        enable: true

    ip_interfaces:
      - name: Loopback0
        address: 1.1.1.1/32
      - name: Loopback1
        address: 2.2.2.1/32
      - name: Ethernet1
        address: 10.1.1.0/31
        mtu: 9000
      - name: Ethernet2
        address: 10.1.1.2/31
        mtu: 9000


A simple playbook to configure BGP, leaf.yml

    - hosts: leafs
      roles:
        - arista.eos-ipv4

Then run with:

    ansible-playbook -i hosts leaf.yml

License
-------

Copyright (c) 2015, Arista Networks EOS+
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of Arista nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

Author Information
------------------

Please raise any issues using our GitHub repo or email us at ansible-dev@arista.com

[quickstart]: http://ansible-eos.readthedocs.org/en/latest/quickstart.html
