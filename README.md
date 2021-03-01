## Table of Contents:

We will be covering the below topics in this guide.

1. [Create an IBM image registry and namespace.](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/main/README.md#1-create-an-ibm-image-registry-and-namespace)
2. [Update the operator and bundle artifacts as per best practices guidelines.](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/main/README.md#2-update-the-operator-and-bundle-artifacts-as-per-best-practices-guidelines)
3. [Validate operator bundle.](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/main/README.md#3validate-operator-bundle)
4. [Push the images to IBM cloud registry.](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/main/README.md#4-push-images-to-ibm-cloud-registry)
5. [Upgrade to new operator version.](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/main/README.md#5-upgrade-to-a-new-version)

## Getting Started

This document will help you learn about the process and best practices that you need to follow while onboarding an operator to IBM Cloud Catalog. The process will be explained with Node-red operator as an example.

Before you start, check out the `Pre-requisites` section to know what things you need to keep ready for operator onboarding.

## Pre-requisites

Prior to continuing, kindly ensure that you have the below accompanying stages ready.

1. You have an active IBM Cloud account.
2. The Operator should be created using Operator-SDK version 1.2.0 or above. This will let you perform smooth onboarding of operator to IBM Cloud Catalog
3. Post the operator creation, the folder structure for your operator should look similar to the sample given below for `node-red-operator`. 

```
node-red-operator/
├── config
│   ├── crd
│   │   ├── bases
│   │   │   ├── nodered.com_noderedbackups.yaml
│   │   │   ├── nodered.com_noderedrestores.yaml
│   │   │   └── nodered.com_nodereds.yaml
│   │   └── kustomization.yaml
│   ├── default
│   │   ├── kustomization.yaml
│   │   └── manager_auth_proxy_patch.yaml
│   ├── manager
│   │   ├── kustomization.yaml
│   │   └── manager.yaml
│   ├── prometheus
│   │   ├── kustomization.yaml
│   │   └── monitor.yaml
│   ├── rbac
│   │   ├── auth_proxy_client_clusterrole.yaml
│   │   ├── auth_proxy_role_binding.yaml
│   │   ├── auth_proxy_role.yaml
│   │   ├── auth_proxy_service.yaml
│   │   ├── kustomization.yaml
│   │   ├── leader_election_role_binding.yaml
│   │   ├── leader_election_role.yaml
│   │   ├── noderedbackup_editor_role.yaml
│   │   ├── noderedbackup_viewer_role.yaml
│   │   ├── nodered_editor_role.yaml
│   │   ├── noderedrestore_editor_role.yaml
│   │   ├── noderedrestore_viewer_role.yaml
│   │   ├── nodered_viewer_role.yaml
│   │   ├── role_binding.yaml
│   │   ├── role.yaml
│   ├── samples
│   │   ├── kustomization.yaml
│   │   ├── nodered_v1alpha1_noderedbackup.yaml
│   │   ├── nodered_v1alpha1_noderedrestore.yaml
│   │   └── nodered_v1alpha1_nodered.yaml
│   ├── scorecard
│   │   ├── bases
│   │   │   └── config.yaml
│   │   ├── kustomization.yaml
│   │   └── patches
│   │       ├── basic.config.yaml
│   │       └── olm.config.yaml
│   └── testing
│       ├── debug_logs_patch.yaml
│       ├── kustomization.yaml
│       ├── manager_image.yaml
│       └── pull_policy
│           ├── Always.yaml
│           ├── IfNotPresent.yaml
│           └── Never.yaml
├── Dockerfile
├── Makefile
├── molecule
│   ├── default
│   │   ├── converge.yml
│   │   ├── create.yml
│   │   ├── destroy.yml
│   │   ├── kustomize.yml
│   │   ├── molecule.yml
│   │   ├── prepare.yml
│   │   ├── tasks
│   │   │   ├── noderedbackup_test.yml
│   │   │   ├── noderedrestore_test.yml
│   │   │   └── nodered_test.yml
│   │   └── verify.yml
│   └── kind
│       ├── converge.yml
│       ├── create.yml
│       ├── destroy.yml
│       └── molecule.yml
├── playbooks
├── PROJECT
├── requirements.yml
├── roles
│   ├── nodered
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── files
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── meta
│   │   │   └── main.yml
│   │   ├── README.md
│   │   ├── tasks
│   │   │   └── main.yml
│   │   ├── templates
│   │   └── vars
│   │       └── main.yml
│   ├── nodered-backup
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── files
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── meta
│   │   │   └── main.yml
│   │   ├── README.md
│   │   ├── tasks
│   │   │   └── main.yml
│   │   ├── templates
│   │   └── vars
│   │       └── main.yml
│   └── nodered-restore
│       ├── defaults
│       │   └── main.yml
│       ├── files
│       ├── handlers
│       │   └── main.yml
│       ├── meta
│       │   └── main.yml
│       ├── README.md
│       ├── tasks
│       │   └── main.yml
│       ├── templates
│       └── vars
│           └── main.yml
└── watches.yaml
```
See the [`node-red-operator`](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/tree/nodered-operator-v0.0.3/node-red-operator)directory for more details.

Now that you have your operator ready, you can proceed to the following sections to get information on the best practices for onboarding your operator.



## 1. Create an IBM image registry and namespace ##

For uninterrupted onboarding of your operator  to IBM Cloud Catalog, you need to set up your private IBM registry namespace.

Please refer [Quick start](https://cloud.ibm.com/registry/start) guide for the important steps on how to get started.



## 2. Update the operator and bundle artifacts as per best practices guidelines ##

Before you begin with the best practices, make sure that you have generated your operator bundle using operator sdk 1.2.0 or above.

The folder structure of your operator bundle should look like the example given below for node-red-operator.

```
.
├── bundle
|   ├── manifests
|   |   ├── nodered.com_noderedbackups.yaml
|   │   ├── nodered.com_noderedrestores.yaml
|   │   │── nodered.com_nodereds.yaml
|   │   └── node-red-operator.clusterserviceversion.yaml
|   ├── metadata
|	    └── annotations.yaml
├── bundle.Dockerfile
```

Now , validate your operator bundle by executing the following command on the root directory of your project:

```execute
operator-sdk bundle validate ./bundle
```

Once you have validated your operator bundle, you're good to start with updating the operator bundle artifacts as follows.

**a) CRD**

By default the operator-sdk 1.2.0 and above versions creates the latest version of CustomResourceDefinition(CRD) `v1`. The older versions of OpenShift only support v1beta1. So if your operator is going to be listed on OCP 4.5 and earlier, you'll need to convert to the older format.

The structure of your operator’s CRD should look like below.

```yaml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: nodereds.nodered.com
spec:
  group: nodered.com
  names:
    kind: NodeRed
    listKind: NodeRedList
    plural: nodereds
    singular: nodered
  scope: Namespaced
  subresources:
    status: {}
  validation:
    openAPIV3Schema:
      type: object
      x-kubernetes-preserve-unknown-fields: true
  version: v1alpha1
  versions:
  - name: v1alpha1
    served: true
    storage: true
```

There are a few important fields in your bundle CSV file that don't get generated by default. So, it is recommended that you make the required changes to your CSV file manually.

**b) CSV **

You need to add personalized data to the following required fields of the CSV.

**```namespace:```** 
If you want your operator to be installed in any specific namespace, modify this field to the required namespace. Else continue with the existing details.

**```name:```**
This is the name of your CSV file. In general, the naming convention includes the name of the operator followed by the semantic version number, separated by a period (e.g. node-red-operator.v2.1.0). You need to modify the name according to your requirement.

**```annotations:```** 

| Sub-fields    | Description                                                  |
| :------------ | ------------------------------------------------------------ |
| alm-examples  | This is an array containing CRD templates. The users of your Operator should be aware of mandatory and optional choices. You can upload the CRD templates containing a minimum set of configuration as an annotation in JSON format. |
| capabilities: | Based on the operator maturity model, the capability can be classified as Basic Install or Seamless Upgrades or Full Lifecycle or Deep Insights or Auto Pilot |
| categories    | A category is a comma separated string of applicable category names. |
| description   | To give a short description of the operator                  |
| createdAt     | This indicates a rough (to the day) timestamp of when the operator image was created |
| support       | This contains the  name of the supporting vendor (eg: ExampleCo) |
| certified     | (deprecated) this string has no effect, but can be set to "true" or "false" |
| repository    | This is an optional URL of the operator's source code repository. If you don't want to specify , set it to NA. |

**`version:`** The value you enter in this field will depict the current version of the operator.

**`replaces:`** This interprets as “the previous version to be replaced with a particular version". This field is very important for version upgrades to take place.

**`customresourcedefinitions:`** Make sure you mention all the CRDs for your operator in this field.

**`icon:`** It is recommended to add the `base64data` of your icon.

**`clusterPermissions:`** Make sure you add all the required cluster permissions for your operator.

**`serviceAccountName:`** It is very important to mention the ServiceAccountName in this field for your operator to work properly.

**`keywords:`** It contains comma-separated list of keywords for your operator 

**`description:`** This contains the description for the operator, which is very important for understanding the operator better.

**`maintainers:`** It is the comma-separated list of maintainers and their emails (e.g. 'name1:email1, name2:email2') 

**`provider:`** It is the provider's name for the operator 

Check the CSV for [`node-red-operator`](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/nodered-operator-v0.0.3/node-red-operator/bundle/manifests/node-red-operator.v2.1.0.clusterserviceversion.yaml) for more details.

**c) bundle.Dockerfile**

The `bundle.Dockerfile` in the root directory of your operator project is the Dockerfile for your metadata bundle image. 

Make sure the following 3 labels are present in the Dockerfile:

* `LABEL com.redhat.openshift.versions="v4.5,v4.6"` : This lists the OpenShift versions, starting with 4.5, that your operator will support. The value should with the letter 'v', followed by the number with no space in between.
* `LABEL com.redhat.delivery.operator.bundle=true` : This just needs to be there
* `LABEL com.redhat.deliver.backport=true` : This indicates the support provision for OpenShift versions before 4.5. If you don't specify this flag, your operator won't be listed in 4.4 or earlier.

As a best practice you may want to add the default channel and package name by adding the following labels for smooth onboarding to IBM cloud.

`LABEL operators.operatorframework.io.bundle.channel.default.v1=alpha`

`LABEL operators.operatorframework.io.bundle.package.v1=node-red-operator-certified`

For more details, refer the example   [`node-red-operator`](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/nodered-operator-v0.0.3/node-red-operator/bundle/bundle-v2.1.0.Dockerfile) 

**d) annotations.yaml**

For a smooth onboarding you need to have all the labels present in your `bundle.Dockerfile` contained in your `annotations.yaml`.

Make sure you add the package name properly.

`operators.operatorframework.io.bundle.package.v1: node-red-operator-certified`

See [`node-red-operator`](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/blob/nodered-operator-v0.0.3/node-red-operator/bundle/metadata/annotations.yaml) for more details.



## 3.Validate operator bundle ##

Once you have covered all the best practices to consider for updating your operator and bundle artifacts, you need to validate your bundle once again using the following command. 

```execute
operator-sdk bundle validate ./bundle
```

If it produces any error or warning please ensure that you resolve all of them. 

After validating your operator bundle as above you can proceed to the next section that talks about pushing  your images to IBM cloud registry. 



## 4. Push images to IBM cloud registry

For getting your operator onboarded to IBM Cloud, it is very important to push all your images to the IBM cloud registry.

**a) Push Application images :**

If your operator uses multiple application images, all of them should be pushed to the Cloud registry. Make sure you push your images with the correct namespace in your registry.

Refer [Quickstart](https://cloud.ibm.com/registry/start) for step-by-step guide to login to your IBM cloud registry and push the application images to your private registry.

**b) Push Operator images :** 

Your Operator image should be available in a public registry .

**c) Push Operator Bundle images :**

Your  Operator Bundle image should be available in a public registry. Please ensure that all these images are public.



## 5. Upgrade to a new version ##

Each Cluster Service Version **(CSV)** file represents a single version of the operator. Subsequent versions of the operator will have their own respective CSV files. 

If you need to release a new version of the operator (say 2.0.0, while your present version is 1.0.0), then the most-followed way is to create a copy of the previous version with the appropriate changes so that you can reuse the old bundle.

See the steps below to be followed.

**a) Create a new operator image**

In the scenarios where you have changed your operator definitions, or you want to change your application image to something new, it is recommended to re-build the operator image after you have made all the desired changes. Also, make sure that you have made your image 'public'.

Refer this [Quick-start-guide](https://cloud.ibm.com/registry/start) for detailed instructions.



**b) Copy the bundle and modify version**

Copy the folder contents of the previous version(example:1.0.0)  to a new folder called 2.0.0 (which will be the new release version).

```
├── 1.0.0
│   ├── manifests
│   │   ├── nodered.com_nodereds_crd.yaml
│   │   └── node-red-operator.v1.0.0.clusterserviceversion.yaml
│   ├── metadata
│       └── annotations.yaml   
├── bundle-1.0.0.Dockerfile
```

 Change the version number in the CSV file name. See the sample below

```
├── 2.0.0
│   ├── manifests
│   │   ├── nodered.com_nodereds_crd.yaml
│   │   └── node-red-operator.v2.0.0.clusterserviceversion.yaml
│   ├── metadata
│       └── annotations.yaml   
├── bundle-2.0.0.Dockerfile
```

Now you should have two folders representing two versions as shown below

```
├── 1.0.0
│   ├── manifests
│   │   ├── nodered.com_nodereds_crd.yaml
│   │   └── node-red-operator.v1.0.0.clusterserviceversion.yaml
│   ├── metadata
│       └── annotations.yaml  
├── 2.0.0
│   ├── manifests
│   │   ├── nodered.com_nodereds_crd.yaml
│   │   └── node-red-operator.v2.0.0.clusterserviceversion.yaml
│   ├── metadata
│       └── annotations.yaml  
├── bundle-1.0.0.Dockerfile
├── bundle-2.0.0.Dockerfile
```

**c) Make changes in the CSV file**

It is the most important step for any version upgrade~~s~~.

To ensure that the upgraded contents are there inside  `clusterserviceversion.yaml`, following list of modifications are required for the CSV file inside the newly created folder (2.0.0) 

* Update the `metadata.name` field of the CSV file with the new version(2.0.0). 

  ```
  name: node-red-operator.v2.0.0
  ```

* Update the `spec.version` field with the new version. 

  ```
  version: 2.0.0
  ```

* Update the `spec.replaces` field to indicate the previous version(1.0.0) of the CSV that is being upgraded to the new version(2.0.0). 

  ```
  replaces: node-red-operator.v1.0.0
  ```

**NOTE: **Along with the above metadata updates to the CSV file, you will also need to update the new version of Operator Image and Application Image(s) based on the updates associated with the new release.



**d) Validate the operator bundle**

Once you have updated your operator bundle, validate your bundle using the following command. 

```execute
operator-sdk bundle validate ./2.0.0
```

If it shows any error or warning, please ensure that you resolve all of them. 

Once your operator bundle is validated,you can proceed to the next section to know how to push your images to IBM cloud registry. 

Please see  [`node-red-operator`](https://github.com/IBM-Cloud/isv-operator-product-deploy-sample/tree/nodered-operator-v0.0.3/node-red-operator/bundle) example for reference.

**e) Push images to IBM cloud registry**

For getting your new version of the operator onboarded to IBM Cloud, it is very important to push all your images to IBM cloud registry.

* **Push Application images :**

If you have made any changes in your application images, all of the new images should be pushed to the Cloud registry. Make sure you push your images in the correct namespace contained in your registry.

Refer [Quick-start-guide](https://cloud.ibm.com/registry/start)  for important steps to login to your IBM cloud registry and push the application images to your private registry.

* **Push Operator images :** 

If you have made any changes in your operator images, then your Operator image should be pushed to IBM Cloud Registry/ [quay.io](https://quay.io/)/ [docker.io](https://hub.docker.com/) and must be  available publicly .

* **Push Operator Bundle images :**

Your  Operator Bundle image should be available in a public registry. Please ensure that all these images are public after you push it to IBM Cloud Registry/ [quay.io](https://quay.io/)/ [docker.io](https://hub.docker.com/).


