# arm-template-wvd
Refactoring WVD ARM template

## Purpose
I'd like to simplify procedure of creating session hostname when using ARM template for WVD.
In the original ARM template for WVD the code related with hostname is scatterd over templates so that it is not easy to customize it.

## Approach
1. Increasing cohesion
1. Decreasing coupling

### Increasing choesion
A logic for creating hostname exist only in "template.json".

The templates for making SessionHost is made up of "template.json" and inner template "managedDisks-customimagevm.json" and "managedDisks-galleryvm.json"
and the logic related hostname is scattered over the templates.

The name of inner template depends on parameters "vmImageType" and "vmUseManagedDisks". 
You can find the portion in "variables" section of template.json.

### Decreasing coupling
"template.json" and inner template is connected via parameter "rdshVmNames", which is an array of hostnames made in "template.json".

The both templates is originally connected via several parameters. Thanks to increase cohesion, the two come to be less coupled.

## How to use
Hostname is made from parameters "vmNamePrefix", "vmInitialNumber", and "vmNumberOfInstances" in variables section of "template.json".
This sample code have this naming convention; Prefix + 6 digit serial number

for example, 
vmNamePrefix : WVD
vmInitialNumber : 100
vmNumberOfInstances : 3
the created hostnames are WVD000100, WVD000101, WVD000102

You can get your parameter file exporting a previous deployment of session host. ([see this guide](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/export-template-portal))

Before executing this templates you have to place the inner templates "managedDisks-customimagevm.json" and "managedDisks-galleryvm.json" into your accesible place 
and the URI is set as a value of "nestedTemplatesLocation".

~~~
az deployment group create `
-n addVMtoHostPool-202007131515 `
-g rgVDI-Pool-00 `
-f .\template.json `
-p .\parameters.json
~~~

## reference
https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/
