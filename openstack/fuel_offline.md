###fuel-mirror

Create a local mirror. Command been used before: fuel-createmirror

  1. Create a local mirror with fuel-mirror create -I /usr/share/fuel-mirror/ubuntu.yaml -G ubuntu mos
  2. Use the local mirror as default for all new environments: fuel-mirror apply --default -I /usr/share/fuel-mirror/ubuntu.yaml -G mos ubuntu
  3. Create a new environment
  
### fuel-bootstrap
List bootstrap images

    fuel-bootstrap list
    fuel-bootstrap import bootstap.tar.gz  ; import a created bootstrap image
    fuel-bootstrap activate ID ;  activate a bootstrap image
 
 Configure repos while fuel-bootstrap build using.
 
     /etc/fuel-bootstrap-cli/fuel_bootstrap_cli.yaml
