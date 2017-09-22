tomcat_boxes = %w(dev tst acc1 acc2 prd1 prd2 prd3 prd4)
nginx_boxes = %w(nginx-acc nginx-prd)

tomcat_acc_boxes = tomcat_boxes.select { |i| i.start_with?("acc") }
tomcat_prd_boxes = tomcat_boxes.select { |i| i.start_with?("prd") }

Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'
  config.vm.network 'private_network', type: 'dhcp', name: 'vboxnet0', adapter: 2
  config.vm.synced_folder '.', '/vagrant', disabled: true
  config.vm.provider 'virtualbox' do |vb|
    vb.memory = '2048'
  end

  #config.proxy.http = 'http://192.168.122.1:3128'
  #config.proxy.https = 'http://192.168.122.1:3128'
  #config.proxy.no_proxy = '.local,localhost,127.0.0.1'

  tomcat_boxes.each_with_index do |box|
    config.vm.define box.to_s do |node|
      node.vm.hostname = box.to_s
      node.vm.provision 'ansible' do |ansible|
        ansible.limit = box.to_s
        ansible.verbose = false
        ansible.playbook = 'provisioning/tomcat-playbook.yml'
      end
    end
  end

  nginx_boxes.each_with_index do |box|
    config.vm.define box.to_s do |node|
      node.vm.hostname = box.to_s
      node.vm.provision 'ansible' do |ansible|
        ansible.limit = box.to_s
        ansible.verbose = false
        ansible.extra_vars = { 'tomcat_acc_list' => tomcat_acc_boxes, 'tomcat_prd_list' => tomcat_prd_boxes }
        ansible.playbook = 'provisioning/nginx-playbook.yml'
      end
    end
  end
end
