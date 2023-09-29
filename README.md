# Ansible Collection - swisscom.vmware_nsxt

Documentation for the collection.

# inventory
```
localhost ansible_connection=local

[vmw_nsxt]
lab01-ntm-01.vcloud24.net
```

# vmw_nsxt.yml
```
nsxt:
  api_host: localhost

  deployment:
    host: localhost
    chdir: /root/repository
    ova: 'nsx-unified-appliance-4.1.1.0.0.22224317.ova'

    vce:
      hostname: 'lab01-vce-01.vcloud24.net'
      username: 'administrator@vsphere.local'
      password: 'VMwareVCE1.'

    datacenter: 'dc-lab01-01'
    folder: 'fld-InfraServices'
    # to cluster 'base01'
    # to resource pool 'base01/Resources/vcd01'
    cluster: 'cl-wms-001/Resources/rp-InfraServices'

    disk_provisioning: 'thin'
    datastore: 'ds-base01-nfs-01_vmw-vsphere_bkp'

    eth0:
      network: 'pg-vmw_mgmt'
      netmask: 255.255.255.192
    
    gateway: 172.16.1.1
    dns: 172.16.1.1
    domain: 'vcloud24.net'
    search: 'vcloud24.net'

    ntp_server: 172.16.1.1
    allow_root_login: true
    enable_ssh: true

    role: "NSX Manager"
    option: "small"

  grub_password: 'VMwareNSX1..'

  admin_username: 'admin'
  admin_password: 'VMwareNSX1..'

  cli_username: 'admin'
  cli_password: 'VMwareNSX1..'

  audit_username: 'audit'
  audit_password: 'VMwareNSX1..'



# POST https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_CreateLicense.html
# default_parameter
#   none
  license:
    - license_key: ""
    - license_key: ""



# POST https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_AddComputeManager.html
# PUT https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_UpdateComputeManager.html
# default_parameter
#   credential.credential_type: 'UsernamePasswordLoginCredential'
#   credential.thumbprint: will be requested
#   resource_type: 'ComputeManager'
  compute_manager:
    - display_name: 'lab01-vce-01.vcloud24.net'
      description: 'Managed by Ansible'
      server: 'lab01-vce-01.vcloud24.net'
      origin_type: 'vCenter'
      multi_nsx: 'false'
      credential:
        username: 'administrator@vsphere.local'
        password: 'VMwareVCE1.'
      create_service_account: 'true'
      set_as_oidc_provider: 'true'
      access_level_for_oidc: 'LIMITED' #or FULL



# PATCH https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_CreateOrPatchIpAddressPool.html
# default_parameter
#   resource_type: 'IpAddressPool'
  ip_pool:
    - id: "vtep-ip-address-pool" #Generate a UUID
      display_name: "vtep-ip-address-pool"
      description: "Managed by Ansible"



# PATCH https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_CreateOrPatchIpAddressPoolSubnet.html
# default_parameter
#   none
  ip_subnet:
    - ippool: "vtep-ip-address-pool" #display_name, for assigning, will be remove from payload
      id: "a9ba3ea8-c2e5-45c3-aa73-216197b37022" #Generate a UUID
      display_name: "a9ba3ea8-c2e5-45c3-aa73-216197b37022" #Use UUID from id
      description: "Managed by Ansible"
      resource_type: "IpAddressPoolStaticSubnet" #or IpAddressPoolBlockSubnet
      cidr: "10.0.0.0/24"
      allocation_ranges:
        - start: "10.0.0.1"
          end: "10.0.0.254"
      #gateway_ip: ""
      #dns_nameservers: ""
      #dns_suffix: ""



# PATCH https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_PatchTransportZoneForEnforcementPoint.html 
# default_parameter
#   resource_type: 'PolicyTransportZone'
  transport_zone:
    - id: "1b3a2f36-bfd1-443e-a0f6-4de01abc963e"
      description: "Managed by Ansible"
      display_name: "nsx-overlay-transportzone"
      tz_type: "OVERLAY_STANDARD"
      is_default: false

    - id: "a95c914d-748d-497c-94ab-10d4647daeba"
      description: "Managed by Ansible"
      display_name: "nsx-vlan-transportzone"
      tz_type: "VLAN_BACKED"
      is_default: false



# PATCH https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_PatchPolicyHostSwitchProfile.html
# default_parameter
#   resource_type: 'PolicyUplinkHostSwitchProfile'
  uplink_profile:
    - id: "ce766854-e12a-482a-9ca3-082e4c04f5ad"
      display_name: "vc24-default-loadbalance-uplink-hostswitch-profile"
      description: "Managed by Ansible1"
      overlay_encap: "GENEVE"
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
      description: "Managed by Ansible1"
      overlay_encap: "GENEVE"
      teaming:
        policy: "FAILOVER_ORDER"
        active_list:
          - uplink_name: "uplink-1"
            uplink_type: "PNIC"
      transport_vlan: 601



#
# default_parameter
#
  transportnode_profile:
    - id: "9447a6f3-bf01-493d-ba07-5c2e1bc5c6b1"
      display_name: "demo-default-loadbalance-transportnode-profile"
      description: "Demo"
      host_switch:
        - dvs_switch: "lab01"
          display_name: "lab01"
          ip_pool: "demo-vtep-ip-address-pool"
          transport_zone:
            - transport_zone_name: "demo-nsx-overlay-transportzone"
              transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          uplink_profile: "vc24-default-loadbalance-uplink-hostswitch-profile"
          uplinks:
            - vds_uplink_name: "Uplink 1"
              uplink_name: "uplink-1"
            - vds_uplink_name: "Uplink 2"
              uplink_name: "uplink-2"
        - dvs_switch: "demo"
          display_name: "demo"
          ip_pool: "demo-vtep-ip-address-pool"
          transport_zone:
            - transport_zone_name: "demo-nsx-vlan-transportzone"
              transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          uplink_profile: "vc24-default-loadbalance-uplink-hostswitch-profile"
          uplinks:
            - vds_uplink_name: "Uplink 2"
              uplink_name: "uplink-1"
            - vds_uplink_name: "Uplink 1"
              uplink_name: "uplink-2"



# POST https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_CreateClusterProfile.html
# PUT https://vdc-repo.vmware.com/vmwb-repository/dcr-public/612caf56-5bb1-4a0b-8d2b-12ccd8981904/37be75ba-aa56-42f4-99c3-f6c989ddbe57/api_includes/method_UpdateClusterProfile.html
# default_parameter
#   resource_type: 'EdgeHighAvailabilityProfile'
  edgecluster_profile:
    - id: "61ca508b-c1d4-402d-b9f8-8de009649bff" # Generate a UUID
      display_name: "vc24-default-edge-high-availability-profile"
      description: "Managed by Ansible"
      bfd_probe_interval: 1000
      bfd_allowed_hops: 255
      bfd_declare_dead_multiple: 3
      standby_relocation_config:
        standby_relocation_threshold: 30



#
# default_parameter
#
  edgenode:
  #Edge lab01-nte-03.vcloud24.net
    - display_name: "lab01-nte-03.vcloud24.net"
      description: "Deployed by Ansible"

      host_switches:
      - dvs_switch: lab01
        uplink_profile: "vc24-nsx-edge-single-nic-uplink-profile"
        uplinks:
        - device_name: fp-eth0
          uplink_name: uplink-1
        # - device_name: fp-eth1
        #   uplink_name: uplink-2
        ip_pool: "vtep-ip-address-pool"
        transport_zone_endpoints:
          - transport_zone_name: "nsx-overlay-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          - transport_zone_name: "nsx-vlan-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"

      deployment_config:
        vm_deployment_config: #grab from vcenter later
          vc_id: 228edb6f-3dcf-4cde-a19a-79c98e4287cb
          compute_id: resgroup-1044 #domain-c8
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
        hostname: "lab01-nte-03.vcloud24.net"
        search_domains:
        - vcloud24.net
        ntp_servers:
        - 172.16.1.1
        dns_servers:
        - 172.16.1.1
        enable_ssh: true
        allow_ssh_root_login: true
        enable_upt_mode: false

  #Edge lab01-nte-04.vcloud24.net
    - display_name: "lab01-nte-04.vcloud24.net"
      description: "Deployed by Ansible"

      host_switches:
      - dvs_switch: lab01
        uplink_profile: "vc24-nsx-edge-single-nic-uplink-profile"
        uplinks:
        - device_name: fp-eth0
          uplink_name: uplink-1
        # - device_name: fp-eth1
        #   uplink_name: uplink-2
        ip_pool: "vtep-ip-address-pool"
        transport_zone_endpoints:
          - transport_zone_name: "nsx-overlay-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          - transport_zone_name: "nsx-vlan-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"

      deployment_config:
        vm_deployment_config: #grab from vcenter later
          vc_id: 228edb6f-3dcf-4cde-a19a-79c98e4287cb
          compute_id: resgroup-1044 #domain-c8
          storage_id: datastore-42
          host_id: host-7011
          management_network_id: dvportgroup-33
          ipv4_assignment_enabled: true
          management_port_subnets:
          - ip_addresses:
            - 172.16.1.19
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
        hostname: "lab01-nte-04.vcloud24.net"
        search_domains:
        - vcloud24.net
        ntp_servers:
        - 172.16.1.1
        dns_servers:
        - 172.16.1.1
        enable_ssh: true
        allow_ssh_root_login: true
        enable_upt_mode: false

  #Edge lab01-nte-05.vcloud24.net
    - display_name: "lab01-nte-05.vcloud24.net"
      description: "Deployed by Ansible"

      host_switches:
      - dvs_switch: lab01
        uplink_profile: "vc24-nsx-edge-single-nic-uplink-profile"
        uplinks:
        - device_name: fp-eth0
          uplink_name: uplink-1
        # - device_name: fp-eth1
        #   uplink_name: uplink-2
        ip_pool: "vtep-ip-address-pool"
        transport_zone_endpoints:
          - transport_zone_name: "nsx-overlay-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          - transport_zone_name: "nsx-vlan-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"

      deployment_config:
        vm_deployment_config: #grab from vcenter later
          vc_id: 228edb6f-3dcf-4cde-a19a-79c98e4287cb
          compute_id: resgroup-1044 #domain-c8
          storage_id: datastore-42
          host_id: host-7011
          management_network_id: dvportgroup-33
          ipv4_assignment_enabled: true
          management_port_subnets:
          - ip_addresses:
            - 172.16.1.20
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
        hostname: "lab01-nte-05.vcloud24.net"
        search_domains:
        - vcloud24.net
        ntp_servers:
        - 172.16.1.1
        dns_servers:
        - 172.16.1.1
        enable_ssh: true
        allow_ssh_root_login: true
        enable_upt_mode: false

  #Edge lab01-nte-06.vcloud24.net
    - display_name: "lab01-nte-06.vcloud24.net"
      description: "Deployed by Ansible"

      host_switches:
      - dvs_switch: lab01
        uplink_profile: "vc24-nsx-edge-single-nic-uplink-profile"
        uplinks:
        - device_name: fp-eth0
          uplink_name: uplink-1
        # - device_name: fp-eth1
        #   uplink_name: uplink-2
        ip_pool: "vtep-ip-address-pool"
        transport_zone_endpoints:
          - transport_zone_name: "nsx-overlay-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"
          - transport_zone_name: "nsx-vlan-transportzone"
            transport_zone_profile: "nsx-default-bfd-transportzone-profile"

      deployment_config:
        vm_deployment_config: #grab from vcenter later
          vc_id: 228edb6f-3dcf-4cde-a19a-79c98e4287cb
          compute_id: resgroup-1044 #domain-c8
          storage_id: datastore-42
          host_id: host-7011
          management_network_id: dvportgroup-33
          ipv4_assignment_enabled: true
          management_port_subnets:
          - ip_addresses:
            - 172.16.1.21
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
        hostname: "lab01-nte-06.vcloud24.net"
        search_domains:
        - vcloud24.net
        ntp_servers:
        - 172.16.1.1
        dns_servers:
        - 172.16.1.1
        enable_ssh: true
        allow_ssh_root_login: true
        enable_upt_mode: false



#
# default_parameter
#
  edgecluster:
    - display_name: edgecluster-01
      cluster_profile: nsx-default-edge-high-availability-profile
      members:
      - display_name: lab01-nte-01.vcloud24.net
      - display_name: lab01-nte-02.vcloud24.net

    - display_name: edgecluster-02
      cluster_profile: nsx-default-edge-high-availability-profile
      members:
      - display_name: lab01-nte-03.vcloud24.net
      - display_name: lab01-nte-04.vcloud24.net

    - display_name: edgecluster-03
      cluster_profile: nsx-default-edge-high-availability-profile
      members:
      - display_name: lab01-nte-05.vcloud24.net
      - display_name: lab01-nte-06.vcloud24.net
```

# site.yml
```
---
- hosts: vmw_nsx
  gather_facts: false
  collections:
    - swisscom.vmware_nsxt

  roles:
    - nsxt_deploy_manager
    - nsxt_config_license
    - nsxt_config_ippool
    - nsxt_config_transportzone
    - nsxt_config_profile-uplink
    - nsxt_config_profile-edgecluster
    - nsxt_config_compute-manager
    # - nsxt_config_profile-transportnode
    # - nsxt_deploy_edgenode
    # - nsxt_config_edgecluster
```