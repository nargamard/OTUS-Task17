ENV["LC_ALL"] = "en_US.UTF-8"
ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'

# Описание параметров ВМ
MACHINES = {
  # Имя DV "pam"
  :backup => {
        # VM box
        :box_name => "ubuntu/jammy64",
        # Имя VM
        :vm_name => "backup",
        # Количество ядер CPU
        :cpus => 2,
        # Указываем количество ОЗУ (В Мегабайтах)
        :memory => 2048,
        # Указываем IP-адрес для ВМ
        :ip => "192.168.56.10",
  },
  :client => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "client",
        :cpus => 2,
        :memory => 2048,
        :ip => "192.168.56.11",

  },
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|

      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]
      box.vm.network "private_network", ip: boxconfig[:ip]
      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end

      # Запуск ansible-playbook
      if boxconfig[:vm_name] == "backup"
       box.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/provision_backup.yml"
        ansible.inventory_path = "ansible/hosts"
        ansible.host_key_checking = "false"
        ansible.limit = "all"
       end
      end

      # Запуск ansible-playbook
      if boxconfig[:vm_name] == "client"
        box.vm.provision "ansible" do |ansible|
         ansible.playbook = "ansible/provision_client.yml"
         ansible.inventory_path = "ansible/hosts"
         ansible.host_key_checking = "false"
         ansible.limit = "all"
        end
       end


    end
  end
end
