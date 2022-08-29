## Create a repo in Github

* ### Set main:

    ```
    git branch -M main
    ```
* ### Add remote:

    ```
    git remote add origin git@github.com:notaarguello/devops_for_non_it_ones.git
    ```
## Gcloud config:

* ### Listing:


    ```
    gcloud info
    ```

    or..

    ```
    gcloud config configurations list
    ```

* ### Creating a new config:

    ```
    gcloud config configurations create <new_config_name>
    ```

    You'll have to add your user and define a project after that one (your new config gets activated after creation but remember that the following command works on your active config):

    ```
    gcloud config set account "your.account.email@provider.com"
    gcloud config set project "your-gcloud-project-id"
    ```

* ### Activating a config:

    ```
    gcloud config configurations activate <config_name>
    ```

    Tip: Use direnv bash plug-in and put that command in a ```.envrc``` file inside your project folder.

## SHH into google cloud shell

    ```
    gcloud cloud-shell ssh
    ```

## Coping local data to gcs:

```
gsutil cp -r my_random_folder/* gs://$DESTINATION_BUCKET_NAME/folder/
```

-r Flag means recursive, it's just like standard bash cp command.

Tip: -m flag to parallelize.

## Working with SQL instances

* ### Creating SQL instance:

    ```
    gcloud sql instances create mysql-instance-source  \
    --database-version=MYSQL_5_7 \
    --tier=db-g1-small \
    --region=us-central1 \
    --root-password=psw123 \
    --availability-type=zonal \
    --storage-size=10GB \
    --storage-type=HDD
    ```

* ### Connect to SQL instance:

    ```
    gcloud sql connect mysql-instance-source --user=root
    ```

    You'll have to have a mysql client installed locally. Installing mysql and then doing ```export PATH=$PATH:/usr/local/mysql/bin/``` will do the trick (Again, you can put that one in your ```.envrc``` file).

* ### Export data from SQL to GCS:

    ```
    gcloud sql export csv mysql-instance-source \
    gs://<full_file_path>.csv \
    --database=<your_db> \
    --offload \
    --query='SELECT * FROM your_table;'
    ```

    Note: mysql-instance-source will need IAM access to write to GCS (storage object admin role).

## Cloud Composer

* ### Loading a DAG to Cloud Composer
    ```
    gcloud composer environments storage dags import \
    --environment [Your Cloud composer environment name] \
    --location [Your Cloud composer region] \
    --source [DAG Python file].py
    ```

* ### Delete a dag from Cloud Composer
    ```
    gcloud composer environments storage dags delete \
    --environment [Your Cloud composer environment name] \
    --location [Your Cloud composer region] \
    [DAG Python file].py
    ```

* ### Create env vars for cloud composer
    ```
    gcloud composer environments create --env-variables=['MYSQL_INSTANCE_NAME'='mysql_instance']
    ```

## Dataproc

* ### Create cluster

    ```
    gcloud dataproc clusters create packt-dataproc-cluster --enable-component-gateway --region us-central1 --zone us-central1-a --master-machine-type n1-standard-2 --master-boot-disk-size 30 --num-workers 2 --worker-machine-type n1-standard-2 --worker-boot-disk-size 30 --image-version 2.0-debian10 --optional-components JUPYTER
    ```

* ### SSH into cluster

    ```
    gcloud compute --project "packt-gcp-data-eng-notaarg" ssh --zone "us-central1-a" "packt-dataproc-cluster-m"
    ```

* ### Your hadoop master node has access to gcloud so...

    ```
    gsutil cp gs://packt-gcp-data-eng-notaarg-data-bucket/from-git/chapter-5/dataset/simple_file.csv ./
    ```
