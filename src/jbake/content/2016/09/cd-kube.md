Continuous Delivery with Kubernetes. Part 1
TL;DR
What, How, Why of implementing Continuous Delivery with Kubernetes.
What
Kubernetes is a fantastic scheduler for containers, but like any new fangle dangle tool it’s all a bit pointless if you're still doing things manually. In this article I’m going to explain how we configured our CD pipeline, why we made certain decisions and the impact they have, and finally discuss future improvements.
How
When deciding which automation tool to use, we had a few principles in mind:
A dashboard showing our delivery pipeline is desirable to non-technical users
Job chaining allows us to fail fast and only run a job when an upstream task has completed successfully
Fanning is the concept of running the same job in parallel for different “flavours”. One example may be running testing in parallel for multi-browsers
CD as Code
The build definitions should live alongside the code and be version controlled
Cloud Aware (aws, kubernetes)
We want to move away from traditional build artifacts and move towards baking runtime environments. This could be baking traditional VMs or building Docker images

We were already adopting so many new tools that we also had a requirement to use something familiar. We toyed with Bamboo for a little while, but settled on Jenkins mainly because of requirement 4.
In the first instance we sketched out how the pipeline would look. We were heavily influenced by the Continuous Delivery book, by David Farley and Jez Humble. So, if you have read that book then this will feel familiar.
node {
 // Commit Phase
 stage ‘Build’
stage ‘Analysis’
 
 stage ‘Integ tests’
stage ‘Create image’
stage ‘Publish image’
// Acceptance Phase
 stage ‘Deploy image’
 
 stage ‘Smoke tests’
 
 stage ‘Acceptance tests’
 
 stage ‘Promote image’
 
 // User Acceptance Phase
 stage ‘Deploy image UAT’
 
 stage ‘Smoke tests’
 
 stage ‘Exploratory tests’
 
 // Production phase
 stage ‘Deploy image PRD’
 
 stage ‘Smoke tests’
}
stage ‘Analysis’
 
 stage ‘Integ tests’
stage ‘Create image’
stage ‘Publish image’
// Acceptance Phase
 stage ‘Deploy image’
 
 stage ‘Smoke tests’
 
 stage ‘Acceptance tests’
 
 stage ‘Promote image’
 
 // User Acceptance Phase
 stage ‘Deploy image UAT’
 
 stage ‘Smoke tests’
 
 stage ‘Exploratory tests’
 
 // Production phase
 stage ‘Deploy image PRD’
 
 stage ‘Smoke tests’
}
