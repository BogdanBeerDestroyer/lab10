# Шумилишский ИУ8-22 Лабораторная 10

Данная лабораторная работа посвещена изучению процесса создания и конфигурирования виртуальной среды разработки с использованием **Vagrant**

## Tutorial
Присваиваем значения переменным:
```sh
$ export GITHUB_USERNAME=<имя_пользователя>
$ export PACKAGE_MANAGER=<пакетный_менеджер>
```
Устанавливаем vagrant
```sh
$ cd ${GITHUB_USERNAME}/workspace
$ ${PACKAGE_MANAGER} install vagrant
```
Смотрим версию vagrand, создаем новую виртуалку, выводим vagrandfile
```sh
$ vagrant version
$ vagrant init bento/ubuntu-19.10
$ less Vagrantfile
$ vagrant init -f -m bento/ubuntu-19.10
```
```sh
$ mkdir shared
```
Записываем в файл Vagrantfile команды для запуска скрипта
```sh
$ cat > Vagrantfile <<EOF
\$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT
EOF
```
Записываем в файл Vagrantfile конфигурацию для vagrant-vbguest
```sh
$ cat >> Vagrantfile <<EOF

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
```
```sh
$ cat >> Vagrantfile <<EOF

  config.vm.box = "bento/ubuntu-19.10"
  config.vm.network "public_network"
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: \$script, privileged: true

  config.ssh.extra_args = "-tt"

end
EOF
```
"vagrant plugin install vagrant-vbguest" устраняет неполадки  
vagrant validate - проверка корректности файла Vagrantfile   
Смотрим список виртуальных машин и их статусы   
Запускаем виртуальной машины   
Информация о проброске портов  
Подключение по ssh к запущенной виртуальной машине   
Посмотреть список сохранённых состояний виртуальной машины   
Остановить виртуальную машину   
Восстановить состояние виртуальной машины по снимку
```sh
$ vagrant validate

$ vagrant status
$ vagrant up # --provider virtualbox
$ vagrant port
$ vagrant status
$ vagrant ssh

$ vagrant snapshot list
$ vagrant snapshot push
$ vagrant snapshot list
$ vagrant halt
$ vagrant snapshot pop
```
Настраиваем Vagrant для VMware
```ruby
  config.vm.provider :vmware_esxi do |esxi|

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end
```
```sh
$ vagrant plugin install vagrant-vmware-esxi
$ vagrant plugin list
$ vagrant up --provider=vmware_esxi
```

## Report

```sh
$ cd ~/workspace/
$ export LAB_NUMBER=10
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```