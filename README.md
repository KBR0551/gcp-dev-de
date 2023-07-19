# gcp-dev-de


----- starting off with commands -----

Gcloud commands:

Gcloud iam service-accounts create 'composer-dev' (creates service account ) 

gcloud iam service-accounts list  ( to setup a role we will need the email address of the service account) 

gcloud iam service-accounts keys create ./keys/my_secret_composer_key.json --iam-account='composer-dev@de-dev-390823.iam.gserviceaccount.com'


cloud Composer uses the following GCP services to run: Cloud SQL, App Engine, Cloud Storage, Kubernetes Engine, Cloud Logging, Cloud Monitoring, and Pub/Sub

-- binding composer.woeker policy to our service account in   'de-dev-390823'  project

gcloud projects add-iam-policy-binding 'de-dev-390823' --member='serviceAccount:composer-dev@de-dev-390823.iam.gserviceaccount.com' --role='roles/composer.worker'

glocud projects add-iam-policy-binding 'de-dev-390823' --member='serviceAccount:composer-dev@de-dev-390823.iam.gserviceaccount.com' --role='roles/bigquery.admin'

-- Creating composer environment (aka air flow instance )

gcloud composer environments create my-dev-environment \
--location=us-central1 \
--zone=us-central1-f \
--machine-type=n1-standard-1 \
--image-version=composer-1.20.12-airflow-2.4.3 \
--python-version=3 \
--node-count=3 \
--service-account=composer-dev@de-dev-390823.iam.gserviceaccount.com

gcloud composer environments list --locations us-central1

gcloud composer environments delete my-dev-environment --location=us-centeral1

Air Flow: Dags, tasks, operators 


--- getting the GCS bucket for composer environment.
gcloud composer environments describe my-dev-environment \
    --location us-central1 \
    --format="get(config.dagGcsPrefix)"

--uploading DAG's to gcs bucket
Gcloud composer environments storage dags import \
--enviroment=my-dev-environment
--location=us-central1
--source=my_first_dag.py


> gcloud composer environments delete my-dev-environment --location us-central1

> gsutil list
gs://us-central1-my-dev-environm-63db6d2e-bucket/

> gsutil rm -r gs://us-central1-my-dev-environm-63db6d2e-bucket/


Cli to enable google apis : > gcloud services enable dataproc.googleapis.com

Launching  a data proc cluster: 
> gcloud dataproc clusters create my-cluster \
    --region=us-central1 \
    --num-workers=2 \
    --worker-machine-type=n2-standard-2 \
    --image-version=1.5-debian10
   (check additional fields) metadata, initialization-actions  

Submitting a job to dataproc cluster:
> gcloud dataproc jobs submit pyspark \
    gs://de-book-dataproc/flattener.py \
    --cluster=my-cluster \
    --region=us-central1

Deleteing a dataproc cluster:
Glocud dataproc delete cluster <cluster_name> <region>

Data procs and composer (using command line arguments in composer tasks) 

-- Updating composer environment ---
> gcloud composer environments update my-environment \
    --update-pypi-packages-from-file requirements.txt \
    --location us-central1
![image](https://github.com/KBR0551/gcp-dev-de/assets/98926998/d1bdb7a1-09b7-4391-8cb3-9038712ce395)
