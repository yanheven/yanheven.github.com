---
layout: post
title: openstack nova 命令行指令大全  
category: cloud
description: 2013-01-05

---

Author:[Hyphen](http://weibo.com/344736086)


###### 来自官方文档
###### nova     

    absolute-limits     Print a list of absolute limits for a user
    actions             Retrieve server actions.
    add-fixed-ip        Add new IP address to network.
    add-floating-ip     Add a floating IP address to a server.
    add-secgroup        Add a Security Group to a server.
    aggregate-add-host  Add the host to the specified aggregate.
    aggregate-create    Create a new aggregate with the specified details.
    aggregate-delete    Delete the aggregate by its id.
    aggregate-details   Show details of the specified aggregate.
    aggregate-list      Print a list of all aggregates.
    aggregate-remove-host    Remove the specified host from the specified  aggregate.
    aggregate-set-metadata Update the metadata associated with the aggregate.
    aggregate-update    Update the aggregate's name and optionally  availability zone.
    boot                Boot a new server.
    cloudpipe-create    Create a cloudpipe instance for the given project
    cloudpipe-list      Print a list of all cloudpipe instances.
    cloudpipe-update Update a cloudpipe instance
    console-log         Get console log output of a server.
    credentials         Show user credentials returned from auth
    delete              Immediately shut down and delete a server.
    diagnostics         Retrieve server diagnostics.
    dns-create          Create a DNS entry for domain, name and ip.
    dns-create-private-domain  Create the specified DNS domain.
    dns-create-public-domain  Create the specified DNS domain.
    dns-delete          Delete the specified DNS entry.
    dns-delete-domain   Delete the specified DNS domain.
    dns-domains         Print a list of available dns domains.
    dns-list            List current DNS entries for domain and ip or domain and name.
    endpoints           Discover endpoints that get returned from the  authenticate services
    fixed-ip-get        Show information for a fixed IP
    fixed-ip-reserve    Reserve a fixed IP
    fixed-ip-unreserve  Unreserve fixed IP
    flavor-create       Create a new flavor
    flavor-delete       Delete a specific flavor
    flavor-key          Set or unset extra_spec for a flavor.
    flavor-list         Print a list of available 'flavors' (sizes of servers).
    flavor-show         Show details about the given flavor.
    floating-ip-create  Allocate a floating IP for the current tenant.
    floating-ip-delete  De-allocate a floating IP.
    floating-ip-list    List floating ips for this tenant.
    floating-ip-pool-list List all floating ip pools.
    get-vnc-console     Get a vnc console to a server.
    host-action         Perform a power action on a host.
    host-describe       Describe a specific host
    host-list           List all hosts by service
    host-update         Update host settings.
    hypervisor-list     List hypervisors.
    hypervisor-servers  List instances belonging to specific hypervisors.
    hypervisor-show     Display the details of the specified hypervisor.
    hypervisor-stats    Get hypervisor statistics over all compute nodes.[cpu,mem]
    hypervisor-uptime   Display the uptime of the specified hypervisor.
    image-create        Create a new image by taking a snapshot of a running server.
    image-delete        Delete an image.
    image-list          Print a list of available images to boot from.
    image-meta          Set or Delete metadata on an image.
    image-show          Show details about the given image.
    keypair-add         Create a new key pair for use with instances
    keypair-delete      Delete keypair by its id
    keypair-list        Print a list of keypairs for a user
    list                List active servers.
    list-extensions List available extensions
    live-migration      Migrates a running instance to a new machine.
    lock                Lock a server.
    meta                Set or Delete metadata on a server.

    migrate             Migrate a server.
    network-list        Print a list of available networks.
    network-show        Show details about the given network.
    pause               Pause a server.
    quota-class-show    List the quotas for a quota class.
    quota-class-update  Update the quotas for a quota class.
    quota-defaults      List the default quotas for a tenant.
    quota-show          List the quotas for a tenant.
    quota-update        Update the quotas for a tenant.
    rate-limits         Print a list of rate limits for a user
    reboot              Reboot a server.
    rebuild             Shutdown, re-image, and re-boot a server.
    remove-fixed-ip     Remove an IP address from a server.
    remove-floating-ip  Remove a floating IP address from a server.
    remove-secgroup     Remove a Security Group from a server.
    rename              Rename a server.
    rescue              Rescue a server.
    reset-state         Reset the state of an instance
    resize              Resize a server.
    resize-confirm      Confirm a previous resize.
    resize-revert       Revert a previous resize (and return to the previous
    VM).
    resume              Resume a server.
    root-password       Change the root password for a server.
    secgroup-add-group-rule
    Add a source group rule to a security group.
    secgroup-add-rule   Add a rule to a security group.
    secgroup-create     Create a security group.
    secgroup-delete     Delete a security group.
    secgroup-delete-group-rule
    Delete a source group rule from a security group.
    secgroup-delete-rule
    Delete a rule from a security group.
    secgroup-list       List security groups for the current tenant.
    secgroup-list-rules
    List rules for a security group.
    service-list        List nova services
    show                Show details about the given server.
    ssh                 SSH into a server.
    start               Start a server.
    stop                Stop a server.
    suspend             Suspend a server.
    unlock              Unlock a server.
    unpause             Unpause a server.
    unrescue            Unrescue a server.
    usage-list          List usage data for all tenants
    volume-attach       Attach a volume to a server.
    volume-create       Add a new volume.
    volume-delete       Remove a volume.
    volume-detach       Detach a volume from a server.
    volume-list         List all the volumes.
    volume-show         Show details about a volume.
    volume-snapshot-create      Add a new snapshot.
    volume-snapshot-delete      Remove a snapshot.
    volume-snapshot-list      List all the snapshots.
    volume-snapshot-show       Show details about a snapshot.

    volume-type-create  Create a new volume type.
    volume-type-delete  Delete a specific flavor.
    volume-type-list    Print a list of available 'volume types'.
    x509-create-cert    Create x509 cert for a user in tenant.
    x509-get-root-cert  Fetches the x509 root cert.
    bash-completion     Prints all of the commands and options to stdout.