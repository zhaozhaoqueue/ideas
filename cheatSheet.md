# Git & GitHub
```shell
# add username and email for all repos
git config --global user.name <username>
git config --global user.email <email>

# erase the token when PAT expires, next time, the git will prompt window to enter new token
echo url=https://lukashinext@github.com | git credential reject

# push commit to remote repo's branch
git push <remote-name> <branch-name>

# add a remote repo
git remote add <remote-name> <remote-url>

# difference between pull and fetch
# Git Fetch is the command that tells the local repository that there are changes available in the remote repository without bringing the changes into the local repository. Git Pull on the other hand brings the copy of the remote directory changes into the local repository. Let us look at Git Fetch and Git Pull separately with the help of an example.

# rename the local branch and push it to remote branch
git push <remote-name> <local-branch-name>:<remte-branch-name>

# check last commit on each local branch
git branch -v

# check all branches
git branch -a

# check remote branches only
git branch -r

# crate a new branch locally from a existing branch and switch to it
git checkout -b <branch-name> <existing-branch-name>

# switch branch
git checkout <branch-name>

# delete a branch
git branch -d <branch-name>

# compare two branch (local or remote)
git diff <branch-name> <branch-name>

# push a new local branch to remote repo
git push --set-upstream <remote-name> <branch-name>

# Before fetching, remove any remote-tracking references that no longer exist on the remote
git fetch --prune origin

# rename current branch
git branch -m <new-branch-name>

# restore a file 
git restore <file-name>

# merge specific file of a branch to another branch
git checkout <target-branch>
git checkout --patch <source-branch> <file-path>

# fetch a remote branch to local
git switch <branch-name>

# remove a file from git repo but not delete it
git rm --cached file1.txt
git commit -m "xxx"

# sync the forked repo
## list the current configured remote repository for your fork
git remote -v
## Specify a new remote upstream repository that will be synced with the fork
git remote add upstream <forked_repo_url>
## get the branches of upstream
git fetch upstream


# remove the added but not committed files, remove all without file_name
git reset <file_name>
````

# Docker
- Cannot build image from a Dockerfile in home directory, move them to another directory and cd to that directory

```shell
# build image from Dockerfile
docker build -t <image-tag> .

# build image
docker buildx build -t <image-tag> --progress plain . --load


# get container ip from host
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container-name>

# get container port
docker image inspect <image-name> -f '{{ .ContainerConfig.ExposedPorts }}'

# start mysql container
docker run --name <contaienr-name> -e MYSQL_ROOT_PASSWORD=<password> -d <image-name>

# list all containers only with id (for batch operation)
docker ps -aq

# remove all containers
docker rm $(docker ps -aq)

# run a container listening host port
docker run -p <host-port>:<container-port> ...

# save a image to tar file
docker save <image_name> > <file_name>.tar

````

# Postgresql Syntax
```sql
# connect to database named postgres, this is a default database
psql postgres

# find the config file path
show config_file;

# list databases
\list
# create database
create database test
# switch database
\connect test
# list tables
\dt
# check privileges
\du+
# add privileges of table to role
GRANT <privilege_list> ON <table_name> TO <role_name>;
# describe table 
\d <table_name>;
# check publication
SELECT * FROM pg_publication;
# check public tables;
SELECT * FROM pg_publication_tables;
# drop table from a publication
ALTER PUBLICATION <publication_name> DROP TABLE <table_name>;
# check slots;
SELECT * FROM pg_replication_slots;
# drop publication
DROP PUBLICATION <publication_name>;
# drop slot
SELECT pg_drop_replication_slot('<slot_name>');
# add column
ALTER TABLE <table_name> ADD COLUMN <column_name> <data_type> [<constraint>];
# alter column data type
ALTER TABLE <table_name> ALTER COLUMN <column_name> TYPE <data_type>;
# drop column
ALTER TABLE <table_name> DROP COLUMN IF EXISTS <column_name>;
# rename column
ALTER TABLE <table_name> RENAME COLUMN <column_name> TO <new_column_name>;

# check column data types
select column_name, data_type from information_schema.columns where table_name = '<table_name>';

# check slot info (lag, etc.)
select slot_name, pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(),restart_lsn)) as replicationSlotLag,active, confirmed_flush_lsn, restart_lsn from pg_replication_slots;

# get the value of a parameter
SELECT * FROM pg_settings WHERE name='<parameter_name>';
select name, setting, unit, context from pg_settings where name = '<parameter_name>';

# check table's replica mark
select * from pg_class;

# create a logical slot
SELECT * FROM pg_create_logical_replication_slot('<slot_name>', '<decode_pulgin_pgoutput>');

# drop subscription
ALTER SUBSCRIPTION <subscription_name> diable;
ALTER SUBSCRIPTION <subscription_name> set (slot_name = none);
DROP SUBSCRIPTION <subscription_name>;

# check replica idnetify of table
SELECT CASE relreplident
          WHEN 'd' THEN 'default'
          WHEN 'n' THEN 'nothing'
          WHEN 'f' THEN 'full'
          WHEN 'i' THEN 'index'
       END AS replica_identity
FROM pg_class
WHERE oid = 'mytablename'::regclass;
````

# Connect to dataeng_test
```
psql -h postgres-dev-us-west-2-001.cjajdxaaxdqr.us-west-2.rds.amazonaws.com -p 5432 -d dataeng_test -U dataeng_test_user

password: zyP4lIWKnw/ChPIcuwst9jEDz/CMPgcKjekuhZ0vfTZ86KsBI5UW3c8Nzutzd80I
```


# Kafka
```shell
# start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# start kafka server
bin/kafka-server-start.sh config/server.properties
```
### kafka connect
```shell
# start kafka connect cluster
bin/connect-distributed.sh config/connect-distributed.properties

# check the status of the kafka connect service
curl -H "Accept:application/json" localhost:8083/

# check all plugins info (class, version)
curl -H "Accept:application/json" localhost:8083/connector-plugins

# check the list of connector registered to kafka connect
curl -H "Accept:application/json" localhost:8083/connectors/

# check connector status
curl -H "Accept:application/json" localhost:8083/connectors/{name}/status

# delete connector
curl -H "Accept:application/json" -X DELETE localhost:8083/connectors/{name}

# check connector's topics
curl -H "Accept:application/json" localhost:8083/connectors/{name}/topics

# validate the provided configuration values against the configuration definition; connector-type: source or sink
curl -i -X PUT -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connector-plugins/{connector-type}/config/validate

# deploy a connector
curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d '<configs>'

# update a connector's configuration
curl -i -X PUT -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/{name}/config -d '<config>'

# restart a task of a connector
curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/{name}/tasks/{taskId}/restart
````

# Mysql
```sql
# check all columns
desc <table_name>;

# show location of configuration file

```

# Brew
```shell
# force to uninstall a package
brew uninstall --force <package_name>

# override the link
brew link --overwrite <cmd_name_like_node>
```

# minikube
```shell
# start
minikube start

# start dashboard
minikube dashboard

# list images
minikube image list

# pull a image
minikube image pull <image-name>

# check docker env for minikube
minikube docker-env
## change 

# remove a image
minikube image rm <image-name>

# load a image from file
minikube image load <image-file.tar>

# mount a directory
minikube mount <source directory>:<target directory>

# open command line
minikube ssh
```

# Kubernetes
```shell
# check k8s version
kubectl version

# check the contexts
kubectl config get-contexts

# swtich context
kubectl config use-context <context-name>

# get pods
kubectl get pod -A
kubectl get pod -n <name_space>

# expose a port of a pod
kubectl port-forward <pod_name> <port> -n <name_space>

# get the yaml of a deployment
kubectl get deploy <deploy_name> -n <name_space> -o yaml

# copy file from local to pod (cntainer)
kubectl cp </path/to/file> <name_space>/<pod_name>:<path/in/pod> [-c <container_name>]

# delete or execute some command in batch on the pod with prefix
kubectl get pods -n <name_space> --no-headers=true | awk '/<PREFIX>/{print $1}' | xargs kubectl delete pod -n <name_space> 

# force delete
kubectl delete pod -n <name_space> <pod_name> --force --grace-period=0
```

# helm
## concept
#### Chart: Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. Think of it like the Kubernetes equivalent of a Homebrew formula, an Apt dpkg, or a Yum RPM file.
#### Repository: the place where charts can be collected and shared
#### Release: an instance of a chart running in a Kubernetes cluster.

## trouble shooting
- “helm” cannot be opened because the developer cannot be verified.
```shell
sudo spctl --master-disable
```
## usage
### add repo to local
```shell
helm repo add <customized_name> <repo_url>
``` 
### install a new package
```shell
helm install <release_name> <chart_name> -n <name_space>
```
### check release's status
```shell
helm status <release_name>
```
### show the configurable options on a chart
```shell
helm show values <chart_name>
```
### override settings with yaml
```shell
# release name is generated by helm
helm install -f <yaml_file_name> <chart_name> --generate-name
```
### overide settings with --set
- check https://helm.sh/docs/intro/using_helm/
### upgrade a release
when the new version of chart is released, or you want to change the configuration of your release
```shell
helm upgrade -f <yaml_file_name> <release_name> <chart_name>
```


# Terraform
## concept
- provider: 
- resource: represent one or more infrastructure objects
- module: a set of Terraform configuration files in a single directory. When you run Terraform commands directly from such a directory, it is considered the root module. your configuration can use module blocks to call modules in other directories. When Terraform encounters a module block, it loads and processes that module's configuration files. A module that is called by another configuration is sometimes referred to as a "child module" of that configuration.
- variable: Input variables make your Terraform configuration more flexible by defining values that your end users can assign to customize the configuration.
    - assign vule through command line flag
    ```shell
    terraform apply -var ec2_instance_type=t2.micro
    ```
    - assign values with a file
    ```
    - terraform.tfvars
    - *.auto.tfvars
    - (command line flag) -var-file
    ```
    - Interpolate variables in strings
- locals (local value): assign a name to an expression or value
- outputs: export structured data about your resources. You can use this data to configure other parts of your infrastructure with automation tools, or as a data source for another Terraform workspace. Outputs are also how you expose data from a child module to a root module.
- data (data source): dynamically fetch data from APIs or other Terraform state backends (some data sources is part of the provider)
## resource dependencies
- The most common source of dependencies is an implicit dependency between two resources or modules.
- explicit dependencies: The depends_on argument is accepted by any resource or module block and accepts a list of resources to create explicit dependencies for.
## language
- count: replicates the given resource or module a specific number of times with an incrementing counter.
- templatefile: dynamically generate a script. Terraform will interpolate the values like ${xxx} into the script.
- lookup: retrieves the value of a single element from a map, given its key.
- file: read the contents of a file





# AWS
## aws client
```shell
# check aws version
aws --version

# update the k8s config to let kubectl communicate with the cluster
aws eks update-kubeconfig --region <region_name> --name <cluster_name>

# list aws configs, the config file location is ~/.aws/config, the access key id and access key are stored in the ~/.aws/credentials
# the name at the end of the " mfa_serial" should be the same as the display name on MFA
aws configure list

# get the current account info, as well as the accessable resources
aws sts get-caller-identity

```

## aws-vault
```shell
# list the existing session and credential
aws-vault list

# clear all the sessions
aws-vault clear

# add the access key id and secret access key to keychain
# if fails with error "... unable to validate MFA code ...", make sure the name at the end of the " mfa_serial" should be the same as the display name on MFA
aws-vault add --backend=keychain root

# login to aws env
aws-vault login <env_name> -t <mfa_code>



# after login through aws-vault login

# switch to an environment, which means assume the ops role of a environment to the current account
# now we can execute the command like: kubectl, vault (with VPN enable)
aws-vault exec <env_name>



```

- script to execute the vault login
```shell
# usage: source vault_login.sh [dev|test|demo|prod]
vault_login() {
  export VAULT_ADDR=https://vault.internal.$1.aws.nexttrucking.com/
  vault login -method=aws header_value=vault.$1.nexttrucking.com role=vault_admin
}

# vault_login [prod|demo]
# vault read database/creds/[service_name]
vault_login $@
```


# Vault
```shell
# list keys
vault kv list secret/<path>

# get a key
vault kv get secret/<path_to_key>

# get a field of a key
vault kv get -field=<field_name> secret/<path_to_key>

# add or update a key; notice: all the fields will be replaced by the new one
vault kv put secret/<path_to_key> <field_name>=<value>


# add or update a field of a key
vault kv patch secret/<path_to_key> <field_name>=<value>

# rollback a key
vault kv rollback -version=<verion_index> secret/<path_to_key>
```





