# Overview

Calculating cloud cost can be tricky and cloud vendors are not making it easy for end-users. 

The cost of a `cloudor` job, however, is simple: it consists of instance cost and network(egress) cost. 
Instance costs are listed by `cloudor vendor` and are pro-rated by second (from when the instance is booted untill the job is done).

Please note that it is important to keep the credit positive to run cloudor jobs because an estimated credit needs to be reserved before the job is executed.
The job's estimate cost is done based on the vendor, region, instance type and timeout value at the submission. 
The job will be rejected if there is insufficient credit remaining.

Newly registered users are given "start-up" credits for a couple hours of instance time on the majority of our supported cloud instances.

The user credit can be managed at the cloudor's user console.
