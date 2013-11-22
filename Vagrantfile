# encoding: utf-8

Vagrant.configure('2') do |config|

  # 
  # BOX
  # 

  # *** Chef 11.4.0 ***
  # system_chef_solo: '/opt/chef/bin/chef-solo'
  config.vm.box = 'omnibus'
  #config.vm.box_url = 'https://s3.amazonaws.com/gsc-vagrant-boxes/ubuntu-12.04-omnibus-chef.box'
  config.vm.box_url = 'http://files.vagrantup.com/precise32.box'

  # How to upgrade Chef on vagrant boxes
  # http://stackoverflow.com/questions/11325479/how-to-control-the-version-of-chef-that-vagrant-uses-to-provision-vms




  # 
  # NETWORK  
  #

  config.vm.network :private_network, ip: '10.255.255.20'
  config.ssh.forward_agent = true

  # NFS - speedup file synchronization vs standard mode 
  config.vm.synced_folder '~/work/odesk/HIPS/HISP-Vagrant/code/', '/home/vagrant/code/', nfs: true
  # config.vm.synced_folder '~/code', '/home/vagrant/code', :nfs => true

  # Webrick
  config.vm.network :forwarded_port, guest: 3000, host: 3001
  # PostgreSQL
  # config.vm.network :forwarded_port, guest: 5432, host: 5432
  # config.vm.network :forwarded_port, guest: 9292, host: 9292


  config.vm.network :forwarded_port, guest: 143, host: 9143 #dovecot imap
  config.vm.network :forwarded_port, guest: 20000, host: 20000 #sogo
  config.vm.network :forwarded_port, guest: 80, host: 8080 #nginx

  # VM Box memory customization (default value may vary - depend on box type)
  config.vm.provider :virtualbox do |vb|
    vb.customize ['modifyvm', :id, '--memory', 1024]
  end

  # 
  # CHEF
  # 

  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ['cookbooks']
    chef.add_recipe 'apt'
    chef.add_recipe 'git'
    chef.add_recipe 'rvm::vagrant'
    chef.add_recipe 'rvm::system'

    chef.add_recipe 'memcached'

    chef.add_recipe 'nginx'
    chef.add_recipe 'hostname'
    chef.add_recipe 'postfix'
    chef.add_recipe 'postfix::postgres_setup'
    chef.add_recipe 'dovecot'
    chef.add_recipe 'sogo'

    chef.add_recipe 'postgresql::server'
    chef.add_recipe 'oh_my_zsh'
    chef.add_recipe 'oh_my_zsh'
    chef.add_recipe 'locale'
    chef.add_recipe 'tmux'

    chef.json = {
      nodejs: {
        version: '0.10.12'
      },
      rvm: {
        vagrant: {
          # default path in new vagrant boxes is '/opt/vagrant_ruby/bin/chef-solo'
          # see cookbokes/rvm/attributes/vargant.rb
          # but third-party boxes use different path to chef,
          # so we need provide correct path specific to used box
          system_chef_solo: '/opt/chef/bin/chef-solo'
        },
        default_ruby: 'ruby-2.0.0-p195'
      },
      postfix: {
        master:{
          submission: true
        },
        main: {
          mydestination: 'localdomain, localhost, localhost.localdomain, localhost, pelesecure.com'
        }
      },
      set_fqdn: 'mail.pelesecure.com',
      postgresql: {
        config: {
          listen_addresses: '*',
          port:             '5432'
        },
        pg_hba: [
          {
            type:   'local',
            db:     'postgres',
            user:   'postgres',
            addr:   nil,
            method: 'trust'
          },
          {
            type:   'host',
            db:     'all',
            user:   'all',
            addr:   '0.0.0.0/0',
            method: 'md5'
          },
          {
            type:   'host',
            db:     'all',
            user:   'all',
            addr:   '::1/0',
            method: 'md5'
          },
          {
            type: 'host',
            db: 'sogo',
            user: 'sogo',
            addr: '127.0.0.1/32',
            method: 'md5'
          }
        ],
          password: {
          postgres: 'password'
        }
      },
      tmux:{
        session_opts: {
          prefix: 'C-b'
        }
      },
      oh_my_zsh: {
        users: [{
          login: 'vagrant',
          theme: 'mortalscumbag',
          plugins: ['gem', 'git', 'rails3', 'redis-cli', 'ruby', 'heroku', 'rake', 'rvm', 'capistrano']
        }]
      },
      dovecot: {
        services: {
          auth: {
            listeners:{
              "unix:auth_userdb" => {
                :path => "/var/spool/postfix/private/auth",
                :mode => "0660",
                :user => "postfix",
                :group => "postfix"
              }
            }
          }
        },
       protocols: {
         imap: {}
       },
       conf: {
         first_valid_uid: 200,
         auth_debug: true,
         auth_debug_passwords: true,
         auth_verbose_passwords: 'plain',
         log_path: '/var/log/dovecot.log',
         disable_plain_text_auth: false,
         auth_mechanisms: 'plain',
         ssl: true,
         sql: {
           driver: 'pgsql',
           connect: 'host=localhost dbname=sogo user=sogo password=hispmail',
           default_pass_scheme: 'SSHA512',
           password_query: "SELECT c_uid as user, c_password as password, 'maildir:/home/mail/'||maildir as userdb_mail FROM sogo_users WHERE c_uid = '%u'"
         },
         mail_uid: 200,
         mail_gid: 4000
       },
       auth: {
         sql: {
           passdb: {
             driver: 'sql',
             args: '/etc/dovecot/dovecot-sql.conf.ext'
           },
           userdb: {
             driver: 'prefetch'
           }
         }
       }
      }
    }
  end
end
