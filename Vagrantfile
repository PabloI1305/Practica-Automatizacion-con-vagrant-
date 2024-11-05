Vagrant.configure("2") do |config|
  # Configuración de los servidores web Apache
  (1..2).each do |i|
    config.vm.define "web#{i}" do |web|
      web.vm.box = "ubuntu/bionic64"
      web.vm.hostname = "web#{i}"
      web.vm.network "private_network", ip: "192.168.33.1#{i}"
      web.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl start apache2
        sudo systemctl enable apache2
      SHELL
      web.vm.synced_folder "./web_content", "/var/www/html"
    end
  end

  # Configuración del servidor NGINX
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "ubuntu/bionic64"
    nginx.vm.hostname = "nginx"
    nginx.vm.network "private_network", ip: "192.168.33.13"
    nginx.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    nginx.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y nginx
      sudo systemctl start nginx
      sudo systemctl enable nginx

      # Configuración de NGINX para el balanceo de carga
      sudo bash -c 'cat > /etc/nginx/conf.d/load_balancer.conf <<EOF
upstream backend {
    server 192.168.33.11;
    server 192.168.33.12;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
    }
}
EOF'
      sudo systemctl restart nginx
    SHELL
  end
end
