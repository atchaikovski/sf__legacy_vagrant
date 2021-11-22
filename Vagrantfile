Vagrant.configure(2) do |config|
  config.ssh.username = 'devops'
  config.ssh.forward_agent = true
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider :vsphere do |vsphere|
    vsphere.host = 'vcsa'
    vsphere.compute_resource_name = 'AlexCluster'
    vsphere.resource_pool_name = ''
    vsphere.vm_base_path = ''
    vsphere.data_store_name = 'flash'
    vsphere.customization_spec_name = 'centos7'
    vsphere.template_name = 'templates/base-centos-7'
    vsphere.user = 'Administrator@alex.local'
    vsphere.password = 'P@sw0rd1'
    vsphere.insecure = true
  end

$script = <<-SHELL
export LANGUAGE=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8

### VERSION 8.4 found in sources
#sudo yum install -y -q wget bison flex readline-devel zlib-devel
#sudo yum install -y -q gcc gcc-c++ make binutils
#wget --no-check-certificate https://ftp.postgresql.org/pub/source/v8.4.4/postgresql-8.4.4.tar.gz
#tar zxvf postgresql-8.4.4.tar.gz
#sudo rm postgresql-8.4.4.tar.gz
#cd postgresql-8.4.4
#./configure
#sudo gmake
#sudo gmake install
#sudo gmake clean
#LD_LIBRARY_PATH=/usr/local/pgsql/lib
#export LD_LIBRARY_PATH
#sudo /sbin/ldconfig

# a bit of hack with repos
sudo yum install -y net-tools
sudo sh -c 'cat << EOF >/etc/yum.repos.d/pgdg-80.repo
[pgdg80]
name=PostgreSQL 8.0 RPMs for RHEL/CentOS 6
baseurl=https://yum-archive.postgresql.org/8.4/redhat/rhel-6-x86_64
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-PGDG 
EOF'
sudo useradd postgres
sudo sh -c 'echo "postgres ALL=(ALL) NOPASSWD: ALL" >>/etc/sudoers.d/postgres'
su - postgres
cd 
sudo yum install -y postgresql84-server
sudo sh -c 'echo "PGDATA=/var/lib/pgsql/8.4/data" >>/etc/sysconfig/pgsql/postgresql'
sudo sh -c 'echo "PATH=$PATH:/usr/pgsql-8.4/bin" >>~/.bash_profile'
sudo service postgresql-8.4 initdb
su root 
chkconfig postgresql-8.4 on
 
#DATA="/var/lib/pgsql/8.4/data"
#BIN="/usr/pgsql-8.4/bin"

SHELL

    config.vm.define "PG" do |master|
        master.vm.box = "dummy.box"
        master.vm.box_url = "dummy.box"
        master.vm.network "public_network", ip: "192.168.0.111"
        #master.vm.network "private_network", type: "dhcp"
        master.vm.hostname = "pg-old"
        master.vm.provision "shell", inline: $script
    end
end
