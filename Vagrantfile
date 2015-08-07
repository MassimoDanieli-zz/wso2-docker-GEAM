VAGRANTFILE_API_VERSION = "2"
VAGRANT_NO_PARALLEL = "yes"
Vagrant.require_version ">= 1.6.3"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "default" do |default|
    default.vm.box = "yungsang/boot2docker"
    default.vm.network "private_network", ip: "192.168.110.110"
    # console port: ESB, API Manager, Application Server
    default.vm.network "forwarded_port", guest: 9443, host: 9443
    default.vm.network "forwarded_port", guest: 9444, host: 9444
    default.vm.network "forwarded_port", guest: 9445, host: 9445
	default.vm.network "forwarded_port", guest: 9446, host: 9446
    #  API MAnager; NIO/PT transport ports
    default.vm.network "forwarded_port", guest: 8243, host: 8243
    default.vm.network "forwarded_port", guest: 8245, host: 8245
    # jenkins
    default.vm.network "forwarded_port", guest: 80, host: 80
    default.vm.network "forwarded_port", guest: 50000, host: 50000
    # HTTP servlet transport: ESB, API Manager, Application Server
    default.vm.network "forwarded_port", guest: 9763, host: 9763
    default.vm.network "forwarded_port", guest: 9764, host: 9764
    default.vm.network "forwarded_port", guest: 9765, host: 9765
	# MYSQL just in case
	default.vm.network "forwarded_port", guest: 3306, host: 3306
    default.vm.synced_folder ".", "/var/www", type: "nfs"

    default.vm.provider "virtualbox" do |virtualbox|
      virtualbox.memory = 4096

    end

    default.vm.provision "docker" do |docker|
     # docker.build_image "/var/www/config/environment/docker/nginx", args: "-t vagrantdocker/nginx"
     # docker.build_image "/var/www/config/environment/docker/phpfpm", args: "-t vagrantdocker/phpfpm"
    end


  end

  ##
  # A pretty simple scenario. EBS is listening on port 9443, GREG on  9444 and APIM on 9445
  ##

# MYSQL
   config.vm.define "mysql", autostart: true do |mysql|
     mysql.vm.provider "docker" do |docker|
	 docker.image = "mysql"
	 docker.name = "mysql"
	 docker.ports = %w(3306:3306)
	 
	 docker.env = {
	 MYSQL_ROOT_PASSWORD: "my-secret-pw", 
	 MYSQL_DATABASE: "REGISTRY", 
	 MYSQL_USER: "greg_user", 
	 MYSQL_PASSWORD: "sql4you"}
	 
	 docker.remains_running = true
	 
	docker.vagrant_vagrantfile = __FILE__
    end
  end 
  
### ESB
  config.vm.define "esb", autostart: true do |esb|
    esb.vm.provider "docker" do |docker|
      docker.image = "massimodanieli/wso2esb-mysqlh"
	    docker.name = "wso2esb"
      docker.ports = %w(9443:9443, 9763:9763, 8280:8280, 8243:8243, 9000:9000, 9002:9002)
  	  docker.link("mysql:db")
      docker.vagrant_vagrantfile = __FILE__
    end
  end

  #### GREG
 
  config.vm.define "greg", autostart: true do |greg|
    greg.vm.provider "docker" do |docker|
      docker.image = "massimodanieli/wso2grh"
	    docker.name = "wso2greg"
      docker.ports = %w(9444:9443)
  	  docker.link("mysql:db")
	    docker.vagrant_vagrantfile = __FILE__
    end
  end

  ### AS Manager
config.vm.define "as", autostart: true do |as|
    as.vm.provider "docker" do |docker|
      docker.image = "massimodanieli/wso2as"
	    docker.name = "wso2as"
      docker.ports = %w(9445:9443, 9764:9763, 8281:8280, 8244:8243)
      docker.vagrant_vagrantfile = __FILE__
    end
  end



end
