@Library('jenkins-shared-library') _

def configMap = [
    project: "roboshop",
    component: "catalogue"
]

if (env.BRANCH_NAME.equalsIgnoreCase('main')){
    echo "we deal it later"
}
else {
    nodejsEKSpipeline(configMap)
}