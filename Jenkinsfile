node {
    checkout scm

	withCredentials([
		string(variable: 'CLOUDFLARE_ACCOUNT_ID', credentialsId: 'CLOUDFLARE_ACCOUNT_ID'),
		string(variable: 'CLOUDFLARE_API_TOKEN', credentialsId: 'CLOUDFLARE_API_TOKEN')
	]) {
		def image = docker.image('node:16-buster-slim')
		image.inside(
			"-p 3000:3000 " + 
			"-e 'CLOUDFLARE_ACCOUNT_ID=$CLOUDFLARE_ACCOUNT_ID' " + 
			"-e 'CLOUDFLARE_API_TOKEN=$CLOUDFLARE_API_TOKEN'"
		) {
			stage('Build') {
				sh 'npm install'
			}

			stage('Test') {
				sh './jenkins/scripts/test.sh'
			}

			stage('Manual Approval') {
				input 'Lanjutkan ke tahap Deploy?'
			}

			stage('Deploy') {
				sh './jenkins/scripts/deliver.sh'
				sh "npx wrangler pages deploy ./build --project-name='dicoding-submission2'"
				sleep 60
				sh './jenkins/scripts/kill.sh'
			}
		}
	}
}
