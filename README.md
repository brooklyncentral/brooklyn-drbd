# DRBD

DRBD is a distributed replicated storage system for the Linux platform. It is implemented as a kernel driver, 
several userspace management applications, and some shell scripts. DRBD is traditionally used in high 
availability (HA) computer clusters to provide a RAID 1 like configuration over a network. 

## Requirements

Brooklyn-DRBD requires [brooklyn-blockstore](https://github.com/cloudsoft/brooklyn-blockstore) an open source 
addition to Brooklyn which enables provisioning of additional volumes for VMs, this should be installed as a bundle.
Note that this is included by default in Cloudsoft AMP.

## Basic Config

Brooklyn DRBD is a YAML blueprint for deployment and in-life management of a two node DRBD structure. Both
DRBD 8.x and 9.x are supported and configurations of more than two nodes for DRBD 9 will be added in the near 
future. 

| Config Key                    | Default         | Description                                           |
|-------------------------------|-----------------|-------------------------------------------------------|
| drbd.version                  | 84              | The version of DRBD to use, (currently 84 or 90)      |
| drbd.mountpoint               | /mnt            | The location that the DRBD filesystem will be mounted |
| drbd.filesystem               | ext4            | The filesystem format of the DRBD volume              |
| drive.capacity                | 1               | The size in Gigabytes of the mirrored volume          |
| drbd.nodes                    | 2               | The number of nodes (NB. 2 currently only supported)  |

## Usage

The main entity is called `drbd-pair`, this provisions a pair of DRBD nodes. You can then use `nodeSpecification`
and `initialNodeSpecification` to specify an `entitySpec` containing the blueprints you want on the nodes. An
example of this is shown in [drbd.tomcat-example.bom](drbd.tomcat-example.bom). Finally `propagatingList` allows
you to specify a list of sensors which will be propagated back to the entity root.

```
services:
- type: drbd-pair
  brooklyn.config:
    propagatingList:
    - $brooklyn:sensor("main.uri")
    nodeSpecification: 
      $brooklyn:entitySpec:
        - type: example-app
    initialNodeSpecification:
      $brooklyn:entitySpec:
        - type: initial-example-app
```

## Future development

DRBD 9+ supports the use of multiple secondaries, this will be implemented in the near future. Additionally in
DRBD 8, multiple primaries are supported. This will require further development.