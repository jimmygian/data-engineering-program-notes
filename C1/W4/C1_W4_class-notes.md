Stakeholder Management and Gathering Requirements

**Week 1 recap**
In the first week of this course, we took a brief look at requirements gathering as it relates to your work as a data engineer. 
- We looked at how a conversation might go between you and a data scientist at your company about their needs for the analytics and machine learning projects they're working on. 
- You saw how a conversation like this could quickly reveal the need for you to talk to other stakeholders. In this case, the marketing team and software engineers. 
- We also took a look at a framework for thinking like a data engineer, where you first identify business goals and stakeholder needs, then define requirements for your system based on those needs, then choose tools and technologies that meet your requirements, and then finally build and deploy your system.

![[Screenshot 2025-10-17 at 19.27.24.png]]
Now there is a lot packed into this little framework, and even when I add in the details of what occurs during each step, things like asking what action your stakeholders will take in the first step, translating stakeholder needs into system requirements in the second step, prototyping and testing in the third, or evolving your system in the last step. You can see that there are still many things that are either left unsaid or unwritten here. 

![[Screenshot 2025-10-17 at 19.28.04.png]]

**Weeks 2 & 3 recap**
Weeks 2 and 3 of this course were designed to fill in many of those details so that you can form a more complete mental model of the stream work, the field of data engineering, and what your role as a data engineer will look and feel like in practice. 

In Week 2, you looked at the **stages** and **undercurrents** of the data engineering life cycle. There, we focused not only on the **components** that make up your data systems, but also on the people involved, including **source system owners** and **downstream end users** that you'll work with. 

In Week 3, we took a deep dive into the **principles of good data architecture**, where you saw that things like **planning for failure** and **choosing common components** or elements of your system design that you should be thinking about right from the start. 

![[Screenshot 2025-10-17 at 19.29.07.png]]

Now you have all the pieces of that mental model that will be critical in your work as a data engineer. This week, we're going to pull all those pieces together in the context of a **practical on-the-job scenario**. 
- We'll start with **requirements gathering**, 
- then choosing **tools** and **technologies**, 
- and finally, **implementation** of your system. 

![[Screenshot 2025-10-17 at 19.28.41.png]]

At the end of this week, you'll translate the requirements into an architecture design and then build your system on the AWS Cloud. Join me in the next video to get started.


## Requirements Gathering

![[Screenshot 2025-10-17 at 19.27.24.png]]

First step of the Data Engineering framework is to "identify business goals & stakeholders you will serve".

At this point, I'd like you to start thinking about **requirements as a hierarchy of needs**.

![[Screenshot 2025-10-17 at 19.38.33.png]]

- At the top of this hierarchy, you have the **Business Goals and Objectives**. These are the things that describe what success looks like for the business as a whole. 
	- These might be objectives around **growing revenue** or **market share** or **user base** or something else that represents an **optimal outcome for the business**. 
- The next thing down on the hierarchy is **stakeholder needs**. In this case, you can think of these stakeholders as the **individual employees** of a business, and each of them plays a role in delivering on the high level business objectives. 
	- For the purposes of these courses, you can assume that they also have a need for robust data systems. 
- Below stakeholder needs in this hierarchy, you have **system requirements**. In principle, this could be any system, but here we're talking about the data systems you will build and maintain. This is a set of requirements that your data systems must satisfy in order to serve stakeholder needs. 
	System requirements are separated into **functional** and **non-functional**. 
	- **Functional requirements** are those that can be expressed in terms of a **specific functionality**, namely, what the system will be able to do to meet the needs of stakeholders. 
	- **Non-functional requirements**, on the other hand, are those system requirements you could think of as **characteristics** or **attributes** of the system that allow it to function properly. These could be characteristics or attributes related to things like latency, scalability, reliability, cost, or security. For example, a non-functional requirement in terms of scalability for a streaming pipeline ingesting data from an e-commerce platform might be, the system must be able to scale up to ingest data from 10,000 users shopping at the same time.

The main takeaway here is that like we've been talking about all along, your work in building data systems is **directly connected t**o the work of others in your organization and to the overall goals of the business. This is why it's so important to understand the needs of your stakeholders and the objectives of the business for the work that you do. That brings us back to requirements gathering. 

When gathering requirements, ideally, you'd like to start at the top. That means talking to **leadership** about the goals of the company. If you work at a small company, you might even be able to have a conversation with the CEO. I would definitely recommend that. If you're at a bigger organization, you just need to go up as far as you can up the chain of command to get oriented with the goals of the business. 

There are four key elements in the **Requirements Gathering Process**
1. Learn what **existing data systems** or solutions are in place
2. Learn what **pain points** or **problems** there are with the existing solutions 
3. Learn what actions stakeholders plan to take based on the data you serve them
4. Identify any other stakeholders you'll need to talk to if you're still missing information.


TIPS:
-  Learn what **actions** the stakeholder plans to take with the data 
- Learn about the existing system solution, what it gets right, what wrong.
- *Always repeat a rephrased version of what the stakeholder said to make them feel understood. After it, ask a follow up if you have one to expand on a topic.*


### Documenting Requirements

The way I like to document requirements is using the hierarchy that we looked at in the previous video. This way, it's easy to visualize the connections from system requirements here at the bottom, all the way up to the high level business goals.

Example:

----
**Business Goals**:
Continue on the growth trajectory: Focus on customer retention and loyalty, expand to new markets and new product offerings.

**Stakeholder Needs**: 
The company needs an Analytics Dashboard, and a Recommender System. Both of these will have their own set of needs, as well as functional and non-functional system requirements.

- Analytics Dashboard Needs:
	- Marketing needs to know about demand spikes. Looks like hourly dashboard updates would suit best.
- Recommender System:
	- Marketing needs a system that recommends products based on browsing or purchase history and current cart contents.

System Requirements
- Functional
	- Analytics Dashboard:
		- The data system needs to serve transofmed data no more than 1h old.
	- Recommender System:
		- The system needs to: 
			- Provide training data for recommender model
			- Ingest, transform, and serve user data to recommender
			- Return model outputs to back to the sales platform.
- Non-functional
	- Analytics Dashboard:
		- ...
	- Recommender System:
		- ...
----

We'll see below in more detail how to gather non-functional reuirements.


### Communicating with source system owners

Disruptions to the data flow or changes in the data format are very common, and  that's something you should be anticipating as a data engineer when you ingest data from source systems. 

The best thing you can do to mitigate these issues is to open lines of communication with the source system owners and discuss how to best anticipate and deal with disruptions or changes in the data when they occur.

When speaking to Source System owners, you can still use the first two of the four key elements of Requirements Gathering.

![[Screenshot 2025-11-04 at 21.44.40.png]]

STEPS:
- Learn about existing system solution.
- Discuss the pain points with the existing systems.
- Develop good communication with upstream users - bond.



### Documenting Non-Functional Requirements

Non-functional requirements can be a little trickier than functional requirements in the sense that they won't, typically, be things that your stakeholders *explicitly* ask for. Rather, these are the characteristics or attributes that your system **must** have in order to get the job done **well**. 

Let's expand on our previous example:

----
**Business Goals**:
Continue on the growth trajectory: Focus on customer retention and loyalty, expand to new markets and new product offerings.

**Stakeholder Needs**: 
The company needs an Analytics Dashboard, and a Recommender System. Both of these will have their own set of needs, as well as functional and non-functional system requirements.

- Analytics Dashboard Needs:
	- Marketing needs to know about demand spikes. Looks like hourly dashboard updates would suit best.
- Recommender System:
	- Marketing needs a system that recommends products based on browsing or purchase history and current cart contents.

System Requirements
- Functional
	- Analytics Dashboard:
		- The data system needs to serve transofmed data no more than 1h old.
	- Recommender System:
		- The system needs to: 
			- Provide training data for recommender model
			- Ingest, transform, and serve user data to recommender
			- Return model outputs to back to the sales platform.
- Non-functional
	- Analytics Dashboard:
		- ...
	- Recommender System:
		- ...
----


**Analytics Dashboard**

Functional Requirements: 
- The data system needs to serve transofmed data no more than 1h old.
Non-Functional Requirements:
- **Scalability and Latency**: System will be able to scale up to ingetst, transofrm, and serve the data volume expected with the **maximum level** of user activity, **while staying** within the latency requirements.
- **Reliability:** System will perform data quality checks to ensure data is confirmant
- Maintainability: The ingestion and transformation stages must be easily adaptable to accommodate any changes in the data schema.


**Recommender System**

Functional Requirements: 
- System needs to provide training data for recommender model
- It also needs to Ingest, transform, and serve user data to recommender
- It then needs to return model outputs to back to the sales platform.

Non-Functional Requirements:
- **Latency**: System must have a latency of less than 1 second from ingestion of user data to serving of recommendation data.

![[Screenshot 2025-11-04 at 22.08.29.png]]

- **Scalability** (same as above)
- **Reliability**:
	- System msust return a set of recommendations within one second.
	- If recommender pipeline fails it should default to serving a selection of the most popular products.

The key thing to take away from these requirements, gathering examples is that it's important to understand the systems you build not just in terms of how they will function, but how that functionality will serve stakeholders and the ultimate goals of the business. 


### Summary

The main takeaways from this lesson include the following.
- Before you set out to build or modify any data systems, you need to **identify the stakeholders** you will serve and **understand their needs** in the context of the broader goals of the business. 
  
  The way to do this is by talking to lots of people in your organization, and maybe everyone from company leadership right down to those you work alongside, like data scientists, and software engineers. 

- It's important to ask **open-ended questions** in these conversations to gain an understanding of current systems, potential problems with the systems. And what action the stakeholders plan to take with the data. 
  
- It's also important to document all of your findings. Having proper documentation of the requirements you've gathered will allow you to confirm with stakeholders, whether the system you're planning to build will serve their needs, as well as the needs of the business. 


So far, we've been focused on figuring out how to build data systems that optimize for serving stakeholder needs. Something we haven't talked about yet this week is about **how to evaluate trade offs** in requirements gathering. For example, your stakeholders might want you to build a data system **as quickly as possible**, or when it comes to costs, you might be working within a **limited budget** for the systems you build. In reality, of course, timeline and budget constraints will be part of any project you work on. Your conversations with stakeholders will need to include a discussion on **what's the most important**. The features of the system or the timeline to deployment or the cost?

There's a concept that emerged from project management, known as the **iron triangle**, where you have three aspects of any project that are fundamentally in tension with one another. 

These are 
1. **the timeline** of the project, 
2. **the scope** of the work, 
3. and **the cost**. 

![[Screenshot 2025-11-04 at 22.16.11.png]]

What I mean by intention with one another is that you can think of each of these things **pulling in a different direction.** 

For example, if you increase the scope of work for a project, then either the timeline or the cost or both **must** increase. Likewise, if you want to complete the project in a shorter timeline, then that could require increasing the cost or reducing the scope or both. There's even an old saying that developed around this concept, which is ***"good, fast or cheap"***. 

In reality, every company wants projects done well, and they typically want them done as quickly as is reasonably possible and within certain budget constraints. So,*what do you do?* 

![[Screenshot 2025-11-04 at 22.20.55.png]]

The way to break the iron triangle is through the application of **principles and processes** like those we've been talking about throughout this course, things like building loosely coupled systems, optimizing for two way door decisions and deeply understanding the needs of stakeholders. By applying these principles and processes, you'll be better able to build and maintain high quality data systems on a predictable timeline and budget. 


