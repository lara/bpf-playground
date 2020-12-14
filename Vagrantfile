# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$bootstrap = <<-SCRIPT
export DEBIAN_FRONTEND=noninteractive

sudo apt -y update
sudo apt -y dist-upgrade
sudo apt install -y bison build-essential cmake flex git libedit-dev \
  libllvm7 llvm-7-dev libclang-7-dev python zlib1g-dev libelf-dev libfl-dev

git clone https://github.com/iovisor/bcc.git
git clone https://github.com/iovisor/bpftrace.git

mkdir bcc/build; cd bcc/build
cmake ..
make
sudo make install
cmake -DPYTHON_CMD=python3 .. # build python3 binding
pushd src/python/
make
sudo make install
popd

# sudo apt-get install -y bpftrace

sudo apt-get update
sudo apt-get install -y bison cmake flex g++ git libelf-dev zlib1g-dev libfl-dev systemtap-sdt-dev binutils-dev
sudo apt-get install -y llvm-7-dev llvm-7-runtime libclang-7-dev clang-7

cd $HOME
sudo mkdir bpftrace/build; cd bpftrace/build;
cmake -DCMAKE_BUILD_TYPE=Release ..
make
sudo make install
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.hostname = "bpf-playground"
  config.vm.provider("virtualbox") { |virtual_box| virtual_box.customize ["modifyvm", :id, "--memory", "1024"] }
  config.vm.provision("shell", inline: $bootstrap)
  config.vm.post_up_message = <<-HEREDOC
#######
bpftrace source is available in /vagrant
Build command: cmake /vagrant -DCMAKE_INSTALL_PREFIX=/usr/local && make
#######
  HEREDOC
end
