#!groovy
@Library('jenkinslib') _  //引用在github上创建的名称为jenkinslib的库 符号“_” 表示引用
def tools = new org.devops.tools()   //定义一个变量tools去引用库里面的内容（此处也就是tools.groovy包）

String workspace = "/opt/jenkins/workspace"
pipeline{
	agent{
		node{
			label "jdxu"    //指定运行节点的标签或者名称（云里面template里面设置的标签：k8s的某个node节点也需要设置同样的label：kubectl label node node1 label=jdxu）
			customWorkspace "${workspace}" //指定运行工作的目录（可选）
		}
	}
	options{
		timestamps() //日志会有时间显示   需要安装Timestamper插件（推荐安装的就有了）
		skipDefaultCheckout()  //删除隐式checkout scm语句,没必要让它去下载代码库
		disableConcurrentBuilds() //禁止并行
		timeout(time: 1, unit: 'HOURS')  //流水线超时时间设置（此处设置为1小时）
	}
	stages{
		//下载代码
		stage("GetCode"){  //阶段名称
			steps{  //步骤
				timeout(time:5,unit:"MINUTES"){ //步骤超时时间
					script{
						println('获取代码')
					}
				}
			}
		}
		//构建
		stage("Build"){
			steps{
				timeout(time:20,unit:"MINUTES"){
					script{
						println('应用打包')
					}
				}
			}
		}
		//代码扫描
		stage('CodeScan'){
			steps{
				timeout(time:30,unit:"MINUTES"){
					script{
						println('代码扫描')
						tools.PrintMes("this is my library!") //比如说在这里引用
					}
				}
			}
		}
		stage('发布到测试'){
			when { environment name: 'test', value: 'abcd' }
			steps{
				timeout(time:10,unit:"MINUTES"){
					script {
						println("${test}")
						input id: "Test", message: '我们是否要继续?', ok: '是，继续吧', parameters: {choice(choices:'main\ndev\nbranch01',description:'请选择分支',name:'BRANCH')}
					}
				}
			}
		}
	}
	//构建后的操作
	post {
		always {
			script {
				println('always')
			}
		}

		success {
			script {
				currentBuild.description = "\n 构建成功！" //做一个状态变更
			}
		}

		failure {
			script {
				currentBild.description = "\n 构建失败！" //可以给开发发一个邮件通知等
			}
		}

		aborted {
			script {
				currentBuild.description = "\n 构建取消！"
			}
		}
	}

}
