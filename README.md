# Jenkinsfile Runner for Fn

<img src="images/jenkins-fn.png" width="150">

An [Fn Project](http://fnproject.io) function to run Jenkins pipelines. It will process a GitHub webhook, git clone the repository and execute the Jenkinsfile in that git repository.

This function allows `Jenkinsfile` execution without needing a persistent Jenkins master running in the same way as [Jenkins X Serverless](https://medium.com/@jdrawlings/serverless-jenkins-with-jenkins-x-9134cbfe6870), but using the Fn Project platform (and supported providers like [Oracle Functions](https://blogs.oracle.com/cloud-infrastructure/announcing-oracle-functions)) instead of Kubernetes.


# Limitations

Current implementation limitations:

* `checkout scm` does not work, change it to `sh 'git clone https://github.com/carlossg/jenkinsfile-runner-fn-example.git'`

# Example

See the [jenkinsfile-runner-fn-example] project for an example that is tested and works.(https://github.com/carlossg/jenkinsfile-runner-fn-example)

# Extending

You can add your plugins to `plugins.txt`.
You could also add the Configuration as Code plugin for configuration.

Other tools can be added to the `Dockerfile`.

# Installation

[Install Fn](http://fnproject.io/tutorials/install/)

## Building

Build the function

    mvn clean package

## Publishing

Create and deploy the function locally

    fn create app jenkinsfile-runner
    fn --verbose deploy --app jenkinsfile-runner --local

## Logging

Start a syslog server to see the logs

    docker run -d --rm -it -p 5140:514 --name syslog-ng balabit/syslog-ng:latest
    docker exec -ti syslog-ng tail -f /var/log/messages-kv.log

Update the function to send logs to the syslog server

    fn update app jenkinsfile-runner --syslog-url tcp://<YOUR_IP>:5140

## Testing

Invoke the function

    cat src/test/resources/github.json | fn invoke jenkinsfile-runner jenkinsfile-runner

## GitHub events

Add a GitHub `json` webhook to your git repo pointing to the function url.