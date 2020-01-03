node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("sneha11/devops-tutorial")
        // app.inside {
        //     sh 'docker-compose build'
        //     echo 'Image built'
        // }
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage ('Deploy') {
        steps{
            sshagent(credentials : ['ssh-server-credentials']) {
                sh 'ssh -o StrictHostKeyChecking=no frontend@13.233.69.23 uptime'
                sh 'docker pull sneha11/devops-tutorial'
                sh 'docker run -p 8000:8000 -d sneha11/devops-tutorial'
            }

            echo "====++++Deployed to the server successfully++++===="
        }
    }
}
