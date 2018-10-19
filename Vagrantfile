# Install required plugins
required_plugins = ["vagrant-hostsupdater"]
required_plugins.each do |plugin|
    exec "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end

def set_env vars
 	command = <<~HEREDOC
 	echo "Setting Environment Variables"
 	source ~/.bashrc
 	HEREDOC

 	vars.each do |key, value|
 	command += <<~HEREDOC
 	if [ -z "$#{key}" ]; then
 	echo "export #{key}=#{value}" >> ~/.bashrc
 	fi
 	HEREDOC
 	end

 	return command
 	end

Vagrant.configure("2") do |config|

    config.vm.define "app" do |app|
      app.vm.box = "ubuntu/xenial64"
      app.vm.network "private_network", ip: "192.168.10.180"
      app.vm.provision "shell", inline: set_env({DB_HOST:"mongodb://192.168.10.180:27017/posts"}), privileged: false
      app.vm.synced_folder ".","/app"
      app.vm.provision "shell", path: "environment/app/provision.sh", privileged: false
    end

    config.vm.define "db" do |db|
      db.vm.box = "ubuntu/xenial64"
      db.vm.network "private_network", ip: "192.168.10.150"
      db.vm.synced_folder ".","/db"
      db.vm.provision "shell", path: "environment/db/provision.sh", privileged: false
    end
end
