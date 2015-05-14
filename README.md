# jenkins-groovy-examples
Some samples of groovy scripts used in jenkins automation


## Get API token for a user

```groovy
user = hudson.model.User.get('username')
prop = user.getProperty(jenkins.security.ApiTokenProperty.class)
println(prop.getApiToken())
```

## Enable matrix auth

Gives all authenticated users admin access

```groovy
import jenkins.model.*
def instance = Jenkins.getInstance()

import hudson.security.*
def realm = new HudsonPrivateSecurityRealm(false)
instance.setSecurityRealm(realm)

def strategy = new hudson.security.GlobalMatrixAuthorizationStrategy()
strategy.add(Jenkins.ADMINISTER, 'authenticated')
instance.setAuthorizationStrategy(strategy)

instance.save()
```

## Configure the slack plugin as if the form was submitted

```groovy
import jenkins.model.*
def instance = Jenkins.getInstance()

// configure slack
def slack = Jenkins.instance.getExtensionList(
  jenkins.plugins.slack.SlackNotifier.DescriptorImpl.class
)[0]
def params = [
  slackTeamDomain: "domain",
  slackToken: "token",
  slackRoom: "",
  slackBuildServerUrl: "$JENKINS_URL",
  slackSendAs: ""
]
def req = [
  getParameter: { name -> params[name] }
] as org.kohsuke.stapler.StaplerRequest
slack.configure(req, null)
slack.save()
```


## Configure Maven options

```groovy
import jenkins.model.*
def instance = Jenkins.getInstance()

// Tell jenkins where maven is
def mavenTask = Jenkins.instance.getExtensionList(
  hudson.tasks.Maven.DescriptorImpl.class
)[0]
mavenTask.setInstallations(
  new hudson.tasks.Maven.MavenInstallation(
    "Maven", "$M2_HOME", []
  )
)
mavenTask.save()

// Configure global maven options
def maven = Jenkins.instance.getExtensionList(
  hudson.maven.MavenModuleSet.DescriptorImpl.class
)[0]
maven.setGlobalMavenOpts("-Dmaven.test.failure.ignore=false")
maven.save()
```


## No executors on master

```groovy
import jenkins.model.*
def instance = Jenkins.getInstance()

// No jobs on master
instance.setNumExecutors(0)
```


## Global email settings

```groovy
import jenkins.model.*
def instance = Jenkins.getInstance()

// set email
def location_config = JenkinsLocationConfiguration.get()
location_config.setAdminAddress("jenkins@azsb.skybet.net")
```
