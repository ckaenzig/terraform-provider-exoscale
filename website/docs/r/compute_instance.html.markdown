---
layout: "exoscale"
page_title: "Exoscale: exoscale_compute_instance"
sidebar_current: "docs-exoscale-compute-instance"
description: |-
  Provides an Exoscale Compute instance resource.
---

# exoscale\_compute\_instance

Provides an Exoscale [Compute instance][compute-doc] resource. This can be used to create, modify, and delete Compute instances.


## Example Usage

```hcl
locals {
  zone = "ch-gva-2"
}

data "exoscale_compute_template" "ubuntu" {
  zone = local.zone
  name = "Linux Ubuntu 20.04 LTS 64-bit"
}

resource "exoscale_security_group" "web" {
  name = "web"
}

data "exoscale_security_group" "default" {
  name = "default"
}

resource "exoscale_compute_instance" "example" {
  zone               = local.zone
  name               = "webserver"
  type               = "standard.medium"
  template_id        = data.exoscale_compute_template.ubuntu.id
  disk_size          = 10
  security_group_ids = [
    data.exoscale_security_group.default.id, 
    exoscale_security_group.web.id,
  ]
  ssh_key            = "my-key"
  user_data          = <<EOF
#cloud-config
manage_etc_hosts: localhost
EOF
}
```


## Arguments Reference

* `zone` - (Required) The name of the [zone][zone] to deploy the Compute instance into.
* `name` - (Required) The name of the Compute instance.
* `type` - (Required) The Compute instance [type][type] (format: `FAMILY.SIZE`, e.g. `standard.medium`, `memory.huge`).  **WARNING**: updating this attribute stops/restarts the Compute instance.
* `template_id` - (Required) The ID of the instance [template][template] to use when creating the Compute instance. Usage of the [`exoscale_compute_template`][d-compute_template] data source is recommended.
* `disk_size` - (Required) The Compute instance disk size in GiB (at least `10`). **WARNING**: updating this attribute stops/restarts the Compute instance.
* `anti_affinity_group_ids` - A list of [Anti-Affinity Group][r-anti_affinity_group] IDs (at creation time only) to assign the Compute instance. Usage of the [`exoscale_anti_affinity_group`][d-anti_affinity_group] data source is recommended.
* `security_group_ids` - A list of [Security Group][r-security_group] IDs to attach to the Compute instance. Usage of the [`exoscale_security_group`][d-security_group] data source is recommended.
* `private_network_ids` - A list of [Private Network][r-private_network] IDs to attach to the Compute instance. Usage of the [`exoscale_private_network`][d-private_network] data source is recommended.
* `elastic_ip_ids` - A list of [Elastic IP][r-elastic_ip] IDs to attach to the Compute instance. Usage of the [`exoscale_elastic_ip`][d-elastic_ip] data source is recommended.
* `ipv6` - Enable IPv6 on the Compute instance (default: `false`).
* `ssh_key` - The name of the [SSH key pair][sshkeypair] to install to the Compute instance's user account during creation.
* `user_data` - A [cloud-init][cloudinit] configuration.
* `state` - The state of the Compute instance (supported values: `started`, `stopped`).
* `deploy_target_id` - A Deploy Target ID.
* `labels` - A map of key/value labels.


## Attributes Reference

In addition to the arguments listed above, the following attributes are exported:

* `created_at` - The creation date of the Compute instance.
* `id` - The ID of the Compute instance.
* `ipv6_address` - The IPv6 address of the Compute instance main network interface.
* `public_ip_address` - The IPv4 address of the Compute instance's main network interface.


## `remote-exec` provisioner usage

If you wish to log to a `exoscale_compute_instance` resource using the [`remote-exec`][remote-exec] provisioner, make sure to explicity set the SSH `user` setting to connect to the instance to the actual template username returned by the [`exoscale_compute_template`][compute_template] data source:

```hcl
data "exoscale_compute_template" "ubuntu" {
  zone = "ch-gva-2"
  name = "Linux Ubuntu 20.04 LTS 64-bit"
}

resource "exoscale_compute_instance" "mymachine" {
  # ...

  provisioner "remote-exec" {
    connection {
      type = "ssh"
      host = self.ip_address
      user = data.exoscale_compute_template.ubuntu.username
    }
  }
}
```


## Import

An existing Compute instance can be imported as a resource by `<ID>@<ZONE>`:


```console
$ terraform import exoscale_compute_instance.my-instance eb556678-ec59-4be6-8c54-0406ae0f6da6@ch-gva-2
```


[cloudinit]: http://cloudinit.readthedocs.io/en/latest/
[compute-doc]: https://community.exoscale.com/documentation/compute/
[d-anti_affinity_group]: ../d/anti_affinity_group.html
[d-compute_template]: ../d/compute_template.html
[d-elastic_ip]: ../d/elastic_ip.html
[d-private_network]: ../d/private_network.html
[d-security_group]: ../d/security_group.html
[r-anti_affinity_group]: anti_affinity_group.html
[r-elastic_ip]: ../r/elastic_ip.html
[r-private_network]: ../r/private_network.html
[r-security_group]: security_group.html
[remote-exec]: https://www.terraform.io/docs/provisioners/remote-exec.html
[sshkeypair-doc]: https://community.exoscale.com/documentation/compute/ssh-keypairs/
[template]: https://www.exoscale.com/templates/
[type]: https://www.exoscale.com/pricing/#/compute/
[zone]: https://www.exoscale.com/datacenters/
