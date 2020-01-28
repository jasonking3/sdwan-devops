# DEVWKS-2226 SD-WAN DevOps Step 1: Automating Test Environments

## Verify access to the VIRL server
1. Browse to https://cpn-rtp-virl5.ciscops.net and login with the credentials supplied by your instructor.

1. Verify access to the VIRL UI.

## Build and configure the SD-WAN topology in VIRL
1. From a shell, clone the sdwan-devops repo.    
    ```
    git clone -b DEVWKS-2226 --recursive https://github.com/CiscoDevNet/sdwan-devops.git
    ```

1. Change to the `sdwan-devops` directory.
    ```
    cd sdwan-devops
    ```
    > Note: this workshop assumes that all future commands are executed from this directory.

1. Make the licenses directory and download the required license file.
    ```
    mkdir licenses
	wget -O licenses/serialFile.viptela -L "https://www.dropbox.com/s/gyuxxn311peccwp/serialFile.viptela?dl=0"
    ```

1. Build the local certificate authority.
    ```
    ./play.sh build-ca.yml
    ```

1. Build the SD-WAN topology in VIRL.
    ```
    ./play.sh build-virl.yml
    ```

1. From the VIRL UI, verify that your simulation has started.  It should have the name you supplied for the VIRL_LAB environment variable specified above.

1. From the shell, watch the playbook as it goes through it's tasks and wait for it to complete.  It will be creating Day 0 configurations, starting the simulation and verifying connectivity to nodes.

1. From the VIRL GUI, click on your simulation and view the topology.

1. If you want to play with some of the VIRL features such as console access, this is a good time do that.

1. From the shell, configure the control plane.
    ```
    ./play.sh config-virl.yml
    ```

1. Watch the playbook as it goes through it's tasks and wait for it to complete.  It will be creating and configuring all required certificates, provisioning vSmart/vBond and, finally, pushing all of the device and feature templates to vManage.

## Login to vManage and verify the configuration
1. From the shell, use `virl-inventory.yml` to find the IP address of vManage.
    ```
    ./play.sh --limit "vmanage1" virl-inventory.yml
    ```

1. Browse to the IP address of vManage and login with the credentials admin/admin.

1. Verify that the control plane is up and that all of the device and feature templates have been configured.

## Deploy the edges
1. From a shell, deploy the SD-WAN edges.
    ```
    ./play.sh deploy-virl.yml
    ```

1. Watch the playbook as it goes through it's tasks and wait for it to complete.  It will be configuring certificates and adding the vEdges to the control plane.

1. After the playbook completes, use the vManage UI to verify that the edges are up and "in sync".  This will take several minutes.  (Alternatively, you can use the `waitfor-sync.yml` playbook to have Ansible tell you when the edges are synced.)

## Test the SD-WAN
1. Verify the SD-WAN connectivity.
    ```
    ./play.sh check-sdwan.yml
    ```

1. If this playbook completes successfully then the SD-WAN toppology has passed all of its connectivity tests.

1. Congratulations!  You have built an entire SD-WAN test environment and configured it in minutes.  You'll never want to do it manually again.

## Cleanup
1. At the end of the workshop, cleanup the repo and delete the simultation.
    ```
    ./play.sh --tags "delete" clean-virl.yml
    ```

1. Remove the sdwan-devops repo.
    ```
    cd ..
    sudo rm -rf sdwan-devops
    ```
