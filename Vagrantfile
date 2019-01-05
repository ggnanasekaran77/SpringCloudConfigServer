# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "centos/7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  config.vm.provision "shell", inline: <<-SHELL
  	echo  "Disabling IPv6"
  	echo "net.ipv6.conf.all.disable_ipv6 = 1
      	net.ipv6.conf.default.disable_ipv6 = 1
  	net.ipv6.conf.lo.disable_ipv6 = 1
  	net.ipv6.conf.eth0.disable_ipv6 = 1" >> /etc/sysctl.conf
  	sysctl -p
  	sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
	systemctl restart sshd.service
	yum update -y 
	yum install bind-utils net-tools git wget -y
	export WORKSPACE=/home/vagrant/workspace
	rm -rf ${WORKSPACE}
	mkdir -p ${WORKSPACE}
	git clone https://github.com/spring-guides/gs-centralized-configuration.git ${WORKSPACE}/gs-centralized-configuration
	mkdir -p ${WORKSPACE}/gs-centralized-configuration/initial/configuration-service/src/main/java/hello
  mkdir -p ${WORKSPACE}/gs-centralized-configuration/initial/configuration-service/src/main/resources
  mkdir -p ${WORKSPACE}/gs-centralized-configuration/initial/configuration-client/src/main/java/hello
  mkdir -p ${WORKSPACE}/gs-centralized-configuration/initial/configuration-client/src/main/resources
  mkdir -p ${WORKSPACE}/config
	cat >${WORKSPACE}/gs-centralized-configuration/initial/configuration-service/src/main/java/hello/ConfigServiceApplication.java<<END
package hello;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@EnableConfigServer
@SpringBootApplication
public class ConfigServiceApplication {

  public static void main(String[] args) {
    SpringApplication.run(ConfigServiceApplication.class, args);
	}
}
END

  cat >${WORKSPACE}/gs-centralized-configuration/initial/configuration-service/src/main/resources/application.properties<<END
server.port=8888
spring.cloud.config.server.git.uri=/home/vagrant/workspace/config
END

  cat >${WORKSPACE}/gs-centralized-configuration/initial/configuration-client/src/main/resources/bootstrap.properties<<END
spring.application.name=a-bootiful-client
# N.B. this is the default:
spring.cloud.config.uri=http://localhost:8888
END

  cat >${WORKSPACE}/gs-centralized-configuration/initial/configuration-client/src/main/resources/application.properties<<END
management.endpoints.web.exposure.include=*
END
  cat >${WORKSPACE}/gs-centralized-configuration/initial/configuration-client/src/main/java/hello/ConfigClientApplication.java<<END
package hello;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class ConfigClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClientApplication.class, args);
    }
}

@RefreshScope
@RestController
class MessageRestController {

    @Value("${message:Hello default}")
    private String message;

    @RequestMapping("/message")
    String getMessage() {
        return this.message;
    }
}
END

  cat>/home/vagrant/workspace/config/a-bootiful-client.properties<<END
message = Hello world
END

  chown -R vagrant:vagrant -R ${WORKSPACE}
  wget --no-check-certificate -c --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.tar.gz
  SHELL
end

