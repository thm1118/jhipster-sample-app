#!/usr/bin/env groovy

node {
    stage('签出代码') {
        checkout scm
    }

    stage('检查java') {
        sh "java -version"
    }

    stage('清理历史构建') {
        sh "chmod +x mvnw"
        sh "./mvnw clean"
    }

    stage('安装工具') {
        sh "./mvnw com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v10.15.0 -DnpmVersion=6.4.1"
    }

    stage('安装前端工具链') {
        sh "./mvnw com.github.eirslett:frontend-maven-plugin:npm"
    }

    stage('后端测试') {
        try {
            sh "./mvnw test"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/surefire-reports/TEST-*.xml'
        }
    }

    stage('前端测试') {
        try {
            sh "./mvnw com.github.eirslett:frontend-maven-plugin:npm -Dfrontend.npm.arguments='run test'"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/test-results/TESTS-*.xml'
        }
    }

    stage('打包') {
//        sh "./mvnw verify deploy -Pprod -DskipTests"
        sh "./mvnw verify package -DskipTests"
//        archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
    }
    stage('sonar扫描') {
        withSonarQubeEnv('sonar') {
            sh "./mvnw sonar:sonar"
        }
    }
}
