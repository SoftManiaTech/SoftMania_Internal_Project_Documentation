1. In the Splunk Instance, install Splunk for Jenkins app and create a custom app called SoftMania Jenkins Monitoring App. In the inputs, create HEC token
2. In the Jenkins Instance, install Splunk Plugin and configure it to send logs to Splunk Instance by using HEC token. Check if you're receiving the logs
3. Make email settings to send email from Splunk. Create a report that is sent to user daily
4. Create a dashboard that has 3rows with drill down option for each
5. Create an Alert when an job fails for 3times consecutively
6. App packaging and pushing it to Github
7. Create Jenkins pipeline to deploy the app in UAT Instance when the app is pushed into github and create a jenkins pipeline for Prod Environment
