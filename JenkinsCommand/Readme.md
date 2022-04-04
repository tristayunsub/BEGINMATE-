jenkins command
===


https://waspro.tistory.com/554

1. Jenkins 파이프라인 정의

Jenkins 파이프라인은 여러 방식으로 구현이 가능합니다.

- Jenkins Webadmin : 일반적인 방식으로 Jenkins 파이프라인을 생성하여 Shell Script를 직접 생성하여 빌드하는 방식

- Git SCM : Git Repository에 JenkinsFile을 작성하여 빌드하는 방식

- Blue Ocean : 파이프라인을 시각화하여 손쉽게 구성하여 빌드하는 방식

2. Jenkins 파이프라인 Script 문법 정의


2. Jenkins 파이프라인 Script 문법 정의

1) Pipeline

```
pipeline {
    /* insert Declarative Pipeline here */
}
```
- 파이프라인을 정의하기 위해서는 반드시 pipeline을 포함해야 합니다.

- pipeline은 최상위 레벨이 되어야하며, { }으로 정의해야 합니다.

- pipeline은 Section, Directives, Steps 또는 assignment 문으로만 구성되야 합니다.



2) Section

a. agent : agent를 추가할 경우 Jenkins는 해당 agent를 설정합니다.

- any, none, label, node, docker, dockerfile, kubernetes를 파라미터로 포함할 수 있습니다.

- agent는 pipeline의 최상위에 포함되어야 하며, agent가 none으로 작성되었을 경우 stage에 포함되어야 합니다.

ex) docker
```
agent {
    docker {
        image 'myregistry.com/node'
        label 'my-defined-label'
        registryUrl 'https://myregistry.com/'
        registryCredentialsId 'myPredefinedCredentialsInJenkins'
    }
}
```

ex) dockerfile
```

agent {
    dockerfile {
        filename 'Dockerfile.build'
        dir 'build'
        label 'my-defined-label'
        registryUrl 'https://myregistry.com/'
        registryCredentialsId 'myPredefinedCredentialsInJenkins'
    }
}

```

ex) agent가 적용 된 JenkinsFile Sample

```
pipeline {
    agent none 
    stages {
        stage('Example Build') {
            agent { docker 'maven:3-alpine' }
            steps {
                echo 'Hello, Maven'
                sh 'mvn --version'
            }
        }
        stage('Example Test') {
            agent { docker 'openjdk:8-jre' }
            steps {
                echo 'Hello, JDK'
                sh 'java -version'
            }
        }
    }
}

```
- agent none이 pipeline의 최상위에 정의되어 있을 경우 stage는 각각 agent를 포함하여야 합니다.

- 이 JenkinsFile Sample을 통해 새로 작성된 컨테이너의 pipeline을 정의는데 유용히 사용할 수 있습니다.



post : 특정 stage나 pipeline이 시작되기 이전 또는 이후에 실행 될 confition block을 정의합니다.

- always, changed, fixed, regression, aborted, failure, success, unstable, unsuccessful,와 cleanup 등의 상태를 정의할 수 있습니다.

- 일반적으로 post는 pipeline의 끝에 배치합니다.

 
```
pipeline {
    agent any
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
        }
    }
}
```

c. stages : 하나 이상의 stage에 대한 모음을 정의합니다.

- pipeline 블록 안에서 한번만 실행 될 수 있습니다.

- stages 내부에서는 여러 stage를 포함할 수 있습니다.

d. steps : stage 내부에서 실행되는 단계를 정의합니다.

ex) stages와 steps가 적용 된 JenkinsFile Sample

```
pipeline {
    agent any
    stages { 
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
}

```


3) Directives

a. environment : key-value 형태로 파이프라인 내부에서 사용할 환경 변수로 선언할 수 있습니다.

ex) environment가 적용 된 JenkinsFile Sample
```
pipeline {
    agent any
    environment { 
        CC = 'clang'
    }
    stages {
        stage('Example') {
            environment { 
                AN_ACCESS_KEY = credentials('my-prefined-secret-text') 
            }
            steps {
                sh 'printenv'
            }
        }
    }
}
```


c. parameters : 사용자가 제공해야할 변수에 대해 선언할 수 있습니다.

- string, text, booleanParam, choice, password 등을 정의할 수 있습니다.

- parameters는 pipeline에서 한번만 정의할 수 있습니다.

ex) parameters가 적용 된 JenkinsFile Sample

```
pipeline {
    agent any
    parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
 
        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
 
        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')
 
        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
 
        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }
    stages {
        stage('Example') {
            steps {
                echo "Hello ${params.PERSON}"
 
                echo "Biography: ${params.BIOGRAPHY}"
 
                echo "Toggle: ${params.TOGGLE}"
 
                echo "Choice: ${params.CHOICE}"
 
                echo "Password: ${params.PASSWORD}"
            }
        }
    }
}

```

triggers : 파이프라인을 다시 트리거해야 하는 자동화된 방법을 정의합니다.

- cron, pollSCM, upstream 등 여러 방식으로 트리거를 구성할 수 있습니다.

ex) triggers가 적용 된 JenkinsFile Sample

```
pipeline {
    agent any
    triggers {
        cron('H */4 * * 1-5')
    }
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
}

```

f. input : input 지시문을 stage에 사용하면 input 단계를 사용하여 입력하라는 메시지를 표시할 수 있습니다.
- 사용 가능한 옵션으로는 message, id, ok, submitter, submitterParameter, parameters가 있습니다.
ex) input이 적용 된 JenkinsFile Sample

```
pipeline {
    agent any
    stages {
        stage('Example') {
            input {
                message "Should we continue?"
                ok "Yes, we should."
                submitter "alice,bob"
                parameters {
                    string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
                }
            }
            steps {
                echo "Hello, ${PERSON}, nice to meet you."
            }
        }
    }
}
```
