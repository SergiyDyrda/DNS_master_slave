
$installbindcript = <<SCRIPT
apt-get install -y bind9
rm /etc/bind/named.conf.options
rm /etc/bind/named.conf.local
SCRIPT

$movefilesmaster = <<SCRIPT
mv /tmp/named.conf.options /etc/bind/named.conf.options
mv /tmp/named.conf.local /etc/bind/named.conf.local
mv /tmp/organization.ua.db /var/lib/bind/organization.ua.db
mv /tmp/192.168.1.db /var/lib/bind/192.168.1.db
SCRIPT

$movefilesslave = <<SCRIPT
mv /tmp/named.conf.options /etc/bind/named.conf.options
mv /tmp/named.conf.local /etc/bind/named.conf.local
SCRIPT


$restartbindcript = <<SCRIPT
/etc/init.d/bind9 restart
SCRIPT

Vagrant.configure("2") do |config|

 config.vm.provider "virtualbox" do |v|
    v.linked_clone = true
    v.customize ["modifyvm", :id, "--memory", "512", "--cpus", "1"]
 end

 config.vm.define "master" do |master|
    master.vm.box = "ubuntu/trusty64"
    master.vm.network :private_network, ip: "192.168.1.4"
    master.vm.hostname = "master"
    master.vm.provision "shell", inline: $installbindcript
    master.vm.provision "file", source: "master/named.conf.options", destination: "/tmp/named.conf.options"
    master.vm.provision "file", source: "master/named.conf.local", destination: "/tmp/named.conf.local"
    master.vm.provision "file", source: "master/organization.ua.db", destination: "/tmp/organization.ua.db"
    master.vm.provision "file", source: "master/192.168.1.db", destination: "/tmp/192.168.1.db"
    master.vm.provision "shell", inline: $movefilesmaster
    master.vm.provision "shell", inline: $restartbindcript
  end

  config.vm.define "slave" do |sl|
   sl.vm.box = "ubuntu/trusty64"
   sl.vm.hostname = "slave"
   sl.vm.network :private_network, ip: "192.168.1.5"
   sl.vm.provision "shell", inline: $installbindcript
   sl.vm.provision "file", source: "master/named.conf.options", destination: "/tmp/named.conf.options"
   sl.vm.provision "file", source: "slave/named.conf.local", destination: "/tmp/named.conf.local"
   sl.vm.provision "shell", inline: $movefilesslave
   sl.vm.provision "shell", inline: $restartbindcript
 end

 config.vm.define "client" do |cl|
   cl.vm.box = "ubuntu/trusty64"
   cl.vm.hostname = "client"
   cl.vm.network :private_network, ip: "192.168.1.6"
 end
end
