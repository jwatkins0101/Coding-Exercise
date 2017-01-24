# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "bento"
  config.vm.box_url = "https://atlas.hashicorp.com/bento/boxes/ubuntu-16.04"

  config.vm.network :forwarded_port, guest: 80, host: 4311
  config.vm.network :private_network, ip: "192.168.43.11"

  config.vm.provider :virtualbox do |vb|
    vb.name = "coding.app"
    vb.customize ["modifyvm", :id, "--memory", "2048"]
    vb.customize ["modifyvm", :id, "--ostype", "Ubuntu_64"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
end
  config.vm.provision "shell", inline: <<-shell

  	hostname coding.app
  	apt-get install python-software-properties software-properties-common
  	add-apt-repository ppa:ondrej/php
  	apt-get update
	apt-get -y install nginx
	echo debconf-set-selections mysql-server mysql-server/root_password password password
    echo debconf-set-selections mysql-server mysql-server/root_password_again password password
    apt-get -y install mysql-server-5.6 mysql-client-5.6
    mysql -uroot -ppassword -e "create database coding"
	apt-get install php7.0 php7.0-fpm php7.0-mysql php7.0-mbstring php7.0-dom php7.0-zip -y
	cp /vagrant/default /etc/nginx/sites-available/default
	sed -i 's/www-data/vagrant/g' /etc/php/7.0/fpm/pool.d/www.conf
	sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' /etc/php/7.0/fpm/php.ini
	sed -i 's/www-data/vagrant/g' /etc/nginx/nginx.conf
	sed -i 's/sendfile on;/sendfile off;/g' /etc/nginx/nginx.conf
	curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
	cp /vagrant/.env.example /vagrant/.env
	cd /vagrant && composer install
	cd /vagrant && php artisan key:generate
	service php7.0-fpm restart
	service nginx restart
  shell
end