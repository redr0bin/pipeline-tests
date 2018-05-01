podTemplate(
    label: "build-pod",
    containers: [
        containerTemplate(name: 'build-slave', image: 'liflandgaming/build-slave:1.9.2', ttyEnabled: true, alwaysPullImage: false, command: 'cat'),
    ],
    volumes: [
        hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
    ]
) {
    node('build-pod') {
        env.BUILD_DIR = "${WORKSPACE}/src/github.com/LiflandGaming/platform"
        env.REGISTRY = "snapshots.do.optibet.ee"
        env.REGISTRY_CREDS = "snapshots-registry"
        env.TAG_NAME = "0.12.12"

        stage("Checkout") {
                sh "mkdir -p ${env.BUILD_DIR}"
                dir("${env.BUILD_DIR}") {
                    checkout scm

                    switch(env.TAG_NAME) {
                        case ~/PR.*/:
                            env.VERSION = "${env.BRANCH_NAME}"
                            break
                        case ~/develop/:
                            env.NAMESPACE = "nsb-dev"
                            env.TARGET_ENV = "dev"
                            break
                        case ~/^\d+.*/:
                            env.NAMESPACE = "nsb-dev"
                            env.TARGET_ENV = "dev"
			    env.VERSION = "${env.TAG_NAME}"
                            break
                        default:
                            break
                    }
                }
        }

        stage("TEST VAR") {
            container('build-slave') {
                withEnv(["GOPATH=${env.WORKSPACE}", "TAG=${env.VERSION}"]) {
                    dir("${env.BUILD_DIR}") {
                        ansiColor('xterm') {
                            sh "echo $TAG"
                            sh "env | sort"
                        }
                    }
                }
            }
        }
    }
}
