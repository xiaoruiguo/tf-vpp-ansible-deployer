# tf-vpp-ansible-deployer
Tungsten fabric VPP ansible deployer repo, used to deploy tungsten fabric controller with VPP as datapath.

# Setting-up
execute ./startup.sh to allow setting up the basic repos needed to deploy tungsten fabric with vpp.

```
git clone https://github.com/atsgen/tf-vpp-ansible-deployer
cd tf-vpp-ansible-deployer
./startup.sh
```
sample configuration is available for reference, defining vpp related parameters.

<b><i>Note:</b> vpp needs uio driver to work, it is assumed the the uio driver is already loaded, ./startup.sh tries to provide igb_uio driver if available to specific kernel version. </i>

move to directory contrail-ansible-deployer, verify/update your configuration in instances.yaml
use following to deploy tungsten fabric and vpp integrated solution with openstack
```
cd contrail-ansible-deployer
# Instance provisioning:
ansible-playbook -i inventory/ playbooks/provision_instances.yml
# Instance configuration:
ansible-playbook -e orchestrator=openstack -i inventory/ playbooks/configure_instances.yml
# OpenStack installation:
ansible-playbook -i inventory/ playbooks/install_openstack.yml
# Tungsten Fabric installation:
ansible-playbook -e orchestrator=openstack -i inventory/ playbooks/install_contrail.yml
# NOTE: need to be logged into docker with the id that have access to container images
# VPP components installation:
ansible-playbook -e orchestrator=openstack -i inventory/ playbooks/install_vpp.yml
```

Note: you will need access to container images for deploying this solution

### Setting up flavour and base image
you can use atsgen/openstack-cli:latest container to use the openstack cli, to execute cli commands to create flavor and images
```
docker run -d --name openstack-cli \
  -v /etc/kolla/kolla-toolbox/admin-openrc.sh:/admin-openrc.sh:ro \
  -it atsgen/openstack-cli:latest
```
you can connect to container using exec :
```
docker exec -it openstack-cli bash
```
vpp is dpdk based and hence needs huge pages backed flavors
```
source /admin-openrc.sh
openstack flavor create --ram 512 --disk 1 --vcpus 1 dpdk.tiny
nova flavor-key dpdk.tiny set hw:mem_page_size=large
openstack image create cirros2 --disk-format qcow2 --public \
  --container-format bare --file cirros-0.4.0-x86_64-disk.img

```
### Access
Tungsten fabric vpp integration is available as a free-ware, which can be accessed by registering with ATSgen.

[click here](https://forms.office.com/Pages/ResponsePage.aspx?id=0yH2wN56HEmDCnxQQOKgIl5ckXp5a_9JtlgcT-8SZJpUMTFJSUdKOElSWDFPWUcyVzdLWFIxRlhWUC4u) to request access to the container images for tungsten-vpp
