# Host a Web App on Google Cloud Using Compute Engine

- **Official lab:** https://www.skills.google/course_templates/754/labs/597889
- **Status:** Completed

## Objectives

Explore how to deploy a sample application, the "Fancy Store" e-commerce website, to show how a website can be deployed and scaled easily with **Compute Engine**

- Create Compute Engine instances.
- Create instance templates from source instances.
- Create managed instance groups.
- Create and test managed instance group health checks.
- Create HTTP(S) Load Balancers.
- Create load balancer health checks.
- Use a Content Delivery Network (CDN) for caching.
## Notes and commands

- Each region has multiple zones (us-east4) -> (us-east4-a, us-east4-b, ...)

- Enable Compute Engine API
```bash
gcloud services enable compute.googleapis.com
```

- we use cloud storage bucket to hold built code and startup scripts
- From cloud shell, create new cloud storage bucket:
```bash
gcloud storage buckets create gs://fancy-store-qwiklabs-gcp-03-e6aa98e1d968
```

- Copy file into bucket:
```bash
gcloud storage cp <file-path> <bucket-link>

# Example
gcloud storage cp ~/monolith-to-microservices/startup-script.sh gs://fancy-store-qwiklabs-gcp-03-e6aa98e1d968
```

- Create Compute Engine VM instance:
``` base
gcloud compute instances create backend \
    --zone=$ZONE \
    --machine-type=e2-standard-2 \
    --tags=backend \
    --image-family=debian-12 \
    --image-project=debian-cloud \
    --metadata=startup-script-url=https://storage.googleapis.com/fancy-store-qwiklabs-gcp-03-e6aa98e1d968/startup-script.sh
```

- list Compute Engine VM instances:
```bash
gcloud compute instances list
```

- create firewall-rules using tags of instances:
```bash
gcloud compute firewall-rules create fw-fe \
    --allow tcp:8080 \
    --target-tags=frontend

gcloud compute firewall-rules create fw-be \
    --allow tcp:8081-8082 \
    --target-tags=backend
```

- stop Compute Engine VM instance:
```bash
gcloud compute instances stop <name> --zone=
```

- create instance template from source instance:
```bash
gcloud compute instance-templates create fancy-fe \
    --source-instance-zone=us-east4-b \
    --source-instance=frontend

```
ip=136.82.108.117

- delete compute engine vm instance:
```bash
gcloud compute instances delete backend --zone=us-east4-b
```

- create managed instance groups:
```bash
gcloud compute instance-groups managed create fancy-fe-mig \
    --zone=us-east4-b \
    --base-instance-name fancy-fe \
    --size 2 \
    --template fancy-fe

```

- create a health check that repairs the instance:
```bash
gcloud compute health-checks create http fancy-fe-hc \
    --port 8080 \
    --check-interval 30s \
    --healthy-threshold 1 \
    --timeout 10s \
    --unhealthy-threshold 3
```

## Evidence
-
## Reflection
- What did I learn?
- What would I repeat or improve?
