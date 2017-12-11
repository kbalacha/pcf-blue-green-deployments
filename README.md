# pcf-blue-green-deployments
Ansible playbooks to perform blue green deployments to PCF


The play command will take the following parameters:

pcf_api_endpoint - PCF Apps Manager endpoint (eg) api.dev.kbalacha.com
space - Space within the foundation to deploy to. (eg) Dev, Test
artifact_location - Location of deployable jar/war in artifactory/nexus. It is downloaded to /var/tmp
app_name - Name of application
branch_name - Name of branch to check out manifest.yml from. Mostly this is master for prod, develop for non prod. The play is configured to check out from a gitlab location. It constructs the full path of git location using app name
blue_green_route - The blue green route to take. Scroll down for more information on routes. By default route 2 is chosen.

Sample ansible playbook exection command:

ansible-playbook pcf-app-deployment-ansible/pcf_push.yml -i pcf-app-deployment-ansible/pcf-app-local-deploy-hosts -f 1 -e space=Dev -e artifact_location=http://artifactory-location -e app_name=name-of-application -e branch_name=develop -e blue_green_route=1


The different blue green routes that can be taken:

1	Delete old app if new app deploy was successful
    To follow up:
       If not succesful, the new app should be manually cleaned up

2	Keep old app; Route all traffic to new app
    To follow up:
       If not succesful, route all traffic to old app; Delete new app
       If succesful, delete old app

3	Keep old; Create temp route to new app for isolated testing
    To follow up:
       If not succesful, delete new app	
       If succesful, Route all traffic to new app; Delete old app

4	Load balance traffic to old and new app
    To follow up:
       If not succesful, Delete new app	
       If succesful, Delete old app
