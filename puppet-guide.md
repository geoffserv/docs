> This is obviously horrible but a minimum bootstrap guide to get up and experimental

- Install the puppetmater packages & do some setup
	- Ubuntu 16.04
		- Did a fresh install, apt-get update/upgrade
		- `sudo apt-get install puppetmaster`
	- DNS/hosts configuration
		- Added to /etc/hosts the IP and hostname 'puppetmaster'
		- Add 'puppet' alias to localhost
- Configure a class
	- Make a quick apache class
		- `sudo mkdir -p /etc/puppet/modules/apache2/manifests`
		- Create the class file `/etc/puppet/modules/apache2/manifests/init.pp`

```
class apache2 {
  package { 'apache2':
    ensure => installed,
  }

  service { 'apache2':
    ensure  => true,
    enable  => true,
    require => Package['apache2'],
  }
}
```
-
	- Create a node that includes the class
		- Add to `/etc/puppet/manifests/site.pp`

```
node 'puppetclient.example.com' {
   include apache2
}
```
-
	- Bounce the daemon
		- `sudo systemctl restart puppetmaster.service`
- Now set up a node to manage
	- Give it a hostname that can resolve both ways between server and node
	- make sure 'puppet' resolves to the puppetmaster! and puppetmaster also resolves
	- Configure the daemon to start
	- change `START=yes` on `/etc/default/puppet`
	- fire it up, `sudo systemctl start puppet.service`
	- Check the fingerprint `sudo puppet agent --fingerprint`
- Approve the cert
	- On the puppetserver: `sudo puppet cert list`
	- If it's not there, maybe a dns resolution issue?  Check logs
	- Sign it `sudo puppet cert sign puppetclient.example.com`
- Do a run
	- On the client, enable puppet `sudo puppet agent --enable`
	- Test run `sudo puppet agent --test`
	- Check that the package is installed `sudo dpkg -s apache2|grep Status`
- Lets install and manage MySQL
	- Install the puppetlabs MySQL module
		- On the puppetserver `sudo puppet module install puppetlabs-mysql`
	- Add this class to the puppetservers `/etc/puppet/manifests/init.pp`
	- Within the node declaration for the node

```
# We want MySQL installed on our machine
# We want MySQL to be constantly running
class { '::mysql::server':
    # We want to set custom MySQL root password
    root_password    => 'our_custom_password_here',
    override_options => {
        'mysqld' => {
            # We want MySQL max connections to be set to max_connections
            'max_connections'   => '1024',
            # We want MySQL key_buffer_size set to 512M      
            'key_buffer_size'   => '512M'       
        }       
    }   
}
```
-
	- Double check your work `puppet parser validate /etc/puppet/manifests/init.pp`
	- I keep having this error on the client:
```
Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Unknown function mysql::deepmerge at /etc/puppet/modules/mysql/manifests/server.pp:117 on node mysqlmaster.x.com
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
```
-
	- Astonishingly the error ZRP's on Google.  Super weird. Tried manually hacking /etc/puppet/modules/mysql/manifests/server.pp to require the .rb file which contains the deepmerge method.  Tried forcing an upgrade of the module on the client and the server.  The version of the module is 6.0.0.
	- Removing version 6.0.0: `sudo puppet module uninstall puppetlabs-mysql`
	- Grabbing the penultimate release: `wget https://forge.puppet.com/v3/files/puppetlabs-mysql-5.4.0.tar.gz`
	- Installing it from local source `sudo puppet module install ./puppetlabs-mysql-5.4.0.tar.gz`
	- That's totally not working either.  A different set of errors that I can't find answers to quickly:
```
Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Syntax error at 'Optional'; expected ')' at /etc/puppet/modules/mysql/manifests/db.pp:45 on node mysqlmaster.x.com
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
geoff@mysqlmaster:~$ sudo puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Loading facts
Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Resource type mysql::db doesn't exist on node mysqlmaster.x.com
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
```
-
	- Ugh headachey.  Decided to sanity check and try the example42 modules:
	- Uninstall the puppetlabs stuff on master and client `sudo puppet module uninstall puppetlabs-mysql`
	- Install the example42 stuff on master and client `sudo puppet module install example42-mysql`
	- Updated the manifest with the most baseline example42 config for a mysql server:
```
node 'mysqlmaster.x.com' {

        class { "mysql": }

}
```
-
	- Bounced everything `sudo systemctl restart puppetmaster.service`
	- Test on the client `sudo puppet agent --test`
	- Huzzah it worked! `sudo dpkg -s mysql-server`
	- Things are starting to work, added a root_password and tried making a table successfully.  in the puppetmaster's /etc/puppet/manifests/site.pp:
```
node 'mysqlmaster.x.com' {

        class { "mysql":
                root_password => '...',
        }
        mysql::grant { 'join_test':
                mysql_user      => '...',
                mysql_password  => '...',
        }

}
```
-
	- and it all worked.
> sources: https://www.slideshare.net/suhancoold/puppet-quick-start-guide
> https://help.ubuntu.com/lts/serverguide/puppet.html.en
> https://kyup.com/tutorials/introduction-puppet-configure-mysql-instances-puppet/
> https://www.digitalocean.com/community/tutorials/getting-started-with-puppet-code-manifests-and-modules
> https://forge.puppet.com/example42/mysql
