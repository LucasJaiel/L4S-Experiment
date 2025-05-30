Vagrant.configure("2") do |config|
  # VM Client L4S (Subnet 192.168.55.0/24)
  config.vm.define "client" do |client|
    client.vm.box = "ubuntu/focal64"
    client.vm.hostname = "client"
    client.vm.network "private_network",
      type: "static",
      ip: "192.168.56.10",
      virtualbox__intnet: "rede_client_router"

    client.vm.provision "shell", inline: <<-SHELL
      sudo ip route add default via 192.168.56.2
      sudo sysctl -w net.ipv4.tcp_ecn=3
      sudo ethtool -K enp0s8 tso off gso off gro off
      sudo tc qdisc replace dev enp0s8 root handle 1: fq limit 20480 flow_limit 10240
    SHELL
  end

  # VM Classic Client (Subnet 192.168.55.0/24)
  config.vm.define "classic-client" do |classic_client|
    classic_client.vm.box = "ubuntu/focal64"
    classic_client.vm.hostname = "classic-client"
    classic_client.vm.network "private_network",
      type: "static",
      ip: "192.168.55.10",
      virtualbox__intnet: "rede_classic_client_router"

    classic_client.vm.provision "shell", inline: <<-SHELL
      sudo ip route add default via 192.168.55.2
      sudo ip link set enp0s8 up
      sudo sysctl -w net.ipv4.tcp_ecn=0
    SHELL
  end

  # VM Malicious Client (Nova subnet 192.168.54.0/24)
  config.vm.define "malicious-client" do |malicious_client|
    malicious_client.vm.box = "ubuntu/focal64"
    malicious_client.vm.hostname = "malicious-client"
    malicious_client.vm.network "private_network",
      type: "static",
      ip: "192.168.54.10",
      virtualbox__intnet: "rede_malicious_client_router"

    malicious_client.vm.provision "shell", inline: <<-SHELL
      sudo ip route add default via 192.168.54.2
      sudo ip link set enp0s8 up
      sudo sysctl -w net.ipv4.tcp_ecn=0
      sudo iptables -t mangle -A POSTROUTING -j TOS --set-tos 0x01/0x03
    SHELL
  end

  # VM Router
  config.vm.define "router" do |router|
    router.vm.box = "ubuntu/focal64"
    router.vm.hostname = "router"

    #Interface Clients-Router
    #Interface Config(Malicious-Client)
    router.vm.network "private_network",
      type: "static",
      ip: "192.168.54.2",
      virtualbox__intnet: "node_malicious_client_router"

    #Interface Config(Classic-Client)
    router.vm.network "private_network",
      type: "static",
      ip: "192.168.55.2",
      virtualbox__intnet: "node_classic_client_router"

    # Interface Config(L4S-Client)
    router.vm.network "private_network",
      type: "static",
      ip: "192.168.56.2",
      virtualbox__intnet: "node_l4s_client_router"

    # Interface SERVERS
    router.vm.network "private_network",
      type: "static",
      ip: "192.168.57.2",
      virtualbox__intnet: "nodee_router_server"

    router.vm.provision "shell", inline: <<-SHELL
      # Enable Routing
      echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
      sudo sysctl -w net.ipv4.ip_forward=1

      # Configuring interfaces for Clients
      sudo tc qdisc replace dev enp0s8 root dualpi2 
      sudo tc qdisc replace dev enp0s9 root dualpi2 
      sudo tc qdisc replace dev enp0s10 root dualpi2 

      # Interface (Servers and bottlenack - 100Mbit)
      sudo tc qdisc replace dev enp0s16 root handle 1: htb default 10
      sudo tc class add dev enp0s16 parent 1: classid 1:10 htb rate 100Mbit ceil 100Mbit burst 1516
      sudo tc qdisc add dev enp0s16 parent 1:10 dualpi2 
    SHELL
  end

  # VM Servers
  # VM L4S server (Subnet 192.168.57.0/24)
  config.vm.define "server" do |server|
    server.vm.box = "ubuntu/focal64"
    server.vm.hostname = "server"
    server.vm.network "private_network",
      type: "static",
      ip: "192.168.57.10",
      virtualbox__intnet: "node_router_server"

    server.vm.provision "shell", inline: <<-SHELL
      sudo ip route add default via 192.168.57.2
      sudo ip link set enp0s8 up
      sudo sysctl -w net.ipv4.tcp_ecn=3
      sudo ethtool -K enp0s8 tso off gso off gro off
      sudo tc qdisc replace dev enp0s8 root handle 1: fq limit 20480 flow_limit 10240
    SHELL
  end

  # VM Classic Server
  config.vm.define "classic-server" do |classic_server|
    classic_server.vm.box = "ubuntu/focal64"
    classic_server.vm.hostname = "classic-server"
    classic_server.vm.network "private_network",
      type: "static",
      ip: "192.168.57.20",
      virtualbox__intnet: "node_router_server"

    classic_server.vm.provision "shell", inline: <<-SHELL
      sudo ip route add default via 192.168.57.2
      sudo ip link set enp0s8 up
    SHELL
  end
end
