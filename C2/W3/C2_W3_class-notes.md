
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
Within software engineering, one key aspect of automation is the practice of continuous integration and continuous delivery, or **CI/CD** for short. In the context of software, the CI/CD process involves setting up systems for automatic review and testing of new code. And then the automatic delivery or deployment into production of code that has been reviewed and tested. 
![[Pasted image 20251207190202.png]]

----

When it comes to DataOps, the practice of CI/CD can be applied directly to code and data within your data pipeline, much as it would be for a software product. And so whether that's code for applying a particular set of data transformations or populating a database or the data itself. You can maintain it just like you would any other piece of software application code. When it comes to the automation of actually running your data pipelines, as I talked about in the previous course, this is something you could do in a number of different ways. For example, have no automation and just run all the processes in your data pipeline manually. Or you could set up the stages of your pipeline to run according to a particular schedule. Or you could orchestrate your pipeline by defining it as a directed acyclic graph, or DAG, using an orchestration tool like Airflow. 

We'll look at DAGs and the automation of testing and deployment more closely next week when we get into orchestration. Now, one key underpinning of any CI/CD system, whether for a software product or a data product, is version control, where each new code version of the code is recorded. This makes it possible to easily revert back to a previous version if for some reason the current version isn't working as expected or other problems occur. You might already be familiar with version control in the context of your own code, maybe using a platform like GitHub. And so within DataOps, the concept of version control also applies to data. Just like you can track changes in your code and roll back to a previous version, with DataOps, you can track changes in the data moving through your pipelines. And be able to roll back to a previous version of the data if you run into problems. 

Another concept that DataOps borrows from DevOps in terms of automation is infrastructure as code. Whether you're building software applications or data pipelines with cloud platform resources, it's possible to maintain the design of your infrastructure as a code base, just like you would for any other application code. You can run that code to deploy your infrastructure, or modify the code to redefine your infrastructure, and then run it again to deploy the updated infrastructure. Now, by defining your infrastructure programmatically using code, you can then maintain version control over your entire infrastructure, just like you would for any other piece of code or for your data. And that way, if you need to roll back to a previous version of your infrastructure, it's as simple as rolling back to a previous version of your code. And so there are a number of ways in which DataOps automation practices will be part of your work as a data engineer. You can start to see how DataOps begins to overlap with the other undercurrents of the data engineering lifecycle. 

So for example, DataOps is closely related to software engineering in the sense that many DataOps practices are directly borrowed from DevOps. And when it comes to the DataOps practice of maintaining version control over your data, this ties directly with the undercurrent of data management. In the sense that it allows you to deliver control and enhance the value of data throughout its lifecycle. Next, I'd like to dig deeper into infrastructure as code in the context of DataOps automation. Then in the next lab, you'll get a chance to practice writing code to define your own infrastructure. So join me in the next video for a closer look at infrastructure as code.