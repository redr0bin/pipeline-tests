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

        stage("Checkout") {
            sh "mkdir -p ${env.BUILD_DIR}"
            dir("${env.BUILD_DIR}") {
                checkout scm

                switch(env.BRANCH_NAME) {
                    case ~/PR/:
                        env.VERSION = "${env.BRANCH_NAME}"
                        break
                    case ~/develop/:
                        env.NAMESPACE = "nsb-dev"
                        env.TARGET_ENV = "dev"
                        break
                    case ~/master/:
                        env.NAMESPACE = "nsb-dev"
                        env.TARGET_ENV = "dev"
			env.VERSION = "${env.BRANCH_NAME}-1"
                        break
                    default:
                        break
                }
            }
        }

        withEnv(["GOPATH=${env.WORKSPACE}", "TAG=${env.VERSION}"]) {
            stage("TEST VAR") {
                container('build-slave') {
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
