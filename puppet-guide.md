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

	- Create a node that includes the class
		- Add to `/etc/puppet/manifests/site.pp`

```
node 'puppetclient.example.com' {
   include apache2
}
```

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


> sources: https://www.slideshare.net/suhancoold/puppet-quick-start-guide
> https://help.ubuntu.com/lts/serverguide/puppet.html.en
> https://kyup.com/tutorials/introduction-puppet-configure-mysql-instances-puppet/
