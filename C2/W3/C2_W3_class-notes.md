
This week is all about DataOps, but really it includes elements of the software engineering and data management undercurrents as well. 

As you learned in the previous course, Dataops is a set of practices and cultural habits centered around building robust data systems and delivering high quality data products. 

![[Pasted image 20251207184103.png]]

And DataOps really emerge from DevOps, which is the set of practices and cultural habits that allow software engineers to efficiently deliver and maintain high quality software products. 

![[Pasted image 20251207184114.png]]

This week, we're going to get into the details of each of the three pillars of data ops, namely automation, observability and monitoring, and incident response. 
![[Pasted image 20251207184126.png]]

Well, actually, to be fair, we're going to spend more time looking at automation and observability and monitoring, and not as much time with incident response. But that's not because incident response is less important. It's just that incident response has more to do with the cultural habits side of data ops, and it's harder to build practical exercises around that in an online course. 



Anyhow, when it comes to automation, we'll be revisiting some of the ideas covered in the previous course, like continuous integration and continuous delivery or CI/CD, and then zeroing in on the concept of infrastructure as code, which is to say, writing code that when you run it deploys the resources required to run your data pipeline. 
![[Pasted image 20251207184229.png]]

- In the previous labs in these courses, you've gotten some experience spinning up certain resources for your data infrastructure using the AWS console. 

- On the job as a data engineer, however, it's becoming common practice to do this deployment through infrastructure as code frameworks rather than manually spinning up instances and installing software. 


![[Pasted image 20251207184328.png]]
- In the first lab this week, you'll get hands on experience actually writing the code in terraform to deploy your infrastructure. 
- And after that, in the labs that follow, you'll build on top of your infrastructure to include monitoring of your data quality and other important observability metrics. 

Another exciting thing we have in store for you this week is a set of interviews with industry experts from the field of data ops. In these interviews, you'll be hearing about the key aspects of **data quality** and **data observability** from people who are actually building products to serve data engineers in these areas. 

## DataOps Automation
![[Pasted image 20251207184126.png]]
In this video, we're going to look at the **automation** pillar of DataOps. And as I said in the previous video, DataOps emerged from DevOps. 

And while there are significant differences between the requirements for delivering high quality software applications and those for delivering high quality data products, there's still a lot of overlaps. And so here, we'll be looking at some of the concepts that DataOps borrows from DevOps when it comes to automation. 

**CI/CD**
Within software engineering, one key aspect of automation is the practice of continuous integration and continuous delivery, or **CI/CD** for short. In the context of software, the CI/CD process involves 
- setting up systems for **automatic review** and **testing of new code**. 
- And then the **automatic delivery or deployment into production** of code that has been reviewed and tested. 

![[Pasted image 20251207190202.png]]



When it comes to DataOps, the practice of CI/CD can be applied directly to code and data within your data pipeline, much as it would be for a software product. 

![[Screenshot 2025-12-10 at 14.26.06.png]]
- whether that's code for applying a particular set of data transformations or populating a database or the data itself, you can maintain it just like you would any other piece of software application code. 


When it comes to actually *running* your data pipelines, as I talked about in the previous course, this is something you could do in a number of different ways. 

1. Have no automation and just run all the processes in your data pipeline manually. ![[Screenshot 2025-12-10 at 14.32.15.png]]
- Or you could set up the stages of your pipeline to run according to a particular schedule. ![[Screenshot 2025-12-10 at 14.32.30.png]]
- Or you could orchestrate your pipeline by defining it as a directed acyclic graph, or DAG, using an orchestration tool like Airflow. ![[Screenshot 2025-12-10 at 14.32.40.png]]

>	*We'll look at DAGs and the automation of testing and deployment more closely next week when we get into orchestration.* 

Now, one key underpinning **of any CI/CD system**, whether for a software product or a data product, is "**version control**", where each new code version of the code is *recorded*. ![[Screenshot 2025-12-10 at 14.33.56.png]]

This makes it possible to easily revert back to a previous version if for some reason the current version isn't working as expected or other problems occur. ![[Screenshot 2025-12-10 at 14.34.10.png]]

You might already be familiar with version control in the context of your own code, maybe using a platform like GitHub. And so within DataOps, the concept of version control also applies to data. Just like you can track changes in your code and roll back to a previous version, with DataOps, you can track changes in the data moving through your pipelines. And be able to roll back to a previous version of the data if you run into problems. 



**Infrastructure as Code**
Another concept that DataOps borrows from DevOps in terms of automation is *infrastructure as code*. Whether you're building software applications or data pipelines with cloud platform resources, it's possible to **maintain the design of your infrastructure as a code base**, just like you would for any other application code. 

You can run that code to deploy your infrastructure, or modify the code to redefine your infrastructure, and then run it again to deploy the updated infrastructure. Now, by defining your infrastructure programmatically using code, you can then maintain version control over your entire infrastructure, just like you would for any other piece of code or for your data. 
![[Screenshot 2025-12-10 at 14.36.26.png]]
And that way, if you need to roll back to a previous version of your infrastructure, it's as simple as rolling back to a previous version of your code. 

And so there are a number of ways in which DataOps automation practices will be part of your work as a data engineer. You can start to see how DataOps begins to overlap with the other undercurrents of the data engineering lifecycle. 
![[Screenshot 2025-12-10 at 14.37.16.png]]


## Infrastructure as Code

As I mentioned in the previous video, you can use Infrastructure as Code to programmatically define, deploy, and maintain your cloud infrastructure. This means you can automate the creation of all the resources you need for your cloud data pipelines, including networking, security, computing, storage, and other data management and analytics resources. 

But the concept of Infrastructure as Code actually **precedes** cloud computing and has much older roots in configuration management, dating back to the 1970s. Even back then, engineers struggled to efficiently configure and manage a series of physical machines. They would write BAS scripts to automate some configuration tasks, and looking back, you can think of this as the sort of primordial roots of Infrastructure as Code. 
![[Screenshot 2025-12-10 at 14.38.47.png]]

With AWS's release of EC2 in 2006, anyone could easily spin up cloud computing resources whenever they needed them, so, engineers were able to build more scalable applications with many components and complex dependencies. 
![[Screenshot 2025-12-10 at 14.39.14.png]]

In the early 2010s, software engineers developed **Infrastructure as Code** tools like *Terraform*, *CloudFormation*, and *Ansible* that allowed them to provision and configure their infrastructure using code-based configuration files. 
![[Screenshot 2025-12-10 at 14.40.42.png]]


Nowadays, you can use these tools to easily manage infrastructure resources on the cloud with lines of code, rather than manually clicking through resource setup windows or writing tedious BASH scripts. 
![[Screenshot 2025-12-10 at 14.41.02.png]]

![[Screenshot 2025-12-10 at 14.41.55.png]]


## Data Observability

In addition to the principles of automation that data engineers borrow and build on from DevOps, the principles and practices of observability and monitoring are a key pillar of DataOps that also has its origins in software development. 

![[Screenshot 2025-12-17 at 15.36.44.png]]



Over the past decade or so, with the advent of the cloud and the move toward distributed systems, software engineers have developed observability tools help their teams gain visibility into the health of their systems. 

With these observability tools, teams are able to monitor metrics such as CPU and RAM usage and response time, which helps to quickly detect anomalies, identify problems, prevent downtime, and ensure reliable software products. 

![[Screenshot 2025-12-17 at 15.37.36.png]]


When it comes to data observability and monitoring the health of your data systems, some of the same tools that software teams rely on can also be helpful to you as a data engineer. With that being said, in addition to monitoring for things like CPU usage or system response times, as a data engineer, y**ou also need visibility into the health, or in other words, the quality of your data**. 

As a reminder, in the previous course, we defined high quality data as *data that is accurate, complete, discoverable, and available in a timely manner.*  And beyond that, high quality data represents exactly what stakeholders expect it to represent in terms of a well defined schema and data definitions. 

![[Screenshot 2025-12-17 at 15.39.10.png]]

Low quality data is the opposite,, and it might be inaccurate, incomplete, or otherwise unusable. 


- When you are able to provide high quality data to stakeholders in your organization, you're providing value for those stakeholders. But believe it or not, providing low quality data is actually worse than providing no data at all. 
- When business decisions get made based on low quality data, it can be extremely costly for an organization, and it can leave stakeholders second guessing the value of the data team. 

![[Screenshot 2025-12-17 at 15.40.18.png]]



To complicate matters further, data systems that provide poor quality data might look perfectly healthy on the outside. For example, let's say you have a software application like a mobile app or a website. If the app crashes, or if you get a 404 error when trying to load a webpage, it's pretty obvious that something has gone wrong. And those are the kinds of things that can trigger an alert to a software engineer so they can go fix the problem. 

With the data system, if your system simply stops working and you're able to recognize immediately that it's not working, that's actually your best case scenario in terms of potential failure modes. In this case, you can debug the problem and get your system up and running again. If, on the other hand, your data suffers a breaking change such that your system still works but is no longer providing high quality output, that's when things can get really ugly. 

*Example: (Left, expected, right, reported)*
![[Screenshot 2025-12-17 at 15.42.09.png]]

And so in terms of data quality, this would be an example of where, in the blink of an eye, your system went from serving high quality data to serving data that was no longer accurate, or at least was not what stakeholders expected it to be. 

Now, you may think that these errors may not be your responsibility since they originated from a software error or similar, but upstream changes that disrupt or break your data systems should be expected and mitigated. Really, no matter where it comes from, once the data is in your hands, it's your responsibility to ensure its quality. So when there's a disruption to your data system, how do you make sure you know what happened as soon as possible? Well, that's where data observability and monitoring comes in.

>	*Data observability is the ability to understand the health of your data systems, be able to improve it over time.*

--- 

Data issues are inevitable and they could occur at any stage of your data pipeline. The earlier you are able to detect them, the less damage to the organization it will cause. To detect data issues, you need to first choose metrics that assess the data quality, similarly to how software teams monitor metrics that assess the health of their software's infrastructure.

In her book ([Data Quality Fundamentals](https://www.oreilly.com/library/view/data-quality-fundamentals/9781098112035/ch02.html#what_are_data_quality_metricsquestion_m)), Barr Moses suggests to start with the following questions:

- Is the data up-to-date?
- Is the data complete?
- Are fields within expected ranges?
- Is the null rate higher or lower than it should be?
- Has the schema changed?

She formulated these questions into 5 pillars for data observability, which aim to fully describe the state of the data.

#### [**Barr Moses 5 Pillars**](https://www.montecarlodata.com/blog-what-is-data-observability/)

1. **Distribution/ Internal Quality**: The quality pillar refers to the internal characteristics of the data, and checks metrics such as the percentage of NULL elements, percentage of unique elements, summary statistics and if your data is within the expected range. It helps you ensure that your data is trusted based on your data expectation.

2. **Freshness**: Data freshness refers to how “fresh” or “up-to-date” the data is within the final asset (table, BI report), i.e., when the data was last updated, and how frequently it is updated. Stale data results in wasted time and money.

3. **Volume**: Data volume refers to checking the amount of data ingested and looking for unexpected spikes or drops. Sudden drops in data volume can indicate issues like lost data or system outages, and sudden increases may indicate unexpected surges in usage.

4. **Lineage**: According to [Barr](https://towardsdatascience.com/introducing-the-five-pillars-of-data-observability-e73734b263d5), “When data breaks, the first question is always “where?” Data lineage helps you trace the data journey from its source to its destination, visualizing how data was transformed and where it was stored. This way, you can identify the source of errors or anomalies.

5. **Schema**: Data schema refers to monitoring changes in data structure or types. This pillar helps avoid the failure of the data pipeline.


##### Additional Resources

If you'd like to learn more about data observability, you can check the following additional resources.

- [Data quality fundamentals](https://learning.oreilly.com/library/view/data-quality-fundamentals/9781098112035/) , by Barr Moses, Lior Gavish, Molly Vorweck [book]
- [The rise of data downtime](https://towardsdatascience.com/the-rise-of-data-downtime-841650cedfd5), by Barr Moses [article]
- [What is data observability?](https://learning.oreilly.com/library/view/what-is-data/9781098120993/ch02.html#characteristics_of_a_data_incident) , by Andy Petrella [book]


## Monitoring Data Quality


By this point, I'm guessing you're feeling pretty well convinced that data observability and monitoring are important. But when it comes to actually doing it, *where do you start?* 

Like a lot of the other things we've been discussing so far in these courses, it all starts with what the **stakeholders** need. 

Practically speaking, there's a wide range of metrics or quality criteria you could decide to monitor when it comes to your data pipeline. For example, 
- you can monitor something like the total number of records adjusted in each batch, or over some time interval, or 
- whether the range of values in a particular column stays within some thresholds. 
- Or you could count the total number of null values in a table, or the difference in time between now and the timestamp of the most recent record in your data. 

There are a whole bunch of things you could decide to monitor. However, rather than setting up monitoring and alerts for every conceivable thing you could measure or observe about your data, **you'll want to identify the most important things and focus on those.** If instead you try to monitor every imaginable aspect of your data, you can end up creating confusion and alert fatigue, and the really important stuff will get lost in the noise. 

![[Screenshot 2025-12-17 at 16.11.18.png]]

When it comes to deciding what metrics or aspects of your data to monitor, the first question should be *what do stakeholders care about the most for this particular use case?* 

![[Screenshot 2025-12-17 at 16.13.56.png]]




I've been emphasizing that you should communicate with source system owners to be sure you understand what kinds of changes you'll need to anticipate or mitigate in the future. While there's no replacement for good communication, you should also take steps to build in checks or tests in your data monitoring to verify that things like the schema and types for the data you are ingesting stay consistent. If all goes well, these can just be nice sanity checks to ensure the data you're ingesting is still in the format you expect. But these checks can also help to identify problems early, before they are propagated further down your data pipelines. 

![[Screenshot 2025-12-17 at 16.15.39.png]]

Like a lot of things in data engineering, there are many different ways to monitor your data quality. You could do some things manually, or maybe write some custom code to perform a set of tests or trigger alerts. Those approaches might make sense in certain scenarios, like when you're first setting up or prototyping your pipelines. But nowadays there are a number of **tools** you can use to ensure data quality and also spare you from any *undifferentiated heavy lifting*.




## Resources

**Optional reading and reference material:**

- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [AWS CloudFormation (User Guide)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
- [Great Expectations Documentation](https://docs.greatexpectations.io/docs/core/introduction/)

- To read about the third pillar "Incident Response", check the following two articles:
    - [article 1](https://medium.com/@mikldd/incident-management-for-data-teams-5a14acd4e3d8)
    - [article 2](https://cloud.google.com/docs/security/incident-response)

- [Data Quality Fundamentals](https://learning.oreilly.com/library/view/data-quality-fundamentals/9781098112035/) , by Barr Moses, Lior Gavish, Molly Vorweck [book]
- [The rise of Data contracts](https://dataproducts.substack.com/p/the-rise-of-data-contracts), by Chad Sanderson [blog]
- [What is data observability?](https://learning.oreilly.com/library/view/what-is-data/9781098120993/ch02.html#characteristics_of_a_data_incident) , by Andy Petrella [book]
- [Driving data quality with data contracts](https://learning.oreilly.com/library/view/driving-data-quality/9781837635009/B19790_02.xhtml#_idParaDest-34), by Andrew Jones [book]
- [Data Contracts: Why Thought Leaders Opt for Ounces Over Pounds](https://www.gable.ai/blog/data-contracts), by Chad Sanderson