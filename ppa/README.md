# Running Microservices Reference Architecture in Airgapped ICP Environments

In some cases, the ICP cluster has no Internet access and cannot pull images from public DockerHub.  In this case, we provide a script to generate a PPA archive that can be imported into ICP using the `bx pr` CLI.  For more information on the CLI, see [this link](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0/manage_cluster/install_cli.html).

## Requirements

- The PPA archive produced will work with IBM Cloud Private 3.1.0 and later.
- You will need this project cloned to a Linux machine with internet access and bash installed.

## Create the PPA Archive

To generate the archive, use a machine with Internet access to clone this git repository. Execute the script to create the PPA archive::

```bash
cd ppa
./build_ppa_archive.sh
```

This will pull all the required Docker images and put the images and the charts into a single tarball.  Copy this tarball to the boot node of the ICP cluster (or one of the master nodes).  Authenticate to the container private registry from the boot node, using the cluster CA domain.  For example, for a cluster named `mycluster.icp`, use:

```bash
docker login mycluster.icp:8500
```

Authenticate with the `bx pr` CLI (which you can install using [these](#installing-the-bx-pr-cli) instructions) using the following command.  For a cluster named `mycluster.icp`, use the following:

```bash
bx pr login -a https://mycluster.icp:8443 --skip-ssl-validation
```

Once you are authenticated, use the following command to import all of the images in the PPA archive named `bluecompute-ce-ppa-0.0.6.tgz`, for a cluster named `mycluster.icp`:

```bash
bx pr load-ppa-archive --archive bluecompute-ce-ppa-0.0.6.tgz --clustername mycluster.icp --namespace default
```

This will push all of the images into the private registry under the `default` namespace and load the chart to the `local-charts` helm chart repository in ICP.  You can install the chart from the catalog in the `local-charts` repository.

For more information on this procedure in ICP, see the [documentation](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0/app_center/add_package_offline.html).

## Installing the `bx pr` CLI

Navigate to the `Cloud Private CLI` page in the ICP dashboard (as shown below) and download the CLI for your platform.

![bx pr cli install instructions](imgs/bx_pr_cli.png?raw=true)

Once downloaded, you can install the `Cloud Private CLI` using [these](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0/manage_cluster/install_cli.html) instructions.