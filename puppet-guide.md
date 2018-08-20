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

> sources: https://www.slideshare.net/suhancoold/puppet-quick-start-guide
> https://help.ubuntu.com/lts/serverguide/puppet.html.en
