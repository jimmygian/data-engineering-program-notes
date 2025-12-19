Video: https://www.youtube.com/watch?v=Yxl7e88cTAQ
Practice Lab: https://www.coursera.org/learn/source-systems-data-ingestion-and-pipelines/ungradedLab/j5xfR/practice-lab-2-implementing-monitoring-with-amazon-cloudwatch/lab


# Implementing Monitoring with Amazon CloudWatch

When you're working with resources on AWS, a lot of them automatically start posting metrics to CloudWatch, without you needing to do any sort of setup. Most of the time, these are system level metrics like: 
- CPU utilization
- disk IO
- network traffic
- memory usage.

These metrics can provide a general understanding of how your resources are performing. Identifying issues before your end users do is important, and having robust monitoring in place can help you do that. 

While many AWS services automatically send metrics to CloudWatch, there are cases where you might want to send custom metrics. **Custom metrics** allow you to monitor specific aspects of your application that are not covered by default system metrics. For instance, you might want to track application-specific data such as: 
- the number of transactions processed, 
- the response time of an API endpoint, or 
- the number of active users. 

You can use **CloudWatch dashboards** to *visualize* and *monitor* the metrics that you deem as the most important. 

You can also **aggregate** metrics from multiple components of your system into one unified view. This view will help you identify and diagnose issues as they arise, but dashboards can show you data over time as well, so you can more easily identify patterns and anomalies. 

**Alarms**
Now, you likely won't be sitting around watching your dashboards all of the time. Instead, you want a way to be made aware of problems as the metrics begin to reflect any issues. You can create **CloudWatch alarms** for specific metrics to do this, and you can **define thresholds** for these metrics so that when the threshold is breached, you can be alerted, or other actions can automatically be taken. Before you determine what reasonable thresholds are to set for your metrics, you need to **establish a baseline**. To do this, you would want to measure performance of your system under different loads and conditions at various times and determine **what is normal**. 

**Retention**
Cloud watch is configured to *retain metric data for up to 15 months*. You can collect metrics for a while before determining what the baseline is for your system. In general, acceptable values for metrics depend on what your application is doing relative to your baseline. 


