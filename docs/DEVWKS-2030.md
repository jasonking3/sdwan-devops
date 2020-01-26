# DEVWKS-2030 SD-WAN DevOps Step 3: Continuous Integration/Continuous Deployment

## Create a CI pipeline in GitLab
1. Login to GitLab at http://cpn-rtp-gitlab1.colab.ciscops.net:8081 using the credentials supplied by your instructor

1. From the GitLab web UI, select "Create a project".

1. Under "Project name", enter `sdwan-devops` and then click "Create project".

1. Populate the necessary VIRL environment variables needed to access the VIRL server.  We store these variables in the project CI settings so that they are not stored unencrypted in the code repo.  Under Settings -> CI/CD, expand the "Variables" section and supply values for the following variables:
    - `VIRL_HOST`
    - `VIRL_USERNAME`
    - `VIRL_PASSWORD`
    - `VIRL_LAB`
    - `VMANAGE_ORG`

1. Click "Save variables".

1. From the shell, clone the `sdwan-devops` repo.
    ```
    git clone -b DEVWKS-2226 --recursive https://github.com/CiscoDevNet/sdwan-devops.git
    ```
1. Change to the `sdwan-devops` directory.
    ```
    cd sdwan-devops
    ```
    > Note: this workshop assumes that all future commands are executed from this directory.

1. Remove the old origin
    ```
    git remote remove origin
    ```

1. Add a new origin that points to your newly created GitLab project, replacing `X` in the command below with your pod number.
    ```
    git remote add origin http://podX@cpn-rtp-gitlab1.colab.ciscops.net:8081/podX/sdwan-devops.git
    ```
    > Note: there are two places where you need to make this change.

1. Make the licenses directory and download the required license file.
    ```
    mkdir licenses
	wget -O licenses/serialFile.viptela -L "https://www.dropbox.com/s/gyuxxn311peccwp/serialFile.viptela?dl=0"
    ```

1. Add the license file for commit and commit the file.
    ```
    git add -f licenses/serialFile.viptela
	git commit -m "Adding license"
    ```

1. Edit the `.gitlab-ci.yml` file, replacing `X` in the tags section with your pod number (e.g. `runnerX` should be changed to `runner1` if you are pod one).

1. Add the `.gitlab-ci.yml` file for commit and commit the file.
    ```
    git add .gitlab-ci.yml
	git commit -m "Updating .gitlab-ci.yml"
    ```

1. Now push the commits to your new project.
    ```
    git push -u origin --all
    ```
    > Note: enter your GitLab credentials if asked

1. From the GitLab web UI, navigate to the CI/CD -> Pipelines page.  You should see a pipeline currently active since we commited a the sdwan-devops code and we had a `.gitlab-ci.yml` file present.  If that file is present, GitLab will automatically try to execute the CI pipeline defined inside.

1. Use the graphical representation of the pipeline to click through the console output of the various stages.  The entire pipeline will take approximately ~12 minutes to complete.  Wait until it completes to go onto the next step.

## Review the configuration in vManage
1. From the shell, export the required VIRL environment variables:
    ```
    export VIRL_HOST=(supplied by instructor)
    export VIRL_USERNAME=(supplied by instructor)
    export VIRL_PASSWORD=(supplied by instructor)
    export VIRL_LAB=podX_sdwan (where X is your pod number)
    ```

1. Run the `virl-inventory.yml` playbook to find your vManage IP address.
    ```
    ./play.sh --limit "vmanage1" virl-inventory.yml
    ```

1. Browse to the IP address listed for your vManage and login with admin/admin.

1. Review the configuration of vManage.

## Modify infrastructure-as-code to exercise the CI pipeline
1. Run the `virl-inventory.yml` playbook to find your site1-vedge1 IP address.
    ```
    ./play.sh --limit "site1-vedge1" virl-inventory.yml
    ```
1. SSH to the `site1-vedge1` IP address, using credentials admin/admin.
    ```
    ssh admin@(your site1-vedge1 IP address)
    ```

1. Verify the banner when you login.  It should look something like:
    ```
    % ssh admin@192.133.183.178
    This system is for the use of authorized clients only.
    admin@192.133.183.178's password: 
    Welcome to site1-vedge1!
    ```

1. Edit the `inventory/hq1/host_vars/site1-vedge1/sdwan.yml` file and replace the line:
    ```
    'banner_motd': Welcome to site1-vedge1!
    ```
    with:
    ```
    'banner_motd': Cisco DevNet is awesome!
    ```

1. Save the file.

1. Add the file for commit and commit the file.
    ```
    git add inventory/hq1/host_vars/site1-vedge1/sdwan.yml
	git commit -m "Updated banner"
    ```

1. Push the commit to GitLab.
    ```
    git push
    ```

1. From the GitLab web UI, navigate to the CI/CD -> Pipelines page.  You should have a new pipeline being run based off the change you pushed to GitLab.  Wait for the pipeline to complete.

1. From the shell, SSH to `site1-vedge1` and verify the new banner.
