```
resource "yandex_compute_instance" "secvm" {
  name = "secvm"

  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd84nt41ssoaapgql97p"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.bastion-external-segment.id
    nat       = true
    security_group_ids = [yandex_vpc_security_group.secure-bastion-sg.id]
  }
  metadata = {
    user-data = "${file("/home/mpalgin/learning/terraform/meta.txt")}"
  }
}
resource "yandex_compute_instance" "webvm1" {
  name = "webvm1"
  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd84nt41ssoaapgql97p"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.bastion-internal-segment-zonea.id
    nat       = false
  }

  metadata = {
    user-data = "${file("/home/mpalgin/learning/terraform/meta.txt")}"
  }
}
resource "yandex_compute_instance" "webvm2" {
  name = "webvm2"
  zone           = "ru-central1-b"
  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd84nt41ssoaapgql97p"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.bastion-internal-segment-zoneb.id
    nat       = false
  }

  metadata = {
    user-data = "${file("/home/mpalgin/learning/terraform/meta.txt")}"
  }
}
resource "yandex_compute_instance" "zabbix-vm" {
  name = "zabbix-vm"
  zone           = "ru-central1-a"
  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd84nt41ssoaapgql97p"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.bastion-external-segment.id
    nat       = false
  }

  metadata = {
    user-data = "${file("/home/mpalgin/learning/terraform/meta.txt")}"
  }
}
resource "yandex_compute_instance" "elastic-vm" {
  name = "elastic-vm"
  zone           = "ru-central1-a"
  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd84nt41ssoaapgql97p"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.bastion-internal-segment-zonea.id
    nat       = false
  }

  metadata = {
    user-data = "${file("/home/mpalgin/learning/terraform/meta.txt")}"
  }
}
resource "yandex_compute_instance" "kibana-vm" {
  name = "kibana-vm"
  zone           = "ru-central1-a"
  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd84nt41ssoaapgql97p"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.bastion-external-segment.id
    nat       = false
  }

  metadata = {
    user-data = "${file("/home/mpalgin/learning/terraform/meta.txt")}"
  }
}

resource "yandex_vpc_network" "external-bastion-network" {
  name = "external-bastion-network"
}

resource "yandex_vpc_subnet" "bastion-external-segment" {
  name           = "bastion-external-segment"
  zone           = "ru-central1-a"
  network_id     = yandex_vpc_network.external-bastion-network.id
  v4_cidr_blocks = ["172.16.17.0/24"]
}

resource "yandex_vpc_security_group" "secure-bastion-sg"{
  name = "secure-bastion-sg"
  network_id     = yandex_vpc_network.external-bastion-network.id
  ingress{
    from_port = 22
    to_port = 22
    protocol = "tcp"
    v4_cidr_blocks = ["0.0.0.0/0"]
  }
  egress{
    from_port = 0
    to_port = 65535
    protocol = "any"
    v4_cidr_blocks = ["0.0.0.0/0"]

  }
}

resource "yandex_vpc_subnet" "bastion-internal-segment-zonea" {
  name           = "bastion-internal-segment-zonea"
  zone           = "ru-central1-a"
  network_id     = yandex_vpc_network.external-bastion-network.id
  v4_cidr_blocks = ["172.16.16.0/24"]
}
resource "yandex_vpc_subnet" "bastion-internal-segment-zoneb" {
  name           = "bastion-internal-segment-zoneb"
  zone           = "ru-central1-b"
  network_id     = yandex_vpc_network.external-bastion-network.id
  v4_cidr_blocks = ["172.16.18.0/24"]
}
resource "yandex_alb_target_group" "targetvmgroup" {
  name           = "targetvmgroup"

  target {
    subnet_id    = yandex_vpc_subnet.bastion-internal-segment-zonea.id
    ip_address   = yandex_compute_instance.webvm1.network_interface.0.ip_address
  }

  target {
    subnet_id    = yandex_vpc_subnet.bastion-internal-segment-zoneb.id
    ip_address   = yandex_compute_instance.webvm2.network_interface.0.ip_address
  }

}
resource "yandex_alb_backend_group" "web-backend-group" {
  name = "web-backend-group"
  session_affinity {
    connection {
      source_ip = false
    }
  }

  http_backend {
    name = "web-backend"
    weight = 1
    port = 80
    target_group_ids = [yandex_alb_target_group.targetvmgroup.id]
    load_balancing_config {
      panic_threshold = 90
    }    
    healthcheck {
      timeout = "10s"
      interval = "2s"
      healthy_threshold = 10
      unhealthy_threshold = 15 
      http_healthcheck {
        path = "/"
      }
    }
  }
}
resource "yandex_alb_http_router" "tf-router" {
  name          = "web-tf-router"
  labels        = {
    tf-label    = "tf-label-value"
    empty-label = ""
  }
}

resource "yandex_alb_virtual_host" "web-virtual-host" {
  name                    = "web-virtual-host"
  http_router_id          = yandex_alb_http_router.tf-router.id
  route {
    name                  = "web-route"
    http_route {
      http_route_action {
        backend_group_id  = yandex_alb_backend_group.web-backend-group.id
        timeout           = "60s"
      }
    }
  }
}  
resource "yandex_alb_load_balancer" "web-balancer" {
  name        = "web-balancer"
  network_id  = yandex_vpc_network.external-bastion-network.id

  allocation_policy {
    location {
      zone_id   = "ru-central1-a"
      subnet_id = yandex_vpc_subnet.bastion-external-segment.id
    }
  }

  listener {
    name = "web-balancer-server"
    endpoint {
      address {
        external_ipv4_address {
        }
      }
      ports = [ 80 ]
    }
    http {
      handler {
        http_router_id = yandex_alb_http_router.tf-router.id
      }
    }
  }
}
output "internal_ip_address_vm_1" {
  value = yandex_compute_instance.webvm1.network_interface.0.ip_address
}
output "external_ip_address_vm_1" {
  value = yandex_compute_instance.webvm1.network_interface.0.nat_ip_address
}
```
