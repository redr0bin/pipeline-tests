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
        env.BUILD_DIR = "${WORKSPACE}/src/github.com/LiflandGaming/new-sports-book"
        env.REGISTRY = "snapshots.do.optibet.ee"
        env.REGISTRY_CREDS = "snapshots-registry"
        env.VERSION = "${BUILD_TIMESTAMP}"

        stage("Checkout") {
            sh "mkdir -p ${env.BUILD_DIR}"
            dir("${env.BUILD_DIR}") {
                checkout scm

                switch(env.BRANCH_NAME) {
                    case ~/PR/:
                        env.VERSION = "${env.GIT_BRANCH}"
                        break
                    case ~/task.*|bug.*/:
                        env.NAMESPACE = "nsb-test"
                        env.TARGET_ENV = "test"
                        break
                    case ~/develop/:
                        env.NAMESPACE = "nsb-dev"
                        env.TARGET_ENV = "dev"
                        break
                    case ~/master/:
                        env.REGISTRY="release.do.optibet.ee"
                        env.REGISTRY_CREDS = "release-registry"
                        env.NAMESPACE = "nsb-stage"
                        env.TARGET_ENV = "stage"
                        break
                    default:
                        break
                }
            }
        }

        //        withEnv(["REGISTRY=${env.REGISTRY}", "REGISTRY_CREDS=${env.REGISTRY_CREDS}", "TAG=${env.VERSION}", "NAMESPACE=${env.NAMESPACE}", "TARGET_ENV=${env.TARGET_ENV}", "BRANCH=${env.GIT_BRANCH}"]) {
        stage("Build") {
            container('build-slave') {
                sh "env | sort"
            }
        }
    }
    //  }
}