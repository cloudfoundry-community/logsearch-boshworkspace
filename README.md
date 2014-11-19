logsearch-boshworkspace
=======================
The fastest way to deploy [logsearch](http://www.logsearch.io) in combination with [Cloud Foundry](http://www.cloudfoundry.org) onto [bosh-lite](https://github.com/cloudfoundry/bosh-lite).

### Preparation
To get started you will need a running bosh-lite.
Get yours by following the instructions [here](https://github.com/cloudfoundry/bosh-lite#install-bosh-lite)

Next step is setting up this repository
```
git clone https://github.com/cloudfoundry-community/logsearch-boshworkspace.git
cd logsearch-boshworkspace
bundle install
```

### Deploy Cloud Foundry
With all prerequisites in place let deploy Cloud Foundry. 
```
bosh deployment cf-warden
bosh prepare deployment
bosh deploy
```

### Deploy Logsearch
```
bosh deployment logsearch-warden
bosh prepare deployment
bosh deploy
```

