#!groovy

@Library('jenkinslib') _

def tools = new org.devops.tools()
def build = new org.devops.build()

String workspace = "/data/jenkins/workspace"
String buildEnv = "${env.buildEnv}"
String buildType = "${env.buildType}"
String buildShell = "${env.buildShell}"

String githubUrl = "${env.githubUrl}"

pipeline {
    agent { node {  label "master"   //指定运行节点的标签或者名称
                    //customWorkspace "${workspace}"   //指定运行工作目录（可选）
            }
    }

    options {
        timestamps()  //日志会有时间
        skipDefaultCheckout()  //删除隐式checkout scm语句
        disableConcurrentBuilds() //禁止并行
        timeout(time: 1, unit: 'HOURS')  //流水线超时设置1h
    }
   
    //parameters { string(name: 'DEPLOY_ENV', defaultValue: 'prod', description: '') }
    parameters {
        gitParameter branch: '', branchFilter: 'origin/(.*)', defaultValue: 'master', description: '选择你要的分支', name: 'BRANCH', quickFilterEnabled: 'true', selectedValue: 'NONE', sortMode: 'NONE', tagFilter: '*', type: 'PT_BRANCH', useRepository: '${githubUrl}'
    }	
	
    stages {
    //    stage('Example') {
    //        input {
    //            message "Should we continue?"
    //            ok "Yes, we should."
    //            submitter "zhouyc,chengdh"
    //            parameters {
    //                string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
    //            }
    //        }
    //        steps {
    //            echo "Hello, ${PERSON}, nice to meet you."
    //        }
    //    }
        //下载代码
        stage("GetCode"){ //阶段名称
	    steps {
	       timeout(time:20, unit:"MINUTES"){   //步骤超时时间
	           script{ //项目构建
                       tools.PrintMes("拉取代码","green")
                        checkout([$class: 'GitSCM', 
                                  branches: [[name: "${params.BRANCH}"]], 
                                  doGenerateSubmoduleConfigurations: false, 
                                  extensions: [],
                                  gitTool: 'Default', 
                                  submoduleCfg: [[credentialsId: '1']], 
                                  userRemoteConfigs: [[url: "${githubUrl}" , credentialsId: '1']]
                                ])
		       }
	          }
             }
        }
        stage("Build"){ //阶段名称
            steps{  //步骤
                timeout(time:20, unit:"MINUTES"){   //步骤超时时间
                    script{ //项目构建
                        println('项目构建')
                        build.Build(buildType,buildShell)
                        //mvnHome = tool "m2"
                        //println(mvnHome)
                        //sh "${mvnHome}/bin/mvn clean install -DskipTests"
                    }
                }
            }
        }
        stage("CodeScan"){ //阶段名称
            steps{  //步骤
                timeout(time:30, unit:"MINUTES"){   //步骤超时时间
                    script{ //代码扫描 
                        println('代码扫描')
			tools.PrintMes("this is my lib","green")
                    }
                }
            }
        }
    }

    //构建后操作
    post {
        always {
            script{
                println("always")
            }
        }

        success {
            script{
                currentBuild.description = "构建成功!" 
            }
        }

        failure {
            script{
                currentBuild.description = "构建失败!" 
            }
        }

        aborted {
            script{
                currentBuild.description = "构建取消!" 
            }
        }
    }
}
