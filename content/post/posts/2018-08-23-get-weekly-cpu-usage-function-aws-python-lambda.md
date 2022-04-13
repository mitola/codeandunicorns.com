---
title: 'Get weekly CPU usage function with AWS Python Lambda'
date: 2018-08-23T15:25:35+00:00
authors:
- admin

categories:
  - How to
  - programming
---
![lambdapython](posts/lambdapython.png "")
Let's first cover the gist of the actual functionality that will be responsible for giving us averaged CPU utilization during a specific time frame. The code itself is a run of the mill kind of code but already coupled in a nice working example so quite easy adapted.

If we take a look at the function bellow:

```
import boto3
import sys
from datetime import datetime, timedelta

def get_cpu_util(instanceID,startTime,endTime):

        client = boto3.client('cloudwatch')
        response = client.get_metric_statistics(
            Namespace='AWS/EC2',
            MetricName='CPUUtilization',
            Dimensions=[
                {
                'Name': 'InstanceId',
                'Value': instanceID
                },
            ],
            
            StartTime=startTime,
            EndTime=endTime,
            Period=86400,
            Statistics=[
                'Average',
            ],
            Unit='Percent'
        )
        
        for k, v in response.items():
            if k == 'Datapoints':
                for y in v:
                    return "{0:.2f}".format(y['Average'])
```

We can see it accepts instanceID,startTime and endTime, which should optimally be of <a href="https://docs.python.org/2/library/datetime.html" target="_blank" rel="noopener">datetime</a> type format.  
In namespace we define AWS/EC2 but it can be also for example RDS etc. just check the AWS docs on the namespaces.

Other atributes should be quite selfexplanatory.

At the end there is a for loop that cycle's through all data of the response and only returns the average time for the period.

One level higher we connect that function to connect it in a higher amount of instances (all of the instances inside the account) that get cycled through.

To get all of the instances with ID and only the ones that are running we need follow code for Python AWS lambda.

```
#get all instances
    instanceIDs=[]
    ec2 = boto3.resource('ec2')
    instances = ec2.instances.filter(
        Filters=[{'Name': 'instance-state-name', 'Values': ['running']}])
```

With the following code we only get the running instances, and beacuse a lot of times you would also like to have human readable name of the instance we use the following code to get the name key tag.

```
for instance in instances:
        tmpDict=get_cpu_util(instance.id,startTime,endTime)
        if tmpDict:
            for tags in instance.tags:
                try:
                    if tags['Key'] == 'Name':
                      tmpDict['Name']=tags['Value']
                      break
                except KeyError: pass
            else:
                print("Name not found")
```

A short and sweet Python combo, hopefully it is useful to someone