Vagrant.configure("2") do |config|
    # Ubuntu 20.04 LTS
    config.vm.box = "bento/ubuntu-20.04"

    # Hostname
    config.vm.hostname = "server.local"

    # Preferred providers
    config.vm.provider "hyperv"
    config.vm.provider "virtualbox"

    # Disks
    config.vm.disk :disk, name: "data", size: "100GB"

    # Network
    config.vm.network "private_network", ip: "192.168.25.25"
end