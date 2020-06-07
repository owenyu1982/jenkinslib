#!groovy
//引用共享库jenkinslib
@Library('jenkinslib') _

//定义一个变量，引用共享库
def tools = new org.devops.tools() //即src/org/devops/tools.groovy 这里.groovy不用写



//String workspace = "/opt/jenkins/workspace"
String workspace = "/var/lib/jenkins/"

hello() //直接使用vars/hello.groovy 这里的hello()就是整个hello.groovy里面的内容吧

//Pipeline
pipeline {
    agent { node {  label "master"   //指定运行节点的标签或者名称
                    customWorkspace "${workspace}"   //指定运行工作目录（可选）
            }
    }

/*    options {
        timestamps()  //日志会有时间
        skipDefaultCheckout()  //删除隐式checkout scm语句
        disableConcurrentBuilds() //禁止并行
        timeout(time: 1, unit: 'HOURS')  //流水线超时设置1h
    } */

    stages {
        //下载代码
        stage("GetCode"){ //阶段名称
            when { environment name: 'test', value: 'abcd' }  //when用法，当变量test值为abcd才运行下面步骤,写到stage下面
            steps{  //步骤
                timeout(time:5, unit:"MINUTES"){   //步骤超时时间
                    script{ //填写运行代码
                        println('获取代码')
                        tools.PrintMes("获取代码",'green')
                        println("${test}")
                        
                        input id: 'Test', message: '我们是否要继续？', ok: '是，继续吧！', parameters: [choice(choices: ['a', 'b'], description: '', name: 'test1')], submitter: 'lizeyang,admin'
                    }
                }
            }
        }

        stage("01"){
            failFast true
            parallel {
        
                //构建
                stage("Build"){
                    steps{
                        timeout(time:20, unit:"MINUTES"){
                            script{
                                println('应用打包')
                                tools.PrintMes("应用打包",'green')
                                mvnHome = tool "m2"
                                println(mvnHome)
                                
                                sh "${mvnHome}/bin/mvn --version"
                            }
                        }
                    }
                }
        
                //代码扫描
                stage("CodeScan"){
                    steps{
                        timeout(time:30, unit:"MINUTES"){
                            script{
                                print("代码扫描")
                                tools.PrintMes("代码扫描",'green')  //使用共享库里面的tools.groovy里面的PrintMes方法
                            }
                        }
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
                currentBuild.description = "\n 构建成功!" 
            }
        }

        failure {
            script{
                currentBuild.description = "\n 构建失败!" 
            }
        }

        aborted {
            script{
                currentBuild.description = "\n 构建取消!" 
            }
        }
    }
}
