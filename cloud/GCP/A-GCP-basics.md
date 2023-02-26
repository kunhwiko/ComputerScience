### GCP Characteristics
---
##### GCP Differentiators
```
a) GCP utilizes Google's massive network and security infrastructure.
b) GCP is developed with global availiability in mind.
   Services such as load balancing are globally available rather than regionally available.
c) GCP VM instances are priced per second with a minimum runtime of 1 minute.
d) GCP data centers run on renewable energy.
```

### Locations
---
##### Region
```
a) Region (e.g. us-west1) is a geographical area that consists of multiple zones (e.g. us-west1-a).
b) Not all services are available in each region or zone.
c) Network latencies within regions should be under 1 ms in 95% of cases.
```

##### Zone
```
a) Zone is a deployment area within a region that could potentially consist of multiple clusters across different data centers. 
b) Single zone deployments are considered to be a single point of failure.
```

##### Network Edge Location
```
Connections to GCP services located in a particular metropolitan area.
```

##### Resource Scope
```
Global Resources
  a) Resources that are globally available within the same project.
  b) Examples include VPC networks, images, firewalls, routes, persistent disk snapshots.

Regional Resources
  a) Resources that are available only within the same region.
  b) Examples include subnets, regional persistent disks, regional managed instance groups.

Zonal Resources
  a) Resources that are available only within the same zone.
  b) Examples include VM instances, zonal persistent disks, zonal managed instance groups.
```

### Resource Management
---
##### Hierarchy
```
a) GCP organizes resources through an organization --> folder --> project --> resources hierarchy.
b) GCP resources are managed through Google APIs, which Google Cloud Console and command line tools use under the hood. 
```

##### Organization
```
a) Organization is the highest logical group that is only available for Google Workspace and Cloud Identity customers.
   Google Workspace or Cloud Identity accounts can have exactly one organization resource.
b) Organization provides centralized visibility and control to every resource that belongs to an organization.
   All projects must belong to some organization if an organization exists.
c) Initial IAM role for a newly created organization grants "Project Creator" and "Billing Account Creator" to all users in the domain.
```

##### Folders
```
a) Folders are logical groups that group projects or other sub-folders.
b) Folders can be used to model different departments or teams within a company.
c) Folders are optional and requires an organization as a prerequisite to use.
   This means folders are available only to Google Workspace and Cloud Identity customers.
```

##### Projects
```
a) Projects represent the smallest logical grouping in GCP and every GCP resource must belong to exactly one project.
b) Projects are identified through either a project ID or project number.
     * Project ID     : human readable identifiers that are frequently used alongside GCP APIs.
     * Project number : used for Google Cloud internal use (e.g. error logging, cloud support).  
c) Quotas limit the number of projects per account.
d) Initial IAM role for a newly created project grants Owner to the creator of the project.
```

##### IAM Inheritance
```
a) IAM policies can be set at an organization, folder, project, or resource level and are inherited throughout the hierarchy.
b) If a policy is set on a folder that grants Project Editor role to Alice, then Alice will have editor role on all projects under the folder.
c) If a policy is set on a folder that grants Project Editor role to Alice but the role is removed at a project level, Alice will still inherit and have editor role for the project.
d) If a policy is set on a folder that grants Owner to Alice but Viewer at a project level, Alice will still be granted Owner to the project.
e) If a project is moved to a new folder, it will remove previously inherited policies and inherit from its new parent resource. 
```

##### Best Practices
```
Google Workspace and Cloud Identity accounts come with super administrator roles that have high privileges.
It is best to limit the direct use of this role on GCP and assign Organization Administrator IAM roles to designated users. 
```

### Billing
---
##### Billing Accounts
```
Smallest entity that is billed is a project and a project can only be associated with one billing account.
There can still be multiple billing accounts that a project can choose from.
```

##### Billing Exports
```
a) GCP allows for exporting billing information to a BigQuery dataset or a file on Cloud Storage.
   After data is exported, users can perform queries to generate useful insights and reports.
b) Budgets and alerts can be set for each billing account or project, where the default alert thresholds are 50%, 90%, 100%.
   If an alert is attached to a billing account used by multiple projects, the alert counts towards the total cost of all projects. 
```

##### Billing Roles
```
Billing Account Creator
  a) Role for initial billing setup and to allow creation of additional billing accounts.
  b) This is an initial IAM role when an organization is created and the number of users with this role should be limited.

Billing Account Administrator
  a) Owner role of a billing account to manage payments, other user roles on the account, billing exports, and link or unlink projects to the account.
  b) Role for managing a given billing account but cannot create new billing accounts.

Billing Account Costs Manager
  a) Role for creating, editing, and deleting budgets, viewing costs and transactions, and managing billing exports.
  b) Role does not give right to export pricing data, view custom pricing, link projects, and manage properties of the billing account. 

Billing Account Viewer
  a) Role for allowing access to view billing information but nothing more.

Billing Account User
  a) Role that has very restricted permissions and can be granted broadly.
  b) If user has "Project Creator" role, this role allows for the creation of new projects linked to the given billing account.
  c) If user has "Project Billing Manager" role, this role allows for linking or unlinking those projects to the given billing account.
     This does not give the rights to resources in the project.
```

### Computing and Hosting Services
---
##### Google Compute Engine (GCE)
```
GCE is Google's infrastructure-as-a-service offering and gives full control over instance hardware, operating system, region/zone, networking, and autoscaling.
```

##### Google Kubernetes Engine (GKE)
```
GKE is Google's version of Kubernetes and container-as-a-service offering.
GKE leverages GCE for hosting cluster nodes and integrates GCP software defined networks.
```