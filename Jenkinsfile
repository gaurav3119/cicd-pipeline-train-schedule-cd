pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Deploy to staging') {
            when {
                branch 'master'
            }
    
            steps {
                withcredentials([usernamePassword(credentialsId: 'webserver_login', usernamevariable: 'USERNAME' , passwordvariable: 'USERPASS')]) {
                    sshpublisher(
                        failonError: true,
                        continueonError: false
                        publishers: [
                            sshpublisherDesc(
                                configName: 'staging',
                                sshcredentials: [
                                    username: "$USERNAME"
                                    encrytptedPassphrase: "$USERPASS"
                                    ],
                                transfers: [
                                    sshtransfer(
                                        sourceFiles: 'dist/train-schedule.zip',
                                        removeprefix: 'dist'
                                        remoteDirectory: '/tmp'
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                        )
                                    ]
                                )
                             ]
                        )
    
                    }
               }
               
           }
        
             stage('Deploy to production') {
            when {
                branch 'master'
            }
    
            steps {
                input 'Does the staging envirnment look good?'
                milestone(1)
                withcredentials([usernamePassword(credentialsId: 'webserver_login', usernamevariable: 'USERNAME' , passwordvariable: 'USERPASS')]) {
                    sshpublisher(
                        failonError: true,
                        continueonError: false
                        publishers: [
                            sshpublisherDesc(
                                configName: 'production',
                                sshcredentials: [
                                    username: "$USERNAME"
                                    encrytptedPassphrase: "$USERPASS"
                                    ],
                                transfers: [
                                    sshtransfer(
                                        sourceFiles: 'dist/train-schedule.zip',
                                        removeprefix: 'dist'
                                        remoteDirectory: '/tmp'
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                        )
                                    ]
                                )
                             ]
                        )
    
                    }
                    
                }
            
            }
         }
    }
