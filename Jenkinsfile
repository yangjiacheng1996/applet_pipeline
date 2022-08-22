pipeline{
    agent{ label "devops1" }
    environment{
        manner_jenkins = credentials("mannerjenkins")
        ecs_root = credentials("applet-server-root")
        repo_namespace = credentials("repo-applet-prod")
    }
    stages{
        stage("check agent Unix style"){
            steps{
                script{
                    if (!isUnix()){
                        error("this agent system is not Unix or Linux, not allowed to run pipeline,program down.")
                    }
                }
            }
        }
        stage("check_vars"){
            steps{
                //sh 'printenv'
                echo "services_image_version: \n${env.services_image_version}"
                echo "hosts_and_services: \n${env.hosts_and_services}"
                echo "versions_path: \n${versions_path}"
                //echo "aliyun shanghai docker repo namespace: \n${env.docker_repo_namespace}"
                echo "ecs_root pw: \n${env.ecs_root_PSW}"
                echo "repo_namespace pw: \n${env.repo_namespace_credential_PSW}"
                echo "gitee mannerjenkins pw: \n${env.manner_jenkins_PSW}"
            }
        }

        // stage("check Artifacts"){
        //     steps{
        //         dir("./check_env"){
                    
        //         }
        //     }
        // }
        stage("trigger build image job"){
            steps{
                echo "start jenkins job to build images and push to repository.--------------------->"
                script{
                    // 这是 Groovy 代码，build是一个step函数 ，后面的都是传参，Groovy函数调用时不需要括号。
                    jobB = build job: 'build_docker_images', propagate: false, wait: true, parameters: [
	                       text(name: "__services_image_version", value: "${env.services_image_version}"),
                           text(name: "__hosts_and_services", value: "${env.hosts_and_services}"),
                           string(name: "__versions_path", value: "${env.versions_path}"),
                           string(name: "__docker_repo_namespace",value:"${env.docker_repo_namespace}"),
	                   ]
                    println jobB.getResult()
                }
            }
        }
    }
    post{
        success{
            echo "构建成功，清空Jenkins工作空间Workspace。"
            cleanWs()
        }
    }
}