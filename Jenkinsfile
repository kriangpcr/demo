pipeline {
    agent any

    environment {
        // ตั้งค่า AWS Credentials สำหรับการทำงานกับ AWS
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        SLACK_CHANNEL = '#your-slack-channel' // ช่อง Slack ที่ใช้แจ้งเตือน
    }

    stages {
        // ขั้นตอนที่ 1: ดึงโค้ดจาก Git
        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repository.git' // ระบุ URL ของ Git repository
            }
        }

        // ขั้นตอนที่ 2: รัน Automated Test (JUnit)
        stage('Run Automated Tests') {
            steps {
                script {
                    try {
                        // รันการทดสอบโดยใช้ Maven (หรือใช้คำสั่งที่เหมาะสมตามโปรเจค)
                        sh 'mvn test' 
                    } catch (Exception e) {
                        // ถ้าการทดสอบล้มเหลว จะทำให้ build เป็น FAILURE
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        // ขั้นตอนที่ 3: Build Application
        stage('Build Application') {
            steps {
                // สร้างโปรเจค เช่น ใช้ Maven หรือ Gradle
                sh 'mvn clean install'  // สำหรับโปรเจคที่ใช้ Maven
            }
        }

        // ขั้นตอนที่ 4: Deploy ไปยัง AWS
        stage('Deploy to AWS') {
            steps {
                script {
                    // ตัวอย่างการ Deploy ไปยัง AWS S3 (สามารถปรับแต่งให้เหมาะสมกับการ Deploy แบบอื่น)
                    sh 'aws s3 cp target/my-app.jar s3://your-bucket-name/my-app.jar'
                    // หรือใช้คำสั่ง AWS CLI สำหรับการ deploy ไปยัง EC2, ECS, หรือ Lambda
                }
            }
        }

        // ขั้นตอนที่ 5: ส่งการแจ้งเตือน
        stage('Notify') {
            steps {
                script {
                    def message = "Build Status: ${currentBuild.result}"
                    
                    // แจ้งเตือนผ่าน Slack
                    slackSend channel: "${SLACK_CHANNEL}", message: message, color: currentBuild.result == 'SUCCESS' ? 'good' : 'danger'
                    
                    // แจ้งเตือนผ่าน Email
                    mail to: 'your-email@example.com', subject: "Jenkins Build Status", body: message
                }
            }
        }
    }

    // ข้อความหลังจาก pipeline ทำงานเสร็จ
    post {
        always {
            echo "Pipeline Completed"
        }
    }
}
