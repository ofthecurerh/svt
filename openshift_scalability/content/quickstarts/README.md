# OpenShift Quickstart Apps

## How to use

### Included files

For each application there are the following files:
- \<app>-\<db>.json
- \<app>-\<db>-pv.json
- \<app>-\<db>-deploy.json
- \<app>-\<db>-pv-deploy.json
- \<app>-build.json

Each application has two permutations, one with persistent volumes and one without persistent volumes. 

Within each permutation there is a complete template that includes a BuildConfig and ImageStream and a deploy only template that omits the build step. The deploy only template assumes that the container image has already been built in the `openshift` namespace. 

### cluster-loader

In the cluster-loader config directory there are corresponding cluster-loader configs for the above permutations of quickstart templates. 

Example usage:
```
# python cluster-loader.py -f config/master-vert-pv.json
```

## Requirements and Recommendations 

### Router

Sample dnsmasq config file for remote machine to resolve application routes:

```
# /etc/dnsmasq.d/openshift.conf
# Wildcard DNS for OpenShift Applications - Points to Router
address=/<routing-config-subdomain>/<router-host-ip-addr>
```

### Registry

- If on AWS, best to use an io1 volume instead of gp2

- Best to use a persistent volume: https://docs.openshift.com/enterprise/3.2/install_config/install/docker_registry.html#registry-production-use

### Docker Storage

- If on AWS, best to use an io1 volume for the docker storage volume on all OSE nodes instead of gp2

## Issues

### Dynamic Provisioning 

- Upstream application templates that use persistent volumes have been modified to use dynamic provisioning

- As of OpenShift Enterprise 3.2, dynamic provisioning only works with [Cinder, Amazon EBS, and GCE volumes](https://docs.openshift.com/enterprise/3.2/install_config/persistent_storage/dynamically_provisioning_pvs.html#enabling-provisioner-plugins)

- Dynamic provisioning on Amazon EBS is [hardcoded](https://github.com/kubernetes/kubernetes/blob/master/pkg/volume/aws_ebs/aws_ebs.go#L414) to use `ext4` filesystem

### Application Templates are Copied from Upstream

- Complete templates have been modified to use persistent volumes

- Complete templates have been edited down to create seperate build and deploy templates

- cluster-loader doesn't support creating templates from URLs

- Template source: https://github.com/openshift/online/tree/master/templates/examples