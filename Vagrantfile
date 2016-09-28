# -*- mode: ruby -*-
# vi: set ft=ruby :

$router_inline = <<EOF
sudo mv /home/ubuntu/hab-director.service /etc/systemd/system/hab-director.service
sudo mkdir -p /hab/etc/director
cat <<BODY > /tmp/director-config.toml
[cfg.services.core.hab-builder-router.lab]
start = "--permanent-peer"
BODY
sudo mv /tmp/director-config.toml /hab/etc/director/config.toml
sudo systemctl daemon-reload
sudo systemctl start hab-director
sudo systemctl enable hab-director
EOF

$worker_inline = <<EOF
sudo mv /home/ubuntu/hab-director.service /etc/systemd/system/hab-director.service
sudo mkdir -p /hab/etc/director
cat <<BODY > /tmp/director-config.toml
[cfg.services.core.hab-builder-worker.lab]
start = "--permanent-peer --bind jobsrv:hab-builder-jobsrv.lab --peer 192.168.50.11:9000"
BODY
sudo mv /tmp/director-config.toml /hab/etc/director/config.toml
sudo systemctl daemon-reload
sudo systemctl start hab-director
sudo systemctl enable hab-director
EOF

Vagrant.configure("2") do |config|

  config.vm.define "node-1" do |node|
    node.vm.box = "ubuntu/xenial64"
    node.vm.network "private_network", ip: "192.168.50.11"
    node.vm.provision "shell", path: "./bootstrap.sh"
    node.vm.provision "file", source: "./hab-director.service", destination: "/home/ubuntu/hab-director.service"
    node.vm.provision "shell", inline: $router_inline
  end

  (2..3).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.box = "ubuntu/xenial64"
      node.vm.network "private_network", ip: "192.168.50.1#{i}"
      node.vm.provision "shell", path: "./bootstrap.sh"
      node.vm.provision "file", source: "./hab-director.service", destination: "/home/ubuntu/hab-director.service"
      node.vm.provision "shell", inline: $worker_inline
    end
  end

end
