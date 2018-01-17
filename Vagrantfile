Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/xenial64"
    config.vm.network :forwarded_port, guest: 3000, host: 3000

    config.vm.provision "bootstrap",
    type: "shell",
    inline: <<-SHELL
        sudo apt-get update && sudo apt-get -y upgrade
        curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
        sudo apt-get install -y nodejs
        sudo apt-get install -y build-essential
        npm install docsify-cli -g
    SHELL

    # add the local user git config to the vm
    config.vm.provision "file", source: "~/.gitconfig", destination: ".gitconfig"

    # Run Docsify dev server
    # example: vagrant provision --provision-with server.
    config.vm.provision "server",
    type: "shell",
    privileged: false,
    run: "never",
    inline: <<-SHELL
        echo "===================================================="
        echo " Starting docsify server"
        cd /vagrant
        docsify serve . &> ~/docsify.log &
    SHELL
end