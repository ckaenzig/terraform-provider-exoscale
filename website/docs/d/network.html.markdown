---
layout: "exoscale"
page_title: "Exoscale: exoscale_network"
sidebar_current: "docs-exoscale-network"
description: |-
  Provides information about a Private Network.
---

# exoscale\_network

Provides information on a [Private Network][privnet-doc] for use in other resources such as a [`exoscale_instance_pool`][r-instance_pool] resource.

!> **WARNING:** This data source is deprecated and will be removed in the next major version.


## Example Usage

```hcl
locals {
  zone = "ch-gva-2"
}

data "exoscale_network" "db" {
  zone = local.zone
  name = "db"
}

data "exoscale_compute_template" "ubuntu" {
  zone = local.zone
  name = "Linux Ubuntu 20.04 LTS 64-bit"
}

resource "exoscale_instance_pool" "webservers" {
  zone               = local.zone
  name               = "webservers"
  template_id        = data.exoscale_compute_template.ubuntu.id
  size               = 5
  service_offering   = "medium"
  network_ids        = [data.exoscale_network.db.id]
}
```


## Arguments Reference

* `zone` - (Required) The [zone][zone] of the Private Network
* `name` - The name of the Private Network (conflicts with `id`)
* `id` - The ID of the Private Network (conflicts with `name`)



## Attributes Reference

In addition to the arguments listed above, the following attributes are exported:

* `description` - The description of the Private Network
* `start_ip` - The first address of IP range used by the DHCP service to automatically assign (for *managed* Private Networks)
* `end_ip` - The last address of the IP range used by the DHCP service (for *managed* Private Networks)
* `netmask` - The netmask defining the IP network allowed for the static lease (for *managed* Private Networks)


[r-instance_pool]: ../r/instance_pool.html
[privnet-doc]: https://community.exoscale.com/documentation/compute/private-networks/
[zone]: https://www.exoscale.com/datacenters/

