# Check to determine whether we're on a windows or linux/os-x host,
# later on we use this to launch ansible in the supported way
# source: https://stackoverflow.com/questions/2108727/which-in-ruby-checking-if-program-exists-in-path-from-ruby
def which(cmd)
    exts = ENV['PATHEXT'] ? ENV['PATHEXT'].split(';') : ['']
    ENV['PATH'].split(File::PATH_SEPARATOR).each do |path|
        exts.each { |ext|
            exe = File.join(path, "#{cmd}#{ext}")
            return exe if File.executable? exe
        }
    end
    return nil
end


Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/trusty64"
    config.vm.hostname = "vanillaforum2.1"
    config.vm.network "forwarded_port", guest: 80, host: 8080
    config.vm.network "forwarded_port", guest: 3306, host: 3306
    config.vm.provider "virtualbox" do |vb|
        vb.name = config.vm.hostname
        vb.memory = 512
        vb.cpus = 1
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
   
    config.ssh.forward_agent = true

    config.vm.synced_folder "./", "/vagrant"
	if Vagrant.has_plugin?("vagrant-exec")
        config.exec.commands '*', prepend: 'sudo'
    end
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
  
    
    
    # Ansible provisioning
    #keeping this for when ansible_local is reliable
    # Should be able to remove this when vagrant 1.8.2 is available
    #Got pathing problem fixed using the technique described at https://github.com/geerlingguy/drupal-vm/issues/361
    #From above:
        # "My patched provisioner for ansible that is located at C:\Vagrant\embedded\gems\gems\vagrant-1.8.1\plugins\provisioners is at
        # https://github.com/geerlingguy/drupal-vm/files/103996/ansible.zip"
    config.vm.provision :ansible_local do |ansible|
        ansible.playbook = "/vagrant/ansible/playbook.yml"
        # ansible.verbose        = true
        ansible.install = true
        # This should work, but the value isn't being passed in
        # ansible.extra_vars = {hostname: config.vm.hostname, var2: 'nothing'}
        ansible.limit = "all"
   end    
end
