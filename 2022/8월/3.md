# 오늘 배운점

---

```groovy
pipeline {
    //agent 지정
    agent any

    //트리거 설정 (예시는 3초 주기로 레포 확인)
    triggers {
        pollSCM('*/3 * * * *')
    }

    //환경변수 세팅 (각 리소스 활용하기 위한 계정정보)
    environment {
        AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId')
        AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey')
        AWS_DEFAULT_REGION = 'ap-northeast-2'
        HOME = "."
    }
    
    stages {
        //레포지토리를 내려받음
        stage('Prepare') {
            agent any

            steps {
                echo "ENV : ${ENV}"
                echo "Clonning Repository"

                git url: 'github repo',
                    branch: 'master',
                    credentialsId: 'jenkinsgit'
            }

            //step 끝나고 실행
            post {
                success {
                    echo 'Successfully Cloned Repository'
                }

                always {
                    echo "i tried.."
                }

                cleanup {
                    echo "after all other post condition"
                }
            }
        }

        stage('Only for production') {
            //조건 시에만 실행됨
            when {
                branch 'production'
                environment name: 'APP_ENV', value: 'prod'
                anyOf {
                    environment name: 'DEPLOY_TO', value: 'production'
                    environment name: 'DEPLOY_TO', vlaue: 'staging'
                }
            }
        }

        //aws s3에 파일 업로드
        stage('Deploy Frontend') {
            steps {
                echo 'Deploying Frontend'
                // 프론트 디렉토리의 정적 리소스를 s3에 업로드. 이전에 반드시 EC2 인스턴스 프로필을 등록해야함.
                dir ('./website') {
                    sh '''
                    aws s3 sync ./ s3://bucketname
                    '''
                }
            }
            // 이메일 전송하려면, 환경설정에서 gsmtp 및 계정정보 설정 필요.
            post {
                success {
                    echo 'Success'

                    //성공 시 이메일 전송
                    mail to: 'test@naver.com'
                        subject: "Deploy Frontend Success",
                        body: "Successfully deployed frontend!"
                }

                    //실패시
                    failure {
                        echo 'failed'

                        mail to: 'test@naver.com'
                            subject: "Failed pipeline",
                            body: "Failed deployed frontend!"
                    }
            }
        }
        
        stage('Lint Backend') {
            // Docker plugin , Docker pipeline 두개를 깔아야 사용 가능!
						// 도커 위에 node 이미지 설치 후 프로세스 진행
            agent {
                docker {
                    image 'node:latest'
                }
            }

            steps {
                dir ('./server') {
                    sh '''
                    npm install &&
                    npm run lint
                    '''
                }
            }
        }

        stage('Test Backend') {
            agent {
                docker {
                    image 'node:latest'
                }
            }
            setps {
                echo 'Test Backend'

                dir ('./server') {
                    sh '''
                    npm install
                    npm run test
                    '''
                }
            }
        }
        
        stage('Build Backend') {
            agent any
            
            steps {
                echo 'Build Backend'

                //도커 배포
                dir('./server') {
                    sh """
                    docker build . -t server --build-arg env=${PROD}
                    """
                }
            }

            post {
                //빌드가 실패해도 다음 파이프라인으로 넘어가기 때문에 이같은 작업이 필요함.
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }

        stage('Deploy Backend') {
            agent any
        
            steps {
                echo 'Build Backend'

                // 실행중인 컨테이너 종료 후 시작
                dir ('./server') {
                    sh '''
                    docker rm -f $(docker ps -aq)
                    docker run -p 80:80 -d server
                    '''
                }
            }

            post {
                success {
                    //mail to~~ (생략)
                }
            }
        }
    }

}
```

[강의 출처] [https://www.youtube.com/watch?v=HVqG59Wtc5c&list=PLe0AyUV1pCIQWy2l8J68rInMzDe_Tu68x&index=4](https://www.youtube.com/watch?v=HVqG59Wtc5c&list=PLe0AyUV1pCIQWy2l8J68rInMzDe_Tu68x&index=4)

# 오늘 느낀점

---

- 강의에서 제공된 실습을 따라 해보면서, 해당 파이프라인을 구성하는 각 스테이지별 역할과 문법적인 요소들의 세부설명을 들을 수 있어서 좋았다. 
또한, 개발코드 통합부터 빌드 및 배포 자동화까지 전체적인 파이프라인의 흐름을 파악해볼 수 있었고, 현재 개발하고 있는 프로젝트에서도 적절하게 적용해볼 수 있을 것 같다!

# 내일 목표

---

- api 명세