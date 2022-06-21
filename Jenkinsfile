def imageName = 'mlabouardy/movies-parser'
def registry = 'https://registry.slowcoder.com'

node('workers'){
    stage('Checkout'){
        checkout scm
    }

    def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")

    stage('Pre-integration Tests'){
        parallel(
            'Quality Tests': {
                imageTest.inside{
                    sh 'golint'
                }
            },
            'Unit Tests': {
                imageTest.inside('-u root:root'){
                    sh 'go test'
                }
            },
            'Security Tests': {
                imageTest.inside('-u root:root'){
                    
                }
            }
        )
    }

    stage('Build'){
      sh "docker build -t 1kper/movies-parser ."
    }

    stage('Push'){
       sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 635154829813.dkr.ecr.us-west-2.amazonaws.com"
        
        sh "docker tag 1kper/movies-parser:latest 635154829813.dkr.ecr.us-west-2.amazonaws.com/1kper/movies-parser:latest"
        sh "docker push 635154829813.dkr.ecr.us-west-2.amazonaws.com/1kper/movies-parser:latest"
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}
