#!groovy
// Jenkinsfile (Pipeline Script)
node {
    env.REPO_URL = "https://github.com/Ziver/Netty-Transport-jSerialComm"
    
    buildStep('Checkout') {
        git url: env.REPO_URL
    }

    withMaven(jdk: "jdk8", maven: "m3.5", mavenLocalRepo: ".repository") {

        buildStep('Build') {
            sh 'mvn clean compile'
        }

        buildStep('Test') {
            sh 'mvn test'
        }

        buildStep('Package') {
            sh 'mvn -DskipStatic -DskipTests install'
        }
    }
    
    setBuildStatus("CI", "SUCCESS");
}

void buildStep(String message, Closure closure) {
    stage(message);
    try {
        setBuildStatus(message, "PENDING");
        closure();
    } catch (Exception e) {
        setBuildStatus(message, "FAILURE");
    }
}

void setBuildStatus(String message, String state) {
    step([
        $class: "GitHubCommitStatusSetter",
        reposSource: [$class: "ManuallyEnteredRepositorySource", url: env.REPO_URL],
        contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
        errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
        statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
    ]);
}
