

Continuous Integration and Continuous Delivery is all about automating and streamlining the steps between committing code changes and deploying the code to production.

![](../Attachments/Pasted%20image%2020220831012321.png)

The pipeline is triggered by a code commit. There are no codified practices, but are generally standardized on the following. 

Committing code starts CI Pipeline:
1. Pull source code from [Git](Git.md) repository
2. Install dependencies
3. Run Automated Tests
4. Build [Containers](Containers.md) Images/Compile Code

This then leads to the CD Pipeline:
1. Grab Image/Code
2. Code Review 
3. Deployment to Production