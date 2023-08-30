# Ansible Collection - swisscom.vmware_nsxt

Documentation for the collection.

# inventory
```
localhost ansible_connection=local

[nsxt]
lab01-ntm-01.vcloud24.net ansible_connection=ssh  ansible_host=172.16.1.11  ansible_ssh_user=root  ansible_password=VMwareNSX1..
```

# nsxt.yml
```
nsxt:
  deployment:
    vce:
      hostname: '{{ deployment_vcenter }}'
      username: '{{ deployment_vcenter_user }}'
      password: '{{ deployment_vcenter_pw }}'

    ova: ''

    datacenter: '{{ deployment_datacenter }}'
    folder: '{{ deployment_folder }}'
    cluster: '{{ deployment_cluster }}'

    disk_provisioning: 'thin'
    datastore: '{{ deployment_datastore }}'

    eth0:
      network: 'vxw-dvs-135-virtualwire-157-sid-20121-LS-WL01-PRD03-CMP'
      netmask: 255.255.255.224

    ntp_server: '{{ dns_servers[0] }},{{ dns_servers[1] }}'
    allow_root_login: false
    enable_ssh: true

  username: 'admin'
  admin_password: 'VMwareNSX1..'
  cli_password: 'VMwareNSX1..'
  audit_password: 'VMwareNSX1..'

  role: "NSX Manager"



  license:
    - "EM295-0634K-183L1-0K30P-8N34M"
    - "KJ484-JKL46-Z8TH1-098UP-A5JL0"



  ip_pools:
    - id: "vtep-ip-address-pool"
      display_name: "vtep-ip-address-pool"
      subnets:
        - id: "a9ba3ea8-c2e5-45c3-aa73-216197b37022"
          display_name: "a9ba3ea8-c2e5-45c3-aa73-216197b37022"
          cidr: "10.0.0.0/24"
          ranges:
            - start: "10.0.0.1"
              end: "10.0.0.254"
          #gateway_ip: ""
          #dns_nameservers: ""
          #dns_suffix: ""



  transport_zone:
    - id: "1b3a2f36-bfd1-443e-a0f6-4de01abc963e"
      display_name: "nsx-overlay-transportzone"
      tz_type: "OVERLAY_STANDARD"
      is_default: false

    - id: "a95c914d-748d-497c-94ab-10d4647daeba"
      display_name: "nsx-vlan-transportzone"
      tz_type: "VLAN_BACKED"
      is_default: false



  uplink_profile:
    - id: "ce766854-e12a-482a-9ca3-082e4c04f5ad" #generate a uuid
      display_name: "vc24-default-loadbalance-uplink-hostswitch-profile"
      #overlay_encap: "GENEVE"
      teaming:
        policy: "LOADBALANCE_SRCID"
        active_list:
          - uplink_name: "uplink-1"
            uplink_type: "PNIC"
          - uplink_name: "uplink-2"
            uplink_type: "PNIC"
      transport_vlan: 601

    - id: "5d26f990-8872-44ce-8b33-c6be80a6b62a"
      display_name: "vc24-nsx-edge-single-nic-uplink-profile"
      #overlay_encap: "GENEVE"
      teaming:
        policy: "FAILOVER_ORDER"
        active_list:
          - uplink_name: "uplink-1"
            uplink_type: "PNIC"
      transport_vlan: 601



  transportnode_profile:
    - id: "5745ef4e-f247-47f6-b92e-b204c51bf000" #Generate a UUID by yourself
      display_name: "demo-default-loadbalance-transportnode-profile"
      description: "Demo"
      host_switch:
        - dvs_switch: "lab01"
          display_name: "TEST"
          ip_pool: "demo-ip-address-pool"
          transport_zone:
            - transport_zone_name: "demo-nsx-overlay-transportzone"
              transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          uplink_profile: "demo-default-loadbalance-uplink-hostswitch-profile"
          uplinks:
            - vds_uplink_name: "Uplink 1"
              uplink_name: "uplink-1"
            - vds_uplink_name: "Uplink 2"
              uplink_name: "uplink-2"



  edgecluster_profile:
    - display_name: "test"
      resource_type: "EdgeHighAvailabilityProfile"
      bfd_probe_interval: 1000
      bfd_allowed_hops: 255
      bfd_declare_dead_multiple: 3
      standby_relocation_config:
        standby_relocation_threshold: 30



  edgenode:
  #Edge lab01-nte-01.vcloud24.net with dual teps
    - display_name: "lab01-nte-01.vcloud24.net"
      description: "Deployed by Ansible"

      host_switches:
      - dvs_switch: lab01
        uplink_profile: "vc24-default-loadbalance-uplink-hostswitch-profile"
        uplinks:
        - device_name: fp-eth0
          uplink_name: uplink-1
        - device_name: fp-eth1
          uplink_name: uplink-2
        ip_pool: "vtep-ip-address-pool"
        transport_zone_endpoints:
          - transport_zone_name: "nsx-overlay-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          - transport_zone_name: "nsx-vlan-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"

      deployment_config:
        vm_deployment_config: #grab from vcenter later
          vc_id: 228edb6f-3dcf-4cde-a19a-79c98e4287cb
          compute_id: resgroup-1044 #domain-c8  <-cluster or resourcepool
          storage_id: datastore-42
          host_id: host-7011
          management_network_id: dvportgroup-33
          ipv4_assignment_enabled: true
          management_port_subnets:
          - ip_addresses:
            - 172.16.1.16
            prefix_length: 26
          default_gateway_addresses:
          - 172.16.1.1
          data_network_ids:
          - "/infra/segments/lab01-0000trunk"
          - "/infra/segments/lab01-0000trunk"
        form_factor: SMALL
        node_user_settings:
          cli_username: "admin"
          root_password: "VMwareAdmin123!"
          cli_password: "VMwareAdmin123!"

      node_settings:
        hostname: "lab01-nte-01.vcloud24.net"
        search_domains:
        - vcloud24.net
        ntp_servers:
        - 172.16.1.1
        dns_servers:
        - 172.16.1.1
        enable_ssh: true
        allow_ssh_root_login: true
        enable_upt_mode: false

  #Edge lab01-nte-02.vcloud24.net with single tep
    - display_name: "lab01-nte-02.vcloud24.net"
      description: "Deployed by Ansible"

      host_switches:
      - dvs_switch: lab01
        uplink_profile: "vc24-nsx-edge-single-nic-uplink-profile"
        uplinks:
        - device_name: fp-eth0
          uplink_name: uplink-1
        ip_pool: "vtep-ip-address-pool"
        transport_zone_endpoints:
          - transport_zone_name: "nsx-overlay-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          - transport_zone_name: "nsx-vlan-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"

      deployment_config:
        vm_deployment_config: #grab from vcenter later
          vc_id: 228edb6f-3dcf-4cde-a19a-79c98e4287cb
          compute_id: resgroup-1044 #domain-c8 <-cluster or resourcepool
          storage_id: datastore-42
          host_id: host-7011
          management_network_id: dvportgroup-33
          ipv4_assignment_enabled: true
          management_port_subnets:
          - ip_addresses:
            - 172.16.1.18
            prefix_length: 26
          default_gateway_addresses:
          - 172.16.1.1
          data_network_ids:
          - "/infra/segments/lab01-0000trunk"
          - "/infra/segments/lab01-0000trunk"
        form_factor: SMALL
        node_user_settings:
          cli_username: "admin"
          root_password: "VMwareAdmin123!"
          cli_password: "VMwareAdmin123!"

      node_settings:
        hostname: "lab01-nte-02.vcloud24.net"
        search_domains:
        - vcloud24.net
        ntp_servers:
        - 172.16.1.1
        dns_servers:
        - 172.16.1.1
        enable_ssh: true
        allow_ssh_root_login: true
        enable_upt_mode: false



  edgecluster:
    - display_name: edgecluster-01
      cluster_profile: nsx-default-edge-high-availability-profile
      members:
      - display_name: lab01-nte-01.vcloud24.net
      - display_name: lab01-nte-02.vcloud24.net
```

# site.yml
```
---
- hosts: nsxt
  gather_facts: false
  collections:
    - swisscom.vmware_nsxt
  tasks:
    - name: Day 1 - Deployment
      include_role:
        name: "nsxt_deploy"

    - name: Day 2 - Api Session
      include_role:
        name: "{{ item }}"
      loop:
        - nsxt_config_license
        - nsxt_config_ippool
        - nsxt_config_transportzone
        - nsxt_config_profile-uplink
        - nsxt_config_profile-edgecluster
        - nsxt_config_profile-transportnode
        - nsxt_config_edgenode
        - nsxt_config_edgecluster
```