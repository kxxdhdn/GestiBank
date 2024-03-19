pipeline {

	environment {
	    // Credential de DockerHub sauvegardé dans Jenkins
	    DOCKERHUB_CREDENTIALS=credentials('docker')
        DOCKER_REPO = "kxxdhdn"
        DOCKER_IMAGE_BACK = "gestibank-backend"
        DOCKER_IMAGE_FRONT = "gestibank-frontend"
        VERSION_BACK = "1.0.0" // initial Back
        VERSION_FRONT = "1.0.0" // initial Front
    }

    agent any 

    stages {
        stage('Début du pipeline ...') { 
            steps {
                echo 'Chargement' 
            }
        }
        
        stage('Obtenir la version (Backend) la plus récente ...') {
            steps {
                script {
                    try {
                        // tags existants
                        def tags = sh(script: "docker pull ${DOCKER_REPO}/${DOCKER_IMAGE_BACK}:${VERSION_BACK} && docker image ls --format='{{.Tag}}' ${DOCKER_REPO}/${DOCKER_IMAGE_BACK}", returnStdout: true).trim()
                        echo "tags : ${tags}"
                        // tag list
                        //def tagList = tags.tokenize("\n")
                        //echo "tagList : ${tagList}"
                        // tag le plus récent
                        def latestTag = tags.tokenize("\n")[-1]
                        echo "latestTag : ${latestTag}"
                        // incrément version
                        if (latestTag != "") {
                            VERSION_BACK = incrementVersion(latestTag)
                            echo "Version (Backend) ${VERSION_BACK}"
                        }
                    } catch (Exception e) {
                        echo "Version (Backend) initiale."
                    }
                }
            }
        }

        stage('Création image Docker (Backend) ...') { 
            steps {
                sh "docker build -t ${DOCKER_REPO}/${DOCKER_IMAGE_BACK}:${VERSION_BACK} ."
            }
        }
        
        stage('Tag et push image (Backend) ...') {
            steps {
                echo "Push image (Backend) version ${VERSION_BACK} ..."

                sh "docker tag ${DOCKER_REPO}/${DOCKER_IMAGE_BACK}:${VERSION_BACK} ${DOCKER_REPO}/${DOCKER_IMAGE_BACK}:${VERSION_BACK}"
                
                //sh "docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW"
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'DOCKERHUB_PSW', usernameVariable: 'DOCKERHUB_USR')]) {
                    
                    script {
                        sh "docker login -u ${DOCKERHUB_USR} -p ${DOCKERHUB_PSW}"
                    }
                }
                
                sh "docker push ${DOCKER_REPO}/${DOCKER_IMAGE_BACK}:${VERSION_BACK}"
                    
                sh "docker logout"
            }

            post{

                success{
                    echo "====++++success++++===="
                }

                failure{
                    echo "====++++failed++++===="
                }
            }
        }
        
        stage('Obtenir la version (Frontend) la plus récente ...') {
            steps {
                script {
                    try {
                        // tags existants
                        def tags = sh(script: "docker pull ${DOCKER_REPO}/${DOCKER_IMAGE_FRONT}:${VERSION_FRONT} && docker image ls --format='{{.Tag}}' ${DOCKER_REPO}/${DOCKER_IMAGE_FRONT}", returnStdout: true).trim()
                        echo "tags : ${tags}"
                        // tag list
                        //def tagList = tags.tokenize("\n")
                        //echo "tagList : ${tagList}"
                        // tag le plus récent
                        def latestTag = tags.tokenize("\n")[-1]
                        echo "latestTag : ${latestTag}"
                        // incrément version
                        if (latestTag != "") {
                            VERSION_FRONT = incrementVersion(latestTag)
                            echo "Version (Frontend) ${VERSION_FRONT}"
                        }
                    } catch (Exception e) {
                        echo "Version (Frontend) initiale."
                    }
                }
            }
        }

        stage('Création image Docker (Frontend) ...') { 
            steps {
                sh "docker build -t ${DOCKER_REPO}/${DOCKER_IMAGE_FRONT}:${VERSION_FRONT} ."
            }
        }
        
        stage('Tag et push image (Frontend) ...') {
            steps {
                echo "Push image (Frontend) version ${VERSION_FRONT} ..."

                sh "docker tag ${DOCKER_REPO}/${DOCKER_IMAGE_FRONT}:${VERSION_FRONT} ${DOCKER_REPO}/${DOCKER_IMAGE_FRONT}:${VERSION_FRONT}"
                
                //sh "docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW"
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'DOCKERHUB_PSW', usernameVariable: 'DOCKERHUB_USR')]) {
                    
                    script {
                        sh "docker login -u ${DOCKERHUB_USR} -p ${DOCKERHUB_PSW}"
                    }
                }
                
                sh "docker push ${DOCKER_REPO}/${DOCKER_IMAGE_FRONT}:${VERSION_FRONT}"
                    
                sh "docker logout"
            }

            post{

                success{
                    echo "====++++success++++===="
                }

                failure{
                    echo "====++++failed++++===="
                }
            }
        }

        stage('Lancement Stack Docker-Compose ...') { 
            steps {
                sh 'docker compose -f Docker-compose.yml up -d'
            }
        }
    }
}

def incrementVersion(version) {
    // numéro de version majeure, mineure et de révision
    def parts = version.tokenize('.')
    def major = parts[0] as Integer
    def minor = parts[1] as Integer
    def patch = parts[2] as Integer
    // incrément version majeure
    //major++
    //patch = 0
    // incrément version mineure
    //minor++
    //patch = 0
    // incrément version révision
    patch++
    
    return "${major}.${minor}.${patch}"
}
