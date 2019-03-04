# openshift-ansible-ansible

Yo dawg, I heard you like Ansible, so I put Ansible in your Ansible!

# openshift-ansible-ansible

Yo dawg, I heard you like Ansible, so I put Ansible in your Ansible!

This is an Ansible role which deploys OpenShift using openshift-ansible.

It's suitable for running a single-node production OpenShift host.

Features:

- Deploy the openshift-ansible inventory.

- Create groups, assign users and cluster role bindings.

- Create (empty) projects and group permissions.

- Pragmatic local volume provisioning - manually create a number of local PVs with `nodeAffinity`.

Example host variables:

    openshift_cluster_domain: apps.example.com

Example group variables:

    openshift_version: "3.11"

    openshift_localstorage_custom_dirs: [/volumes/hosted_registry]
    
    openshift_groups:
      admin-users:
        - account@example.com

    openshift_ansible_inventory:
      OSEv3:
        hosts:
          <host-inserted-here>:
            ansible_connection: local
            openshift_node_group_name: node-config-all-in-one
        children:
          masters:
            hosts:
              <host-inserted-here>:
          etcd:
            hosts:
              <host-inserted-here>:
          nodes:
            hosts:
              <host-inserted-here>:
        vars:
          ansible_user: root

          openshift_deployment_type: origin
          openshift_release: "{{ openshift_version }}"
          openshift_master_default_subdomain: "{{ openshift_cluster_domain }}"
          openshift_master_cluster_hostname: "master.{{ openshift_cluster_domain }}"
          openshift_master_cluster_public_hostname: "console.{{ openshift_cluster_domain }}"

          # Disable Firewall and NTP management, assuming you have your own roles managing these
          os_firewall_enabled: no
          os_firewall_use_firewalld: no
          openshift_clock_enabled: no

          openshift_master_identity_providers:
          - name: google
            challenge: false
            login: true
            mappingMethod: claim
            kind: GoogleIdentityProvider
            clientID: [...]
            clientSecret: [...]
            hostedDomain: [...]

          openshift_hosted_registry_storage_kind: hostpath
          openshift_hosted_registry_storage_access_modes: [ReadWriteOnce]
          openshift_hosted_registry_storage_hostpath_path: /volumes/hosted_registry
          openshift_hosted_registry_storage_volume_size: 50Gi

          os_sdn_network_plugin_name: redhat/openshift-ovs-networkpolicy
          osm_host_subnet_length: 16

          openshift_disable_check: docker_storage

          debug_level: 2
