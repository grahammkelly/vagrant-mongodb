VAGRANT_API_VER = "2"
# All Vagrant configuration is done below. The "2" above
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

VM_NAME = "mongo-test-server"
Vagrant.configure(VAGRANT_API_VER) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = VM_NAME

  config.vm.network "forwarded_port", guest: 27017, host: 27017
  config.vm.network "forwarded_port", guest: 28017, host: 28017

  # config.vm.network "private_network", ip: "192.168.33.12"
  # config.vm.network "public_network"

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  config.vm.synced_folder "#{Dir.pwd}/data", "/vagrant_data"

  config.vm.provider "virtualbox" do |vb|
  	# vb.memory = "1024"
    vb.name = VM_NAME
  end

  config.vm.provision "shell" do |s|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    s.inline = <<-SHELL
      echo "Ensuring the English locale is available"
      sudo locale-gen en_IE.UTF-8

      # Create the SSH keys to allow direct SSH onto the box (not through vagrant)
      echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /root/.ssh/authorized_keys

      # Set up MongoDB on the box
      cd /opt && \
	    wget http://nodejs.org/dist/v0.10.31/node-v0.10.31-linux-x64.tar.gz && \
	    tar zxf node-v0.10.31-linux-x64.tar.gz && \
	    ln -s /opt/node-v0.10.31-linux-x64/bin/node /usr/local/bin/node && \
	    ln -s /opt/node-v0.10.31-linux-x64/bin/node-waf /usr/local/bin/node-waf && \
	    ln -s /opt/node-v0.10.31-linux-x64/bin/npm /usr/local/bin/npm
    
	    apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10 && \
	    echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee /etc/apt/sources.list.d/mongodb.list && \
	    apt-get update && \
	    apt-get install mongodb-org=2.6.4 mongodb-org-server=2.6.4 mongodb-org-shell=2.6.4 mongodb-org-mongos=2.6.4 mongodb-org-tools=2.6.4 && \
	    echo "mongodb-org hold" | dpkg --set-selections && \
	    echo "mongodb-org-server hold" | dpkg --set-selections && \
	    echo "mongodb-org-shell hold" | dpkg --set-selections && \
	    echo "mongodb-org-mongos hold" | dpkg --set-selections && \
	    echo "mongodb-org-tools hold" | dpkg --set-selections && \
	    sed -i '/bind_ip = 127.0.0.1/,/bind_ip\ =\ 127\.0\.0\.1/s/^/#/' /etc/mongod.conf && \
	    service mongod restart
    SHELL
  end
end
