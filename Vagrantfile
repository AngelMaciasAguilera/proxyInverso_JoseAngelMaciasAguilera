Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "256"
  end
  
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update && apt-get install -y nginx
    sudo apt install -y curl
    sudo rm /etc/nginx/sites-available/default
    sudo rm /etc/nginx/sites-enabled/default
  SHELL
  
  config.vm.define "proxy" do |proxy|
    proxy.vm.hostname = "www.example.test"
    proxy.vm.network "private_network", ip: "192.168.57.12"
    proxy.vm.provision "shell", inline: <<-SHELL
      sudo cp -v /vagrant/nginx_provision/proxy /etc/nginx/sites-available/
      sudo ln -s /etc/nginx/sites-available/proxy /etc/nginx/sites-enabled/
      sudo systemctl restart nginx
    SHELL
  end
  
  config.vm.define "web" do |web|
    web.vm.hostname = "w1.example.test"
    web.vm.network "private_network", ip: "192.168.57.11"
    web.vm.network "forwarded_port", guest: 8080, host: 8080
    web.vm.provision "shell", inline: <<-SHELL
      sudo mkdir -p /var/www/web_server/html
      sudo chown -R www-data:www-data /var/www/web_server/html
      sudo cp -vr /vagrant/nginx_provision/index.html /var/www/web_server/html
      sudo chmod -R 755 /var/www/web_server
      sudo systemctl restart nginx
      sudo cp -v /vagrant/nginx_provision/server /etc/nginx/sites-available/
      sudo ln -s /etc/nginx/sites-available/server /etc/nginx/sites-enabled/
      sudo systemctl restart nginx
    SHELL
  end  
end